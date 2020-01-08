---
title: 将 Azure Active Directory 用户同步到群集 - Azure HDInsight
description: 将通过身份验证的用户从 Azure Active Directory 同步到 HDInsight 群集。
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9767bccc754d5ccf8c0e7cbb66c8e9c3d5eaf7d0
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105369"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>将 Azure Active Directory 用户同步到 HDInsight 群集

[使用企业安全性套餐 (ESP) 的 HDInsight 群集](hdinsight-domain-joined-introduction.md)可对 Azure Active Directory (Azure AD) 用户使用强身份验证，还可使用基于角色的访问控制 (RBAC) 策略。 将用户和组添加到 Azure AD 时，可以同步需要访问群集的用户。

## <a name="prerequisites"></a>先决条件

如果你尚未执行此操作，请[使用企业安全性套餐创建 HDInsight 群集](hdinsight-domain-joined-configure.md)。

## <a name="add-new-azure-ad-users"></a>添加新的 Azure AD 用户

若要查看主机，请打开 Ambari Web UI。 每个节点都会使用新的无人参与升级设置进行更新。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到与 ESP 群集相关联的 Azure AD 目录。

2. 从左侧菜单选择“所有用户”，然后选择“新建用户”。

    ![Azure 门户用户和组](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. 完成新用户表单。 选择所创建的组，以便分配基于群集的权限。 在此示例中，请创建名为“HiveUsers”的组，以便向其分配新用户。 [示例说明](hdinsight-domain-joined-configure.md)介绍如何创建 ESP 群集，其中包括如何添加 `HiveUsers` 和 `AAD DC Administrators` 这两个组。

    ![Azure 门户用户窗格选择组](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. 选择“创建”。

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>使用 Apache Ambari REST API 来同步用户

在群集创建过程中指定的用户组是在创建时同步的。 用户同步每小时自动进行一次。 若要立即同步用户，或者要同步的组不是群集创建过程中指定的组，请使用 Ambari REST API。

以下方法通过 Ambari REST API 使用 POST。 有关详细信息，请参阅[使用 Apache Ambari REST API 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari-rest-api.md)。

1. [使用 SSH 连接到群集](hdinsight-hadoop-linux-use-ssh-unix.md)。 在 Azure 门户中，从群集的概览窗格选择“安全外壳(SSH)”按钮。

    ![HDInsight 安全外壳（SSH）图标](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-secure-shell.png)

2. 复制显示的 `ssh` 命令，将其粘贴到 SSH 客户端中。 系统提示时，输入 ssh 用户密码。

3. 进行身份验证后，输入以下命令：

    ```bash
    curl -u admin:<YOUR PASSWORD> -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events"
    ```
    
    响应应为如下所示：

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

4. 若要查看同步状态，执行一个新的 `curl` 命令：

    ```bash
    curl -u admin:<YOUR PASSWORD> https://<YOUR CLUSTER NAME>.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```
    
    响应应为如下所示：
    
    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

5. 此结果显示，状态为“完成”，创建了一个新用户且为该用户指定了成员身份。 在此示例中，用户分配到“HiveUsers”同步的 LDAP 组，因为用户已在 Azure AD 中添加到该组。

> [!NOTE]  
> 前一方法仅同步创建群集时在域设置的“访问用户组”属性中指定的 Azure AD 组。 有关详细信息，请参阅[创建 HDInsight 群集](domain-joined/apache-domain-joined-configure.md)。

## <a name="verify-the-newly-added-azure-ad-user"></a>验证新添加的 Azure AD 用户

打开 [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)，验证新的 Azure AD 用户是否已添加。 浏览到 **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** ，对 Ambari Web UI 进行访问。 输入群集管理员用户名和密码。

1. 在 Ambari 仪表板中，选择“管理”菜单下的“管理 Ambari”。

    ![Apache Ambari 仪表板管理 Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. 在页面左侧的“用户 + 组管理”菜单组下选择“用户”。

    ![HDInsight 用户和组菜单](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. 新用户应在“用户”表中列出。 “类型”设置为 `LDAP` 而非 `Local`。

    ![HDInsight aad 用户页面概述](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>作为新用户登录到 Ambari

新用户（或任何其他域用户）在登录到 Ambari 时，使用完整的 Azure AD 用户名和域凭据。  Ambari 显示用户别名，该别名是用户在 Azure AD 中的显示名称。 新示例用户的用户名为 `hiveuser3@contoso.com`。 在 Ambari 中，这个新用户显示为 `hiveuser3`，但该用户是作为 `hiveuser3@contoso.com` 登录到 Ambari 的。

## <a name="see-also"></a>请参阅

* [使用 ESP 在 HDInsight 中配置 Apache Hive 策略](hdinsight-domain-joined-run-hive.md)
* [管理 ESP HDInsight 群集](hdinsight-domain-joined-manage.md)
* [授权用户访问 Apache Ambari](hdinsight-authorize-users-to-ambari.md)
