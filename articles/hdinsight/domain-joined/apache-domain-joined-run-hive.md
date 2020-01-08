---
title: 在具有企业安全性套餐的 HDInsight 中配置 Hive 策略 - Azure
description: 了解如何为具有企业安全性套餐的 Azure HDInsight 服务中的 Hive 配置 Apache Ranger 策略。
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: b0213fc1a96b38b615cbd8b7b6374a6716b9f840
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918186"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>在具有企业安全性套餐的 HDInsight 中配置 Apache Hive 策略
了解如何为 Apache Hive 配置 Apache Ranger 策略。 本文将创建两个 Ranger 策略来限制对 hivesampletable 的访问。 HDInsight 群集附带 hivesampletable。 配置这些策略后，可以使用 Excel 和 ODBC 驱动程序连接到 HDInsight 中的 Hive 表。

## <a name="prerequisites"></a>先决条件
* 具有企业安全性套餐的 HDInsight 群集。 请参阅[配置具有 ESP 的 HDInsight 群集](apache-domain-joined-configure.md)。
* 装有 Office 2016、Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone 或 Office 2010 Professional Plus 的工作站。

## <a name="connect-to-apache-ranger-admin-ui"></a>连接到 Apache Ranger 管理 UI
**连接到 Ranger 管理 UI**

1. 通过浏览器连接到 Ranger 管理 UI。 URL 为 https://&lt;ClusterName>.azurehdinsight.net/Ranger/。

   > [!NOTE]  
   > Ranger 使用的凭据与 Apache Hadoop 群集不同。 若要防止浏览器使用缓存的 Hadoop 凭据，请使用新的 InPrivate 浏览器窗口连接到 Ranger 管理 UI。

2. 使用群集管理员域用户名和密码登录：

    ![HDInsight ESP Ranger 主页](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    目前，Ranger 只能处理 Yarn 和 Hive。

## <a name="create-domain-users"></a>创建域用户
请参阅[创建具有 ESP 的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)，了解如何创建 hiveruser1 和 hiveuser2。 本文中使用这两个用户帐户。

## <a name="create-ranger-policies"></a>创建 Ranger 策略
本部分将创建用于访问 hivesampletable 的两个 Ranger 策略。 将授予对不同列集的 select 权限。 这两个用户是在[创建具有 ESP 的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)的教程中创建的。 在下一部分，会在 Excel 中测试两个策略。

**创建 Ranger 策略**

1. 打开 Ranger 管理 UI。 请参阅“连接到 Apache Ranger 管理 UI”。
2. 单击“Hive”下的“&lt;群集名称>_hive”。 应会看到两个预配置策略。
3. 单击“添加新策略”，并输入以下值：

   * 策略名称：read-hivesampletable-all
   * Hive 数据库：default
   * 表：hivesampletable
   * Hive 列：*
   * 选择用户：hiveuser1
   * 权限：select

     ![HDInsight ESP Ranger Hive 策略配置](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]  
     > 如果“选择用户”中未填充域用户，请等待片刻时间让 Ranger 与 AAD 同步。
     >
     >
4. 单击“添加”保存策略。
5. 重复最后两个步骤，创建具有以下属性的另一个策略：

   * 策略名称：read-hivesampletable-devicemake
   * Hive 数据库：default
   * 表：hivesampletable
   * Hive 列：clientid, devicemake
   * 选择用户：hiveuser2
   * 权限：select

## <a name="create-hive-odbc-data-source"></a>创建 Hive ODBC 数据源
可以在 [Create Hive ODBC data source](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)（创建 Hive ODBC 数据源）中找到说明。  

 | 属性  |描述 |
 | --- | --- |
 | 数据源名称 | 为数据源提供名称 |
 | 主机 | 输入 &lt;HDInsightClusterName>.azurehdinsight.net。 例如，myHDICluster.azurehdinsight.net |
 | Port | 使用 **443**。 （此端口已从 563 更改为 443。） |
 | 数据库 | 使用“默认”。 |
 | Hive 服务器类型 | 选择“Hive Server 2” |
 | 机制 | 选择“Azure HDInsight 服务” |
 | HTTP 路径 | 将此字段留空。 |
 | 用户名 | 输入 hiveuser1@contoso158.onmicrosoft.com。 如果域名不同，请更新域名。 |
 | 密码 | 输入 hiveuser1 的密码。 |

在保存数据源之前，请务必单击“测试”。

## <a name="import-data-into-excel-from-hdinsight"></a>将数据从 HDInsight 导入 Excel
在最后一个部分中，已配置两个策略。  hiveuser1 对所有列拥有 select 权限，hiveuser2 对两个列拥有 select 权限。 在本部分中，会模拟两个用户将数据导入 Excel。

1. 在 Excel 中打开新工作簿或现有工作簿。
2. 在“数据”选项卡中，依次单击“从其他数据源”、“从数据连接向导”以启动“数据连接向导”。

    ![打开数据连接向导][img-hdi-simbahiveodbc.excel.dataconnection]
3. 选择“ODBC DSN”作为数据源，并单击“下一步”。
4. 从 ODBC 数据源中，选择在上一步中创建的数据源名称，并单击“下一步”。
5. 在向导中重新输入群集的密码，然后单击“确定”。 等待“选择数据库和表”对话框打开。 这可能需要几秒钟时间。
6. 选择 **hivesampletable**，并单击“下一步”。
7. 单击 **“完成”** 。
8. 在“导入数据”对话框中，可更改或指定查询。 为此，请单击“属性”。 这可能需要几秒钟时间。
9. 单击“定义”选项卡。命令文本为：

       SELECT * FROM "HIVE"."default"."hivesampletable"

   根据定义的 Ranger 策略，hiveuser1 对所有列拥有 select 权限。  因此，此查询可以使用 hiveuser1 的凭据，但不能使用 hiveuser2 的凭据。

   ![连接属性][img-hdi-simbahiveodbc-excel-connectionproperties]
10. 单击“确定”关闭“连接属性”对话框。
11. 单击“确定”关闭“导入数据”对话框。  
12. 重新输入 hiveuser1 的密码，并单击“确定”。 需要几秒钟时间才能将数据导入到 Excel 中。 完成后，应会看到 11 个数据列。

测试在最后一个部分中创建的第二个策略 (read-hivesampletable-devicemake)

1. 在 Excel 中添加一个新工作表。
2. 遵循上一过程导入数据。  所做的唯一更改是使用 hiveuser2 的凭据，而不是 hiveuser1 的凭据。 此查询失败，因为 hiveuser2 仅有权查看两个列。 此时会出现以下错误：

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. 请遵循相同的过程导入数据。 这次使用 hiveuser2 的凭据，并且还修改 select 语句，从：

        SELECT * FROM "HIVE"."default"."hivesampletable"

    修改为：

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    完成后，应会看到导入的两个数据列。

## <a name="next-steps"></a>后续步骤
* 有关使用企业安全性套餐配置 HDInsight 群集的信息，请参阅[使用 ESP 配置 HDInsight 群集](apache-domain-joined-configure.md)。
* 有关管理具有 ESP 的 HDInsight 群集的信息，请参阅[管理具有 ESP 的 HDInsight 群集](apache-domain-joined-manage.md)。
* 有关在具有 ESP 的 HDInsight 群集上使用 SSH 运行 Hive 查询，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。
* 若要使用 Hive JDBC 连接 Hive，请参阅 [Connect to Apache Hive on Azure HDInsight using the Hive JDBC driver](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)（使用 Hive JDBC 驱动程序连接到 Azure HDInsight 上的 Apache Hive）
* 若要使用 Hive ODBC 将 Excel 连接到 Hadoop，请参阅 [Connect Excel to Apache Hadoop with the Microsoft Hive ODBC drive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)（使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Apache Hadoop）
* 若要使用 Power Query 将 Excel 连接到 Hadoop，请参阅 [Connect Excel to Apache Hadoop by using Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)（使用 Power Query 将 Excel 连接到 Apache Hadoop）
