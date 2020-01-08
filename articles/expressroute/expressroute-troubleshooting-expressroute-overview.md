---
title: 验证连接 - ExpressRoute 故障排除指南：Azure | Microsoft Docs
description: 本页说明如何对 ExpressRoute 线路的端到端连接进行故障排除和验证。
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 026900e3dcbf7c20750bb8e17e44ba64897c9a30
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123439"
---
# <a name="verifying-expressroute-connectivity"></a>验证 ExpressRoute 连接
本文可帮助验证 ExpressRoute 连接并对其进行故障排除。 ExpressRoute 可以通过经连接提供商加速的专用连接将本地网络扩展到 Microsoft 云中，涉及以下三个不同的网络区域：

-   客户网络
-   提供商网络
-   Microsoft 数据中心

本文档的目的是帮助用户确定存在连接问题的位置和区域（或者只是单纯地确定是否存在连接问题），以便向相应的团队寻求帮助以解决问题。 如果需要 Microsoft 支持才能解决问题，请使用[Microsoft 支持部门][Support]打开支持票证。

> [!IMPORTANT]
> 本文档旨在帮助用户诊断和修复简单问题。 它不是为了替代 Microsoft 支持部门。 如果无法使用所提供的指南解决问题，请使用[Microsoft 支持部门][Support]打开支持票证。
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概述
下图显示了客户网络通过 ExpressRoute 连接到 Microsoft 网络时的逻辑连接。
[![1]][1]

在上图中，数字表示关键网络点。 在本文中，网络点通常通过其关联的数字来引用。

网络点 3 和 4 可以是交换机（第 2 层设备），具体取决于 ExpressRoute 连接模型（云交换归置、点到点以太网连接，或任意位置之间的连接 (IPVPN)）。 上述关键网络点详述如下：

1.  客户计算设备（例如服务器或电脑）
2.  CE：客户边缘路由器 
3.  PE（面向 CE）：提供商边缘路由器/交换机，面向客户边缘路由器。 在本文档中称为 PE-CE。
4.  PE（面向 MSEE）：提供商边缘路由器/交换机，面向 MSEE。 在本文档中称为 PE-MSEE。
5.  MSEE：Microsoft 企业边缘 (MSEE) ExpressRoute 路由器
6.  虚拟网络 (VNet) 网关
7.  Azure VNet 上的计算设备

如果使用“云交换归置”或“点到点以太网连接”连接模型，客户边缘路由器 (2) 会与 MSEE (5) 建立 BGP 对等互连。 网络点 3 和 4 仍将存在，但作为第 2 层设备，其在某种程度上是透明的。

如果使用“任意位置之间的连接 (IPVPN)”连接模型，PE（面向 MSEE）(4) 会与 MSEE (5) 建立 BGP 对等互连。 然后，路由通过 IPVPN 服务提供商网络传播回客户网络。

> [!NOTE]
>为了确保 ExpressRoute 高可用性，Microsoft 要求在 MSEE (5) 和 PE-MSEE (4) 之间存在冗余性的 BGP 会话对。 另外还建议在客户网络和 PE-CE 之间设置冗余性的网络路径对。 但是，在“任意位置之间的连接 (IPVPN)”模型中，单个 CE 设备 (2) 可能会连接到一个或多个 PE (3)。
>
>

若要验证 ExpressRoute 线路，可执行以下步骤（网络点以关联的数字表示）：
1. [验证线路预配和状态 (5)](#validate-circuit-provisioning-and-state)
2. [验证是否已配置至少一个 ExpressRoute 对等互连 (5)](#validate-peering-configuration)
3. [验证 Microsoft 和服务提供商之间的 ARP（4 和 5 之间的链接）](#validate-arp-between-microsoft-and-the-service-provider)
4. [验证 MSEE 上的 BGP 和路由（4 到 5 之间的 BGP，如果连接了 VNet，则还包括 5 到 6 之间的 BGP）](#validate-bgp-and-routes-on-the-msee)
5. [检查流量统计信息（经过 5 的流量）](#check-the-traffic-statistics)

将来会添加更多的验证和检查，请每月回来查看！

## <a name="validate-circuit-provisioning-and-state"></a>验证线路预配和状态
不管什么连接模型，都必须创建 ExpressRoute 线路，从而生成用于线路预配的服务密钥。 预配 ExpressRoute 线路即可在 PE-MSEE (4) 和 MSEE (5) 之间建立冗余性的第 2 层连接。 有关如何创建、修改、预配和验证 ExpressRoute 线路的详细信息，请参阅[创建和修改 expressroute 线路][CreateCircuit]一文。

>[!TIP]
>服务密钥可以唯一地标识 ExpressRoute 线路。 对于本文档中提到的大多数 PowerShell 命令，此密钥是必需的。 另外，如果需要 Microsoft 或 ExpressRoute 合作伙伴的帮助来排查 ExpressRoute 问题，请提供服务密钥，以便标识线路。
>
>

### <a name="verification-via-the-azure-portal"></a>通过 Azure 门户进行验证
可以在 Azure 门户中查看 ExpressRoute 线路的状态，方法是：在左侧栏菜单上选择“![2][2]”，并选择 ExpressRoute 线路。 选择“所有资源”下列出的 ExpressRoute 线路即可打开 ExpressRoute 线路边栏选项卡。 在边栏选项卡的 " ![3][3] " 部分中，列出了 ExpressRoute 概要，如以下屏幕截图所示：

![4][4]    

在 ExpressRoute 的“概要”中，“线路状态”表示 Microsoft 这一侧线路的状态。 “提供商状态”表示线路在服务提供商这一侧的状态是“已预配”还是“未预配”。 

若要确保 ExpressRoute 线路正常运行，“线路状态”必须为“已启用”，“提供商状态”必须为“已预配”。

> [!NOTE]
> 如果未启用*线路状态*，请联系[Microsoft 支持部门][Support]。 如果“提供商状态”不是“已预配”，请与服务提供商联系。
>
>

### <a name="verification-via-powershell"></a>通过 PowerShell 进行验证
若要列出资源组中的所有 ExpressRoute 线路，请使用以下命令：

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>可通过 Azure 获取资源组名称。 请参阅本文档的上一小节，并注意该资源组名称已在示例屏幕截图中列出。
>
>

若要选择资源组中的特定 ExpressRoute 线路，请使用以下命令：

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

示例响应如下：

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

若要确认 ExpressRoute 线路是否正常运行，请特别注意以下字段：

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> 如果未启用*CircuitProvisioningState* ，请联系[Microsoft 支持部门][Support]。 如果“ServiceProviderProvisioningState”不是“已预配”，请与服务提供商联系。
>
>

### <a name="verification-via-powershell-classic"></a>通过 PowerShell（经典）进行验证
若要列出订阅的所有 ExpressRoute 线路，请使用以下命令：

    Get-AzureDedicatedCircuit

若要选择特定 ExpressRoute 线路，请使用以下命令：

    Get-AzureDedicatedCircuit -ServiceKey **************************************

示例响应如下：

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

若要确认 ExpressRoute 线路是否正常运行，请特别注意以下字段：ServiceProviderProvisioningState：预配状态：Enabled

> [!NOTE]
> 如果*状态*为 "未启用"，请联系[Microsoft 支持部门][Support]。 如果“ServiceProviderProvisioningState”不是“已预配”，请与服务提供商联系。
>
>

## <a name="validate-peering-configuration"></a>验证对等互连配置
在服务提供商完成对 ExpressRoute 线路的预配以后，即可基于 MSEE-PR (4) 和 MSEE (5) 之间的 ExpressRoute 线路创建路由配置。 每条 ExpressRoute 线路可以启用一个、两个或三个路由上下文：Azure 专用对等互连（流向 Azure 中的专用虚拟网络）、Azure 公共对等互连（到 Azure 中的公共 IP 地址的流量）以及 Microsoft 对等互连（流量发送到 Office 365）。 有关如何创建和修改路由配置的详细信息，请参阅[创建和修改 ExpressRoute 线路的路由一][CreatePeering]文。

### <a name="verification-via-the-azure-portal"></a>通过 Azure 门户进行验证

> [!NOTE]
> 如果服务提供商提供第 3 层且对等互连在门户中为空，请使用门户中的刷新按钮来刷新线路配置。 此操作会将正确的线路配置应用到你的线路。 
>
>

可以在 Azure 门户中查看 ExpressRoute 线路的状态，方法是：在左侧栏菜单上选择“![2][2]”，并选择 ExpressRoute 线路。 选择“所有资源”下列出的 ExpressRoute 线路会打开 ExpressRoute 线路边栏选项卡。 在边栏选项卡的 " ![3][3] " 部分中，将列出 ExpressRoute 概要，如以下屏幕截图所示：

![5][5]

如以上示例所述，Azure 专用对等互连路由上下文已启用，而 Azure 公共和 Microsoft 对等互连路由上下文则未启用。 成功启用的对等互连上下文还会列出主要的和辅助的点到点（BGP 所必需）子网。 /30 子网适用于 MSEE 和 PE-MSEE 的接口 IP 地址。 

> [!NOTE]
> 如果未启用对等互连，请检查分配的主要子网和辅助子网是否符合 PE-MSEE 上的配置。 如果没有，若要更改 MSEE 路由器上的配置，请参阅[创建和修改 ExpressRoute 线路的路由][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>通过 PowerShell 进行验证
若要获取 Azure 专用对等互连配置详细信息，请使用以下命令：

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

已成功配置的专用对等互连的示例响应如下：

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 成功启用的对等互连上下文会列出主要的和辅助的地址前缀。 /30 子网适用于 MSEE 和 PE-MSEE 的接口 IP 地址。

若要获取 Azure 公共对等互连配置详细信息，请使用以下命令：

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

若要获取 Microsoft 对等互连配置详细信息，请使用以下命令：

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

如果未配置对等互连，则会出现错误信息。 当所述对等互连（本示例中为 Azure 公共对等互连）未在线路中配置时的示例响应如下：

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> 如果未启用对等互连，请检查分配的主要子网和辅助子网是否符合链接的 PE-MSEE 上的配置。 另请检查是否在 MSEE 上使用了正确的 VlanId、AzureASN 和 PeerASN，以及这些值是否映射到链接的 PE-MSEE 上使用的对应项。 如果选择了 MD5 哈希，则 MSEE 和 PE-MSEE 对上的共享密钥应相同。 若要更改 MSEE 路由器上的配置，请参阅[创建和修改 ExpressRoute 线路的路由][CreatePeering]。  
>
>

### <a name="verification-via-powershell-classic"></a>通过 PowerShell（经典）进行验证
若要获取 Azure 专用对等互连配置详细信息，请使用以下命令：

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

已成功配置的专用对等互连的示例响应如下：

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

成功启用的对等互连上下文会列出主要的和辅助的对等子网。 /30 子网适用于 MSEE 和 PE-MSEE 的接口 IP 地址。

若要获取 Azure 公共对等互连配置详细信息，请使用以下命令：

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

若要获取 Microsoft 对等互连配置详细信息，请使用以下命令：

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> 如果服务提供商设置了第 3 层对等互连，则通过门户或 PowerShell 设置 ExpressRoute 对等互连会覆盖服务提供商设置。 重置提供商这一侧的对等互连设置需要服务提供商的支持。 如果确定服务提供商只提供第 2 层服务，则只修改 ExpressRoute 对等互连！
>
>

> [!NOTE]
> 如果未启用对等互连，请检查分配的主要对等子网和辅助对等子网是否符合链接的 PE-MSEE 上的配置。 另请检查是否在 MSEE 上使用了正确的 VlanId、AzureAsn 和 PeerAsn，以及这些值是否映射到链接的 PE-MSEE 上使用的对应项。 若要更改 MSEE 路由器上的配置，请参阅[创建和修改 ExpressRoute 线路的路由][CreatePeering]。
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>验证 Microsoft 和服务提供商之间的 ARP
本部分使用 PowerShell（经典）命令。 如果一直使用 PowerShell Azure 资源管理器命令，请确保对订阅具有管理员/共同管理员权限。 有关使用 Azure 资源管理器命令进行故障排除，请参阅[资源管理器部署模型文档中的获取 ARP 表][ARP]。

> [!NOTE]
>获取 ARP 时，Azure 门户和 Azure 资源管理器 PowerShell 命令均可使用。 如果使用 Azure 资源管理器 PowerShell 命令时出错，则应使用经典 PowerShell 命令，因为经典 PowerShell 命令也适用于 Azure 资源管理器 ExpressRoute 线路。
>
>

若要从主要的进行专用对等互连的 MSEE 路由器获取 ARP 表，请使用以下命令：

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

成功情况下，该命令的示例响应如下：

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

同样，对于*专用*/*公共*/*Microsoft* 对等互连，也可在*主要*/*辅助* 路径中查看 MSEE 提供的 ARP 表。

以下示例显示某个对等互连的命令响应不存在。

    ARP Info:
       
> [!NOTE]
> 如果 ARP 表没有将接口的 IP 地址映射到 MAC 地址，请查询以下信息：
>1. 为 MSEE-PR 和 MSEE 之间的链接分配的 /30 子网的第一个 IP 地址是否用在 MSEE-PR 的接口上。 Azure 始终使用 MSEE 的第二个 IP 地址。
>2. 验证客户型 (C-Tag) 和服务型 (S-Tag) VLAN 标记在 MSEE-PR 和 MSEE 对上是否均匹配。
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>验证 BGP 以及 MSEE 上的路由
本部分使用 PowerShell（经典）命令。 如果一直使用 PowerShell Azure 资源管理器命令，请确保对订阅具有管理员/共同管理员权限。

> [!NOTE]
>获取 BGP 信息时，Azure 门户和 Azure 资源管理器 PowerShell 命令均可使用。 如果使用 Azure 资源管理器 PowerShell 命令时出错，则应使用经典 PowerShell 命令，因为经典 PowerShell 命令也适用于 Azure 资源管理器 ExpressRoute 线路。
>
>

若要获取特定路由上下文的路由表（BGP 邻居）摘要，请使用以下命令：

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

示例响应如下：

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

如以上示例所示，该命令用于确定路由上下文已建立多长时间。 它还指示对等互连的路由器播发的路由前缀的数。

> [!NOTE]
> 如果状态为“活动”或“空闲”，请检查分配的主要对等子网和辅助对等子网是否符合链接的 PE-MSEE 上的配置。 另请检查是否在 MSEE 上使用了正确的 VlanId、AzureAsn 和 PeerAsn，以及这些值是否映射到链接的 PE-MSEE 上使用的对应项。 如果选择了 MD5 哈希，则 MSEE 和 PE-MSEE 对上的共享密钥应相同。 若要更改 MSEE 路由器上的配置，请参阅[创建和修改 ExpressRoute 线路的路由][CreatePeering]。
>
>

> [!NOTE]
> 如果某些目标无法通过特定对等互连访问，请检查属于特定对等互连上下文的 MSEE 的路由表。 如果路由表中存在匹配的前缀（可能是 NAT 型 IP），则请检查路径上是否设置了防火墙/NSG/ACL，以及这些设置是否允许通信。
>
>

对于特定的“专用”路由上下文，若要获取“主要”路径上的 MSEE 提供的完整路由表，请使用以下命令：

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

成功情况下，该命令的示例结果如下：

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

同样，对于*专用*/*公共*/*Microsoft* 对等互连上下文，也可在*主要*/*辅助* 路径中查看 MSEE 提供的路由表。

以下示例显示某个对等互连的命令响应不存在：

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>检查流量统计信息
若要获取对等互连上下文在主要路径和辅助路径上的综合流量统计信息（出入字节数），请使用以下命令：

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

该命令的示例输出如下：

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

对于不存在的对等互连，该命令的示例输出如下：

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>后续步骤
有关详细信息或帮助，请查看以下链接：

- [Microsoft 支持][Support]
- [创建和修改 ExpressRoute 线路][CreateCircuit]
- [为 ExpressRoute 线路创建和修改路由][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "逻辑 ExpressRoute 连接"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "“所有资源”图标"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "“概述”图标"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute 概要示例屏幕截图"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute 概要示例屏幕截图"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






