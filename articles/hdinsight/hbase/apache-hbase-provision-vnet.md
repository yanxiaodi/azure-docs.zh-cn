---
title: 在虚拟网络中创建 HBase 群集 - Azure
description: 开始在 Azure HDInsight 中使用 HBase。 了解如何在 Azure 虚拟网络上创建 HDInsight HBase 群集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 60a7afb6e610294ccaa535eaa7371ff8d5015db3
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077205"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>在 Azure 虚拟网络中的 HDInsight 上创建 Apache HBase 群集
了解如何在[Azure 虚拟网络][1]中创建 Azure HDInsight Apache HBase 群集。

通过虚拟网络集成，可以将 Apache HBase 群集部署到应用程序所在的虚拟网络，以便应用程序直接与 HBase 进行通信。 优点包括：

* 将 Web 应用程序直接连接到 HBase 群集节点，通过 HBase Java 远程过程调用 (RPC) API 实现通信。
* 提高性能，因为流量不必通过多个网关和负载均衡器。
* 能够以更安全的方式处理敏感信息，而无需公开公共终结点。

### <a name="prerequisites"></a>先决条件
在开始阅读本文前，必须具有以下项目：

* **Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **配备 Azure PowerShell 的工作站**。 请参阅 [Install and use Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)（安装并使用 Azure PowerShell）。

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>在虚拟网络中创建 Apache HBase 群集
在本部分中，通过 [Azure 资源管理器模板](../../azure-resource-manager/resource-group-template-deploy.md)在 Azure 虚拟网络中使用从属 Azure 存储帐户创建基于 Linux 的 Apache HBase 群集。 对于其他群集创建方法以及了解设置，请参阅[创建 HDInsight 群集](../hdinsight-hadoop-provision-linux-clusters.md)。 有关使用模板在 HDInsight 中创建 Apache Hadoop 群集的详细信息，请参阅[使用 Azure 资源管理器模板在 HDInsight 中创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> 某些属性已在模板中硬编码。 例如：
>
> * **位置**：美国东部 2
> * **群集版本**：3.6
> * **群集工作节点计数**：2
> * **默认存储帐户**：唯一字符串
> * **虚拟网络名称**：&lt;群集名称>-vnet
> * **虚拟网络地址空间**：10.0.0.0/16
> * **子网名称**：subnet1
> * **子网地址范围**：10.0.0.0/24
>
> &lt;群集名称> 会替换为使用模板时提供的群集名称。

1. 单击下面的图像即可在 Azure 门户中打开该模板。 该模板位于 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)中。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. 在“自定义部署”边栏选项卡中输入以下属性：

   * **订阅**：选择用来创建 HDInsight 群集的 Azure 订阅、相关存储帐户和 Azure 虚拟网络。
   * **资源组**：选择“新建”，并指定新的资源组名称。
   * **位置**：选择资源组的位置。
   * **ClusterName**：为要创建的 Hadoop 群集输入名称。
   * **群集登录名和密码**：默认登录名为“admin”。
   * **SSH 用户名和密码**：默认用户名为“sshuser”。  可以重命名它。
   * **我同意上述条款和条件**：（选择）
3. 单击“购买”。 创建群集大约需要 20 分钟时间。 创建群集之后，便可以在门户中单击群集边栏选项卡以打开它。

完成文章后，你可能想要删除群集。 有了 HDInsight，便可以将数据存储在 Azure 存储中，因此可以在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。 有关删除群集的说明，请参阅[使用 Azure 门户在 HDInsight 中管理 Apache Hadoop 群集](../hdinsight-administer-use-portal-linux.md#delete-clusters)。

要开始处理新 HBase 群集，可以按照[开始在 HDInsight 中将 Apache HBase 与 Apache Hadoop 配合使用](./apache-hbase-tutorial-get-started-linux.md)中的步骤进行操作。

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>使用 Apache HBase Java RPC API 连接到 Apache HBase 群集。
1. 将基础结构即服务 (IaaS) 虚拟机创建到相同的 Azure 虚拟网络和子网中。 有关创建新 IaaS 虚拟机的说明，请参阅[创建运行 Windows Server 的虚拟机](../../virtual-machines/windows/quick-create-portal.md)。 按照本文档中的步骤进行操作时，必须对网络配置使用以下值：

   * **虚拟网络**：&lt;群集名称>-vnet
   * **子网**：subnet1

   > [!IMPORTANT]  
   > 将 &lt;群集名称> 替换为在先前步骤中创建 HDInsight 群集时使用的名称。

   使用这些值可将虚拟机放置在与 HDInsight 群集相同的虚拟网络和子网中。 此配置让它们能够直接相互通信。 有一种方法可使用空的边缘节点创建 HDInsight 群集。 该边缘节点可用于管理群集。  有关详细信息，请参阅[在 HDInsight 中使用空边缘节点](../hdinsight-apps-use-edge-node.md)。

2. 使用 Java 应用程序远程连接到 HBase 时，必须使用完全限定的域名 (FQDN)。 要确定这一点，必须获取 HBase 群集的连接特定的 DNS 后缀。 为此，可以使用以下方法之一：

   * 使用 Web 浏览器进行 [Apache Ambari](https://ambari.apache.org/) 调用：

     浏览到 https://&lt;ClusterName>.azurehdinsight.net/api/v1/clusters/&lt;ClusterName>/hosts?minimal_response=true。 随后将返回带有 DNS 后缀的 JSON 文件。
   * 使用 Ambari 网站：

     1. 浏览到 https://&lt;ClusterName>.azurehdinsight.net。
     2. 在顶部菜单中单击“主机”。
   * 使用 Curl 发出 REST 调用：

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     在返回的 JavaScript 对象表示法 (JSON) 数据中，找到“host_name”条目。 此条目包含群集中的节点的 FQDN。 例如：

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     以群集名称开头的域名的部分是 DNS 后缀。 例如，mycluster.b1.cloudapp.net。
   * 使用 Azure PowerShell

     使用以下 Azure PowerShell 脚本注册 **Get-ClusterDetail** 函数，该函数可用于返回 DNS 后缀：

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     运行 Azure PowerShell 脚本后，使用以下命令通过 **Get-ClusterDetail** 函数来返回 DNS 后缀。 使用此命令时，指定 HDInsight HBase 群集名称、管理员名称和管理员密码。

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     此命令返回 DNS 后缀。 例如 **yourclustername.b4.internal.cloudapp.net**。


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

若要验证虚拟机是否可与 HBase 群集进行通信，请从虚拟机使用 `ping headnode0.<dns suffix>` 命令。 例如，ping headnode0.mycluster.b1.cloudapp.net。

要在 Java 应用程序中使用此信息，可以按照[使用 Apache Maven 构建将 Apache HBase 与 HDInsight (Hadoop) 配合使用的 Java 应用程序](./apache-hbase-build-java-maven-linux.md)中的步骤来创建应用程序。 若要让应用程序连接到远程 HBase 服务器，请修改本示例中的 **hbase-site.xml** 文件，以对 Zookeeper 使用 FQDN。 例如：

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> 有关 Azure 虚拟网络中的名称解析的详细信息，包括如何使用自己的 DNS 服务器，请参阅[名称解析 (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

## <a name="next-steps"></a>后续步骤
本文介绍了如何创建 Apache HBase 群集。 若要了解更多信息，请参阅以下文章：

* [HDInsight 入门](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中使用空边缘节点](../hdinsight-apps-use-edge-node.md)
* [在 HDInsight 中配置 Apache HBase 复制](apache-hbase-replication.md)
* [在 HDInsight 中创建 Apache Hadoop 群集](../hdinsight-hadoop-provision-linux-clusters.md)
* [开始在 HDInsight 中将 Apache HBase 与 Apache Hadoop 配合使用](./apache-hbase-tutorial-get-started-linux.md)
* [虚拟网络概述](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

