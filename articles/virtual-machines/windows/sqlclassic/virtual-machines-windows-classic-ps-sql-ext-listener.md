---
title: 为 Always On 可用性组配置外部侦听器 | Microsoft Docs
description: 本教程逐步说明如何在 Azure 中创建一个可以使用关联云服务公共虚拟 IP 地址从外部访问的 AlwaysOn 可用性组侦听器。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 78881830d4e558daaad6e1929b30287e2731fb1b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100414"
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>在 Azure 中配置 AlwaysOn 可用性组的外部侦听器
> [!div class="op_single_selector"]
> * [内部侦听器](../classic/ps-sql-int-listener.md)
> * [外部侦听器](../classic/ps-sql-ext-listener.md)
> 
> 

本主题说明如何为 AlwaysOn 可用性组配置一个可以通过 Internet 从外部访问的侦听器。 这通过将云服务的**公共虚拟 IP (VIP)** 地址与侦听器关联来实现。

> [!IMPORTANT] 
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

可用性组可以仅包含本地副本或 Azure 副本，也可以跨越本地和 Azure 以实现混合配置。 Azure 副本可以位于同一区域，也可以跨越使用多个虚拟网络 (VNet) 的多个区域。 下面的步骤假设已经[配置可用性组](../classic/portal-sql-alwayson-availability-groups.md)，但未配置侦听器。

## <a name="guidelines-and-limitations-for-external-listeners"></a>外部侦听器的准则和限制
使用云服务的公共 VIP 地址部署时，请注意有关 Azure 中可用性组侦听器的以下准则：

* Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2 支持可用性组侦听器。
* 客户端应用程序必须位于不包含可用性组 VM 的云服务中。 Azure 不支持客户端和服务器位于同一个云服务中的直接服务器返回。
* 默认情况下，本文中的步骤说明如何将一个侦听器配置为使用云服务虚拟 IP (VIP) 地址。 但是，可以为云服务保留和创建多个 VIP 地址。 这样就可以使用本文中的步骤创建多个侦听器，每个侦听器与不同的 VIP 相关联。 有关如何创建多个 VIP 地址的信息，请参阅[每个云服务具有多个 VIP](../../../load-balancer/load-balancer-multivip.md)。
* 如果要为混合环境创建侦听器，则本地网络必须连接到公共 Internet，还通过 Azure 虚拟网络连接到站点到站点 VPN。 位于 Azure 子网中时，只能通过相应云服务的公共 IP 地址来访问该可用性组侦听器。
* 不支持在你在其中也有使用内部负载均衡器 (ILB) 的内部侦听器的同一云服务中创建外部侦听器。

## <a name="determine-the-accessibility-of-the-listener"></a>确定侦听器的可访问性
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

本文重点介绍如何创建使用**外部负载均衡**的侦听器。 如果要创建专用于虚拟网络的侦听器，请参阅本文的另一个版本，其中提供了设置[使用 ILB 的侦听器](../classic/ps-sql-int-listener.md)的步骤

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>创建支持直接服务器返回的负载均衡 VM 终结点
外部负载均衡使用托管 VM 的云服务的公共虚拟 IP 地址。 因此，在这种情况下，不需要创建或配置负载均衡器。

必须为每个托管 Azure 副本的 VM 创建一个负载均衡的终结点。 如果在多个区域中拥有副本，该区域的每个副本必须位于同一个 VNet 的同一个云服务中。 跨越多个 Azure 区域创建可用性组副本需要配置多个 Vnet。 有关配置跨 VNet 连接的详细信息，请参阅[配置 VNet 到 VNet 连接](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

1. 在 Azure 门户中，导航到托管副本的每个 VM 并查看详细信息。
2. 单击每个 VM 的“终结点”选项卡。
3. 验证想要使用的侦听器终结点“名称”和“公用端口”是否已被使用。 在下面的示例中，名称为“MyEndpoint”，端口为“1433”。
4. 在本地客户端上，下载并安装[最新的 PowerShell 模块](https://azure.microsoft.com/downloads/)。
5. 启动 **Azure PowerShell**。 随即打开新的 PowerShell 会话，其中加载了 Azure 管理模块。
6. 运行 **Get-AzurePublishSettingsFile**。 此 cmdlet 会定向到浏览器，以将发布设置文件下载到本地目录。 系统可能会提示输入 Azure 订阅的登录凭据。
7. 结合下载的发布设置文件的路径运行 **Import-AzurePublishSettingsFile** 命令：
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    导入发布设置文件后，便可以在 PowerShell 会话中管理 Azure 订阅。
    
1. 将以下 PowerShell 脚本复制到文本编辑器中，并根据你的环境设置变量值（这里为某些参数提供了默认值）。 请注意，如果可用性组跨多个 Azure 区域，则必须在每个数据中心内为云服务以及位于其中的节点运行该脚本一次。
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. 设置变量后，将脚本从文本编辑器复制到 Azure PowerShell 会话中运行。 如果提示符仍然显示 >>，请再次按 Enter，以确保脚本开始运行。

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>如果需要，请验证是否已安装 KB2854082
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>在可用性组节点中打开防火墙端口
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>创建可用性组侦听器

通过两个步骤创建可用性组侦听器。 首先，创建客户端接入点的群集资源，并配置依赖关系。 其次，使用 PowerShell 配置群集资源。

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>创建客户端接入点并配置群集依赖关系
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>在 PowerShell 中配置群集资源
1. 对于外部负载均衡，你必须获取包含副本的云服务的公共虚拟 IP 地址。 登录到 Azure 门户。 导航到包含你的可用性组 VM 的云服务。 打开“仪表板”视图。
2. 记下“公共虚拟 IP (VIP)地址”下显示的地址。 如果解决方案跨 VNet，请针对包含副本所在 VM 的每个云服务重复此步骤。
3. 在某个 VM 上，将以下 PowerShell 脚本复制到文本编辑器中，将变量设置为之前记下的值。
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. 设置变量之后，打开提升的 Windows PowerShell 窗口，然后从文本编辑器复制脚本，并将其粘贴到 Azure PowerShell 会话中运行。 如果提示符仍然显示 >>，请再次按 Enter，以确保脚本开始运行。
5. 在每个 VM 上重复此过程。 此脚本将使用云服务的 IP 地址来配置 IP 地址资源，同时设置探测端口等其他参数。 在 IP 地址资源联机后，它可以响应我们在本教程前面部分创建的负载均衡终结点在探测端口上的轮询。

## <a name="bring-the-listener-online"></a>使侦听器联机
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>后续操作项
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>测试可用性组侦听器（在同一 VNet 中）
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>测试可用性组侦听器（通过 Internet）
若要从虚拟网络外部访问侦听器，必须使用外部/公共负载均衡（如本主题中所述）而不是 ILB，因为 ILB 只能在同一 VNet 中进行访问。 在连接字符串中指定云服务名称。 例如，如果云服务名为 *mycloudservice*，则 sqlcmd 语句将如下所示：

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

与前面的示例不同，现在必须使用 SQL 身份验证，因为调用方无法通过 Internet 使用 Windows 身份验证。 有关详细信息，请参阅 [Azure VM 中的 Always On 可用性组：客户端连接方案](https://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)。 使用 SQL 身份验证时，请确保在两个副本上创建相同的登录名。 有关排查可用性组登录问题的详细信息，请参阅[如何映射登录名或使用包含的 SQL 数据库用户连接到其他副本并映射到可用性数据库](https://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx)。

如果 Always On 副本位于不同子网中，客户端必须在连接字符串中指定 **MultisubnetFailover=True**。 这会导致尝试并行连接到不同子网中的副本。 请注意，这种情况下包括跨区域 AlwaysOn 可用性组部署。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

