---
title: 授权用户访问 Ambari 视图 - Azure HDInsight
description: 如何在启用 ESP 的情况下管理 HDInsight 群集的 Ambari 用户和组权限。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 8fada1d944a3d6bb6c0f85b3fd456581b2b0bdc6
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720012"
---
# <a name="authorize-users-for-apache-ambari-views"></a>授权用户访问 Apache Ambari 视图

[支持企业安全性套餐 (ESP) 的 HDInsight 群集](./domain-joined/hdinsight-security-overview.md)提供了企业级功能，包括基于 Azure Active Directory 的身份验证。 可以将已添加的[新用户同步](hdinsight-sync-aad-users-to-cluster.md)到已被授予群集访问权限的 Azure AD 组，从而允许这些特定用户执行某些操作。 ESP HDInsight 群集和标准 HDInsight 群集均支持使用 [Apache Ambari](https://ambari.apache.org/) 中的用户、组和权限。

Active Directory 用户可以使用其域凭据登录到群集节点。 他们还可以使用自己的域凭据在其他已批准的终结点（例如 [Hue](https://gethue.com/)、Ambari 视图、ODBC、JDBC、PowerShell 和 REST API）上进行身份验证，以便与群集交互。

> [!WARNING]  
> 不要在基于 Linux 的 HDInsight 群集上更改 Ambari 监视程序 (hdinsightwatchdog) 的密码。 更改密码将导致无法通过群集使用脚本操作或执行缩放操作。

如果尚未如此操作，请按照[这些说明](./domain-joined/apache-domain-joined-configure.md)预配新的 ESP 群集。

## <a name="access-the-ambari-management-page"></a>访问 Ambari 管理页

要访问 [Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) 上的 Ambari 管理页面，请浏览到 **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`** 。 输入创建群集时定义的群集管理员用户名和密码。 接下来，在 Ambari 仪表板中，选择“管理”菜单下面的“管理 Ambari”：

![Apache Ambari 仪表板管理](./media/hdinsight-authorize-users-to-ambari/manage-apache-ambari.png)

## <a name="add-users"></a>添加用户

### <a name="add-users-through-the-portal"></a>通过门户添加用户

1. 从 "管理" 页中选择 "**用户**"。

    ![Apache Ambari 管理页用户](./media/hdinsight-authorize-users-to-ambari/apache-ambari-management-page-users.png)

1. 选择 " **+ 创建本地用户**"。

1. 提供**用户名**和**密码**。 选择 "**保存**"。

### <a name="add-users-through-powershell"></a>通过 PowerShell 添加用户

使用适当的值替换 `CLUSTERNAME`，`NEWUSER`，并 `PASSWORD` 来编辑下面的变量。

```powershell
# Set-ExecutionPolicy Unrestricted

# Begin user input; update values
$clusterName="CLUSTERNAME"
$user="NEWUSER"
$userpass='PASSWORD'
# End user input

$adminCredentials = Get-Credential -UserName "admin" -Message "Enter admin password"

$clusterName = $clusterName.ToLower()
$createUserUrl="https://$($clusterName).azurehdinsight.net/api/v1/users"

$createUserBody=@{
    "Users/user_name" = "$user"
    "Users/password" = "$userpass"
    "Users/active" = "$true"
    "Users/admin" = "$false"
} | ConvertTo-Json

# Create user
$statusCode =
Invoke-WebRequest `
    -Uri $createUserUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $createUserBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output "User is created: $user"
}
else
{
    Write-Output 'User is not created'
    Exit
}

$grantPrivilegeUrl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/privileges"

$grantPrivilegeBody=@{
    "PrivilegeInfo" = @{
        "permission_name" = "CLUSTER.USER"
        "principal_name" = "$user"
        "principal_type" = "USER"
    }
} | ConvertTo-Json

# Grant privileges
$statusCode =
Invoke-WebRequest `
    -Uri $grantPrivilegeUrl `
    -Credential $adminCredentials `
    -Method POST `
    -Headers @{"X-Requested-By" = "ambari"} `
    -Body $grantPrivilegeBody | Select-Object -Expand StatusCode

if ($statusCode -eq 201) {
    Write-Output 'Privilege is granted'
}
else
{
    Write-Output 'Privilege is not granted'
    Exit
}

Write-Host "Pausing for 100 seconds"
Start-Sleep -s 100

$userCredentials = "$($user):$($userpass)"
$encodedUserCredentials = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($userCredentials))
$zookeeperUrlHeaders = @{ Authorization = "Basic $encodedUserCredentials" }
$getZookeeperurl="https://$($clusterName).azurehdinsight.net/api/v1/clusters/$($clusterName)/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"

# Perform query with new user
$zookeeperHosts =
Invoke-WebRequest `
    -Uri $getZookeeperurl `
    -Method Get `
    -Headers $zookeeperUrlHeaders

Write-Output $zookeeperHosts
```

### <a name="add-users-through-curl"></a>通过卷添加用户

使用适当的值替换 `CLUSTERNAME`，`ADMINPASSWORD`，`NEWUSER`，并 `USERPASSWORD` 来编辑下面的变量。 此脚本设计为与 bash 一起执行。 在 Windows 命令提示符下，需要进行少许修改。

```bash
export clusterName="CLUSTERNAME"
export adminPassword='ADMINPASSWORD'
export user="NEWUSER"
export userPassword='USERPASSWORD'

# create user
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d "{\"Users/user_name\":\"$user\",\"Users/password\":\"$userPassword\",\"Users/active\":\"true\",\"Users/admin\":\"false\"}" \
https://$clusterName.azurehdinsight.net/api/v1/users

echo "user created: $user"

# grant permissions
curl -k -u admin:$adminPassword -H "X-Requested-By: ambari" -X POST \
-d '[{"PrivilegeInfo":{"permission_name":"CLUSTER.USER","principal_name":"'$user'","principal_type":"USER"}}]' \
https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/privileges

echo "Privilege is granted"

echo "Pausing for 100 seconds"
sleep 10s

# perform query using new user account
curl -k -u $user:$userPassword -H "X-Requested-By: ambari" \
-X GET "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER"
```

## <a name="grant-permissions-to-apache-hive-views"></a>授予对 Apache Hive 视图的权限

Ambari 随附 [Apache Hive](https://hive.apache.org/) 和 [Apache TEZ](https://tez.apache.org/) 等服务的视图实例。 若要授予对一个或多个 Hive 视图实例的访问权限，请转到 **Ambari 管理页**。

1. 在管理页中，选择左侧“视图”菜单标题下面的“视图”链接。

    ![Apache Ambari 视图视图链接](./media/hdinsight-authorize-users-to-ambari/apache-ambari-views-link.png)

2. 在“视图”页中，展开“HIVE”行。 有一个默认的 Hive 视图，它是在将 Hive 服务添加到群集时创建的。 还可以根据需要创建更多的 Hive 视图实例。 选择一个 Hive 视图：

    ![HDInsight 视图-Apache Hive 视图](./media/hdinsight-authorize-users-to-ambari/views-apache-hive-view.png)

3. 滚动到“视图”页的底部。 在“权限”部分下面，可使用两个选项向域用户授予对该视图的权限：

**向这些用户授予权限**![向这些用户授予权限](./media/hdinsight-authorize-users-to-ambari/hdi-add-user-to-view.png)

**向这些组授予权限**![向这些组授予权限](./media/hdinsight-authorize-users-to-ambari/add-group-to-view-permission.png)

1. 若要添加用户，请选择“添加用户”按钮。

   * 开始键入用户名，随后会看到以前定义的名称的下拉列表。

     ![Apache Ambari 用户自动完成](./media/hdinsight-authorize-users-to-ambari/ambari-user-autocomplete.png)

   * 选择或完成键入用户名。 若要将此用户名添加为新用户，请选择“新建”按钮。

   * 若要保存更改，请选中**蓝色复选框**。

     ![Apache Ambari 授予用户权限](./media/hdinsight-authorize-users-to-ambari/user-entered-permissions.png)

1. 若要添加组，请选择“添加组”按钮。

   * 开始键入组名称。 选择现有组名称或添加新组的过程与添加用户的过程相同。
   * 若要保存更改，请选中**蓝色复选框**。

     ![Apache Ambari grant 权限](./media/hdinsight-authorize-users-to-ambari/ambari-group-entered.png)

若要向某个用户分配该视图的使用权限，但不希望该用户成为拥有其他权限的组的成员，那么，将用户直接添加到视图的做法就很有效。 若要降低管理开销，向组分配权限的做法可能更简便。

## <a name="grant-permissions-to-apache-tez-views"></a>授予对 Apache TEZ 视图的权限

[Apache TEZ](https://tez.apache.org/) 视图实例可让用户监视和调试由 [Apache Hive](https://hive.apache.org/) 查询和 [Apache Pig](https://pig.apache.org/) 脚本提交的所有 Tez 作业。 有一个默认的 Tez 视图实例，它是预配群集时创建的。

若要将用户和组分配到 Tez 视图实例，请如前所述，展开“视图”页上的“TEZ”行。

![HDInsight 视图-Apache Tez 视图](./media/hdinsight-authorize-users-to-ambari/views-apache-tez-view.png)

若要添加用户或组，请重复上一部分中的步骤 3 - 5。

## <a name="assign-users-to-roles"></a>将用户分配到角色

用户和组有五个安全角色，下面按访问权限的降序列出了这些角色：

* 群集管理员
* 群集操作员
* 服务管理员
* 服务操作员
* 群集用户

若要管理角色，请转到 **Ambari 管理页**，在左侧的“群集”菜单组中选择“角色”链接。

![Apache Ambari 角色菜单链接](./media/hdinsight-authorize-users-to-ambari/cluster-roles-menu-link.png)

若要查看授予每个角色的权限列表，请单击“角色”页上“角色”表标题旁边的蓝色问号。

![Apache Ambari 角色菜单链接权限](./media/hdinsight-authorize-users-to-ambari/roles-menu-permissions.png "Apache Ambari 角色菜单链接权限")

在此页上，有两个可用于管理用户角色和组角色的不同视图：“块”和“列表”。

### <a name="block-view"></a>“块”视图

“块”独行显示每个角色，提供前面所述的“向这些用户分配角色”和“向这些组分配角色”选项。

![Apache Ambari 角色阻止视图](./media/hdinsight-authorize-users-to-ambari/ambari-roles-block-view.png)

### <a name="list-view"></a>列表视图

“列表”视图提供两种类别的快速编辑功能：“用户”和“组”。

* “列表”视图的“用户”类别显示所有用户的列表，可让我们在下拉列表中选择每个用户的角色。

    ![Apache Ambari 角色列表视图-用户](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  “列表”视图的“组”类别显示所有组，以及分配给每个组的角色。 在本示例中，组列表已从群集“域”设置的“访问用户组”属性中指定的 Azure AD 组同步。 请参阅[在启用了 ESP 的情况下创建 HDInsight 群集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp)。

    ![Apache Ambari 角色列表视图-组](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    在上图中，为“hiveusers”组分配了“群集用户”角色。 这是一个只读的角色，允许该组的用户查看但不允许更改服务配置和群集指标。

## <a name="log-in-to-ambari-as-a-view-only-user"></a>以仅限查看用户的身份登录到 Ambari

我们已向 Azure AD 域用户“hiveuser1”分配了访问 Hive 和 Tez 视图的权限。 当我们启动 Ambari Web UI 并输入该用户的域凭据（电子邮件格式的 Azure AD 用户名，以及密码）时，该用户会重定向到 Ambari 的“视图”页。 在此页中，该用户可以选择任何可访问的视图。 该用户无法访问站点的其他任何部分，包括仪表板、服务、主机、警报或管理页。

![仅具有视图的 Apache Ambari 用户](./media/hdinsight-authorize-users-to-ambari/ambari-user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>以群集用户的身份登录到 Ambari

我们已将 Azure AD 域用户“hiveuser2”分配到“群集用户”角色。 此角色有权访问仪表板和所有菜单项。 群集用户有权使用的选项比管理员要少。 例如，hiveuser2 可以查看每个服务的配置，但不能编辑这些配置。

![Apache Ambari 仪表板显示](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>后续步骤

* [使用 ESP 在 HDInsight 中配置 Apache Hive 策略](./domain-joined/apache-domain-joined-run-hive.md)
* [管理 ESP HDInsight 群集](./domain-joined/apache-domain-joined-manage.md)
* [在 HDInsight 中将 Apache Hive 视图与 Apache Hadoop 配合使用](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [将 Azure AD 用户同步到群集](hdinsight-sync-aad-users-to-cluster.md)
* [使用 Apache Ambari REST API 管理 HDInsight 群集](./hdinsight-hadoop-manage-ambari-rest-api.md)
