---
title: 在 Azure 中配置 Always On 可用性组的 ILB 侦听器 | Microsoft Docs
description: 本教程使用通过经典部署模型创建的资源，并使用内部负载均衡器在 Azure 中创建 Always On 可用性组侦听器。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mikeray
ms.openlocfilehash: ca8adf4f9ce221533240e6c797f1fb01dacf6e8d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101908"
---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>在 Azure 中配置 Always On 可用性组的 ILB 侦听器
> [!div class="op_single_selector"]
> * [内部侦听器](../classic/ps-sql-int-listener.md)
> * [外部侦听器](../classic/ps-sql-ext-listener.md)
>
>

## <a name="overview"></a>概述

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同部署模型：[Azure 资源管理器部署模型和经典部署模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍经典部署模型的用法。 我们建议在大多数新部署中使用 Resource Manager 模型。

若要在 Resource Manager 模型中配置 Always On 可用性组的侦听器，请参阅[在 Azure 中配置 Always On 可用性组的负载均衡器](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md)。

可用性组可以仅包含本地副本或 Azure 副本，也可以跨越本地和 Azure 以实现混合配置。 Azure 副本可以位于同一区域，也可以跨越使用多个虚拟网络的多个区域。 本文中的过程假设已[配置可用性组](../classic/portal-sql-alwayson-availability-groups.md)，但未配置侦听器。

## <a name="guidelines-and-limitations-for-internal-listeners"></a>内部侦听器的准则和限制
在 Azure 中使用内部负载均衡器 (ILB) 的可用性组侦听器遵循以下准则：

* Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2 支持可用性组侦听器。
* 每个云服务只支持一个内部可用性组侦听程序，因为该侦听程序将配置给 ILB，而每个云服务只有一个 ILB。 但是，可以创建多个外部侦听器。 有关详细信息，请参阅[在 Azure 中配置 Always On 可用性组的外部侦听器](../classic/ps-sql-ext-listener.md)。

## <a name="determine-the-accessibility-of-the-listener"></a>确定侦听器的可访问性
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

本文重点介绍如何创建使用 ILB 的侦听器。 如果需要一个公共或外部侦听器，请参阅本文的另一个版本，其中介绍了如何设置[外部侦听器](../classic/ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>创建支持直接服务器返回的负载均衡 VM 终结点
首先通过运行本部分稍后的脚本创建 ILB。

为每个托管 Azure 副本的 VM 创建一个负载均衡的终结点。 如果在多个区域中拥有副本，该区域的每个副本必须位于同一个 Azure 虚拟网络的同一个云服务中。 跨越多个 Azure 区域创建可用性组副本需要配置多个虚拟网络。 有关配置虚拟网络连接的详细信息，请参阅[配置虚拟网络到虚拟网络连接](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。

1. 在 Azure 门户中，转到每个托管副本的 VM 查看详细信息。

2. 单击每个 VM 的“终结点”选项卡。

3. 验证想要使用的侦听器终结点“名称”和“公用端口”是否已被使用。 在本部分的示例中，名称为“MyEndpoint”，端口为“1433”。

4. 在本地客户端上，下载并安装[最新的 PowerShell 模块](https://azure.microsoft.com/downloads/)。

5. 启动 Azure PowerShell。  
    将打开新的 PowerShell 会话，其中加载了 Azure 管理模块。

6. 运行 `Get-AzurePublishSettingsFile`。 此 cmdlet 将你定向到浏览器，以将发布设置文件下载到本地目录。 系统可能会提示输入 Azure 订阅的登录凭据。

7. 结合下载的发布设置文件的路径运行以下 `Import-AzurePublishSettingsFile` 命令：

        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

    导入发布设置文件后，便可以在 PowerShell 会话中管理 Azure 订阅。

8. 对于 *ILB*，请分配一个静态 IP 地址。 运行以下命令检查当前虚拟网络配置：

        (Get-AzureVNetConfig).XMLConfiguration
9. 记下包含副本所在 VM 的子网的 *Subnet* 名称。 脚本中的 $SubnetName 参数将要使用此名称。

10. 记下包含副本所在 VM 的子网的 *VirtualNetworkSite* 名称和起始 *AddressPrefix*。 再通过将这两个值传递给 `Test-AzureStaticVNetIP` 命令并检查 AvailableAddresses 来查找可用的 IP 地址。 例如，如果虚拟网络名为 MyVNet，并包含从 172.16.0.128 开始的子网地址范围，则以下命令将列出可用地址：

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
11. 选择一个可用地址，并将其用于下一步骤中脚本的 $ILBStaticIP 参数。

12. 将以下 PowerShell 脚本复制到文本编辑器，并根据环境设置变量值。 已经为某些参数提供了默认值。  

    使用地缘组的现有部署不能添加 ILB。 有关 ILB 要求的详细信息，请参阅[内部负载均衡器概述](../../../load-balancer/load-balancer-internal-overview.md)。

    此外，如果可用性组跨 Azure 区域，则必须在每个数据中心内对其中的云服务和节点运行该脚本一次。

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the virtual network
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load-balanced endpoint for each node in $AGNodes by using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

13. 设置变量后，将脚本从文本编辑器复制到 PowerShell 会话并运行。 如果提示符仍然显示 >>，请再次按 Enter，以确保脚本开始运行。

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>如果需要，请验证是否已安装 KB2854082
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>在可用性组节点中打开防火墙端口
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>创建可用性组侦听器

通过两个步骤创建可用性组侦听器。 首先，创建客户端接入点的群集资源，并配置依赖关系。 其次，在 PowerShell 中配置群集资源。

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>创建客户端接入点和配置群集依赖关系
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>在 PowerShell 中配置群集资源
1. 对于 ILB，必须使用前面创建的 ILB 的 IP 地址。 在 PowerShell 中使用以下脚本获取此 IP 地址：

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

2. 在某个 VM 上，将操作系统的 PowerShell 脚本复制到文本编辑器中，然后将变量设置为之前记下的值。

    对于 Windows Server 2012 或更高版本，请使用以下脚本：

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}

    对于 Windows Server 2008 R2，请使用以下脚本：

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP address resource name
        $ILBIP = "<X.X.X.X>" # the IP address of the ILB

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

3. 设置变量之后，打开提升的 Windows PowerShell 窗口，然后将文本编辑器中的脚本粘贴到 PowerShell 会话并运行。 如果提示符仍然显示 **>>** ，请再次按 Enter，确保脚本开始运行。

4. 针对每个 VM 重复上述步骤。  
    此脚本使用云服务的 IP 地址配置 IP 地址资源，同时设置探测端口等其他参数。 在 IP 地址资源联机后，它可以响应我们前面创建的负载均衡终结点在探测端口上的轮询。

## <a name="bring-the-listener-online"></a>使侦听器联机
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>后续操作项
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-virtual-network"></a>测试可用性组侦听器（在同一虚拟网络中）
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>后续步骤
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]
