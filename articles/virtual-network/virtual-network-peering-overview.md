---
title: Azure 虚拟网络对等互连
titlesuffix: Azure Virtual Network
description: 了解 Azure 中的虚拟网络对等互连。
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 100bbb6e0ed8e2ea5b35e30e7759a3b11c169b60
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077628"
---
# <a name="virtual-network-peering"></a>虚拟网络对等互连

使用虚拟网络对等互连可以无缝连接 Azure [虚拟网络](virtual-networks-overview.md)。 建立对等互连后，出于连接目的，两个虚拟网络会显示为一个。 对等虚拟网络中虚拟机之间的流量通过 Microsoft 主干基础结构路由，非常类似于只通过专用 IP 地址在同一虚拟网络中的虚拟机之间路由流量。  Azure 支持：
* VNet 对等互连 - 连接同一 Azure 区域中的 VNet
* 全局 VNet 对等互连 - 跨 Azure 区域连接 VNet

使用虚拟网络对等互连（无论本地还是全局）的优点包括：

* 对等虚拟网络之间的网络流量是专用的。 虚拟网络之间的流量仅限于 Microsoft 主干网络。 在虚拟网络之间通信不需公共 Internet、网关或加密。
* 不同虚拟网络中资源之间的连接延迟低且带宽高。
* 在虚拟网络对等互连之后，一个虚拟网络中的资源与另一虚拟网络中的资源通信的功能。
* 跨 Azure 订阅、部署模型和 Azure 区域传输数据的功能。
* 可将通过 Azure 资源管理器创建的虚拟网络对等互连，或者将一个通过资源管理器创建的虚拟网络对等互连到通过经典部署模型创建的虚拟网络。 若要详细了解 Azure 部署模型，请参阅[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 在创建对等互连之时或之后，虚拟网络中的资源不会出现停机的现象。

## <a name="connectivity"></a>连接

将虚拟网络对等互连后，其中一个虚拟网络中的虚拟机资源可直接连接到对等互连虚拟网络中的资源。

同一区域中对等互连虚拟网络上的虚拟机之间的网络延迟与单个虚拟网络中的延迟相同。 网络吞吐量取决于可供虚拟机使用的与其大小成比例的带宽。 对等互连的带宽没有任何其他限制。

对等互连虚拟网络中虚拟机之间的流量直接通过 Microsoft 主干基础结构路由，而不通过网关或公共 Internet 路由。

可根据需要将网络安全组应用于虚拟网络（阻止访问其他虚拟网络）或子网。
配置虚拟网络对等互连时，可打开或关闭虚拟网络之间的网络安全组规则。 如果打开对等虚拟网络之间的完全连接（这是默认选项），则可将网络安全组应用到特定子网或虚拟机，以便阻止或拒绝特定访问。 若要深入了解网络安全组，请参阅[网络安全组概述](security-overview.md)。

## <a name="service-chaining"></a>服务链

可将指向对等虚拟网络中虚拟机的用户定义的路由表配置为“下一跃点”IP 地址（也可配置指向虚拟网关的用户定义的路由），以便启用服务链。  使用服务链，可以通过用户定义的路由将流量从一个虚拟网络定向到对等虚拟网络中的虚拟设备（或虚拟网关）。

可以部署中心辐射型网络，允许中心虚拟网络在其中托管基础结构组件，如网络虚拟设备或 VPN 网关。 然后，可以将所有分散虚拟网络与中心虚拟网络对等。 流量可以流经中心虚拟网络中的网络虚拟设备或 VPN 网关。 

通过虚拟网络对等互连，用户定义的路由中的下一个跃点可以成为对等虚拟网络中虚拟机或 VNP 网关的 IP 地址。 但在虚拟网络之间进行路由时，不能通过用户定义的路由将 ExpressRoute 网关指定为下一跃点类型。 若要深入了解用户定义的路由，请参阅[用户定义的路由概述](virtual-networks-udr-overview.md#user-defined)。 若要了解如何创建中心和分支网络拓扑，请参阅[中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="gateways-and-on-premises-connectivity"></a>网关和本地连接

无论是否与另一个虚拟网络对等，每个虚拟网络仍可具有自己的网关，并使用它连接到本地网络。 即使虚拟网络对等，用户也可以使用网关配置[虚拟网络到虚拟网络连接](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

若已配置虚拟网络互连的两个选项，则虚拟网络之间的流量将通过对等配置（即通过 Azure 主干）流通。

虚拟网络对等时，用户还可以将对等虚拟网络中的网关配置为本地网络的传输点。 在这种情况下，使用远程网关的虚拟网络没有自己的网关。 虚拟网络只能有一个网关。 网关可能是本地网关或远程网关（对等虚拟网络中），如下图所示：

![虚拟网络对等互连传输](./media/virtual-networks-peering-overview/figure04.png)

VNet 对等互连和全局 VNet 对等互连均支持网关传输。 仅当网关位于虚拟网络（资源管理器）中时，才支持通过不同部署模型（资源管理器和经典）创建的虚拟网络之间的网关传输。 若要了解有关使用网关进行传输的详细信息，请参阅[配置 VPN 网关以在虚拟网络对等互连中传输](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

正在共享单个 Azure ExpressRoute 连接的虚拟网络对等时，它们之间的流量会通过对等关系（即通过 Azure 主干网）流通。 仍可在各个虚拟网络中使用本地网关连接到本地线路。 也可使用共享网关，并为本地连接配置传输。

## <a name="troubleshoot"></a>故障排除

若要确认虚拟网络对等互连，可以[检查有效路由](diagnose-network-routing-problem.md)，看虚拟网络的任何子网中是否存在网络接口。 如果虚拟网络对等互连存在，则虚拟网络中的所有子网都会有下一跃点类型为“VNet 对等互连”的路由，这适用于每个对等互连的虚拟网络中的每个地址空间。 

还可以使用网络观察程序的[连接性检查](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来排查与对等互连虚拟网络中某个虚拟机之间的连接问题。 可以通过连接性检查来确定流量如何从源虚拟机的网络接口路由到目标虚拟机的网络接口。

也可尝试[虚拟网络对等互连问题的故障排除工具](https://support.microsoft.com/help/4486956/troubleshooter-for-virtual-network-peering-issues)。

## <a name="requirements-and-constraints"></a>要求和约束

仅当虚拟网络全局对等互连时，以下约束适用：
- 一个虚拟网络中的资源无法与全局对等互连虚拟网络中基本内部负载均衡器的前端 IP 地址通信。 对基本负载均衡器的支持仅存在于同一区域内。 VNet 对等互连和全局 VNet 对等互连都支持标准负载均衡器。 [此处](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)记录了使用基本负载均衡器但不能通过全局 VNet 对等互连正常工作的服务。

若要详细了解要求和约束，请参阅[虚拟网络对等互连要求和约束](virtual-network-manage-peering.md#requirements-and-constraints)。 若要了解可以为虚拟网络创建的对等互连的数目限制，请参阅 [Azure 网络限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 

## <a name="permissions"></a>权限

若要了解创建虚拟网络对等互连所需的权限，请参阅[虚拟网络对等互连权限](virtual-network-manage-peering.md#permissions)。

## <a name="pricing"></a>定价

利用虚拟网络对等互连的入口和出口流量会产生少许费用。 有关 VNet 对等互连和全局 VNet 对等互连定价的详细信息，请参阅[定价页面](https://azure.microsoft.com/pricing/details/virtual-network)。

网关传输是启用要使用用于跨内部部署或 VNet 到 VNet 连接的对等互连虚拟网络中的 VPN/ExpressRoute 网关的虚拟网络对等互连属性。 通过在此方案中的远程网关的流量都将遵守[VPN 网关费用](https://azure.microsoft.com/pricing/details/vpn-gateway/)或 ExpressRoute 网关费用和不会产生[VNet 对等互连费用。](https://azure.microsoft.com/pricing/details/virtual-network) 例如，如果 VNetA 具有本地连接的 VPN 网关，并且 VNetB 对等互连到 VNetA 与配置的相应属性，从 VNetB 到的本地流量仅收取出口每个 VPN 网关定价或 ExpressRoute 定价。 不会产生 VNet 对等互连费用。 了解如何[针对虚拟网络对等互连配置 VPN 网关传输](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="next-steps"></a>后续步骤

* 可在通过相同或不同订阅中的相同或不同部署模型创建的虚拟网络之间创建虚拟网络对等互连。 完成适用于以下方案之一的教程：

    |Azure 部署模型             | 订阅  |
    |---------                          |---------|
    |都是资源管理器模型              |[相同](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[不同](create-peering-different-subscriptions.md)|
    |一个是资源管理器模型，一个是经典模型  |[相同](create-peering-different-deployment-models.md)|
    |                                   |[不同](create-peering-different-deployment-models-subscriptions.md)|

* 了解如何创建[中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 了解所有[虚拟网络对等互连设置以及如何对其进行更改](virtual-network-manage-peering.md)。
* 通过我们的 [VNet 对等互连常见问题解答](virtual-networks-faq.md#vnet-peering)获取常见的 VNet 对等互连和全局 VNet 对等互连问题的答案
