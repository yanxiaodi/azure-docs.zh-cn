---
title: Azure VPN 网关常见问题 | Microsoft 文档
description: VPN 网关常见问题。 Microsoft Azure 虚拟网络跨界连接、混合配置连接和 VPN 网关的常见问题。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: yushwang
ms.openlocfilehash: 23dc017b6ffcca8761966a10bd5cb45b32c7a602
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65786700"
---
# <a name="vpn-gateway-faq"></a>VPN 网关常见问题

## <a name="connecting"></a>连接到虚拟网络

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>能否连接不同 Azure 区域中的虚拟网络？

是的。 事实上，没有任何区域约束。 一个虚拟网络可以连接到同一区域中的其他虚拟网络，也可以连接到其他 Azure 区域中的其他虚拟网络。 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>能否连接不同订阅中的虚拟网络？

是的。

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>能否从一个虚拟网络连接到多个站点？

可以使用 Windows PowerShell 和 Azure REST API 连接到多个站点。 请参阅 [多站点与 VNet 到 VNet 连接](#V2VMulti) 的“常见问题”部分。

### <a name="is-there-an-additional-cost-for-setting-up-a-vpn-gateway-as-active-active"></a>将 VPN 网关设置为“主动-主动”是否需要额外费用？

不。 

### <a name="what-are-my-cross-premises-connection-options"></a>我的跨界连接选项有哪些？

支持以下跨界连接：

* 站点到站点 - 基于 IPsec（IKE v1 和 IKE v2）的 VPN 连接。 此类型的连接需要 VPN 设备或 RRAS。 有关详细信息，请参阅[站点到站点](vpn-gateway-howto-site-to-site-resource-manager-portal.md)。
* 点到站点 - 基于 SSTP（安全套接字隧道协议）或 IKE v2 的 VPN 连接。 此连接不需要 VPN 设备。 有关详细信息，请参阅[点到站点](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。
* VNet 到 VNet - 此类连接与站点到站点配置相同。 VNet 到 VNet 是一种基于 IPsec（IKE v1 和 IKE v2）的 VPN 连接。 它不需要 VPN 设备。 有关详细信息，请参阅 [VNet 到 VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。
* 多站点 - 这是站点到站点配置的变体，可以用于将多个本地站点连接到虚拟网络。 有关详细信息，请参阅[多站点](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)。
* ExpressRoute - ExpressRoute 是从 WAN 到 Azure 的专用连接，不是通过公共 Internet 的 VPN 连接。 有关详细信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)和 [ExpressRoute 常见问题](../expressroute/expressroute-faqs.md)。

有关 VPN 网关连接的详细信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>站点到站点连接和点到站点连接的区别是什么？

**站点到站点**（IPsec/IKE VPN 隧道）配置是指本地位置与 Azure 之间的配置。 这意味着，可以将任何本地计算机连接到虚拟网络中的任何虚拟机或角色实例，具体取决于如何选择路由和权限的配置。 它对于需要始终可用的跨界连接来说是一个极佳的选项，很适合混合配置。 此类连接依赖于 IPsec VPN 设备（硬件设备或软件设备），该设备必须部署在网络边缘。 若要创建此类连接，必须具有面向外部的 IPv4 地址。

**点到站点**（基于 SSTP 的 VPN）配置允许从任何位置的单台计算机连接到虚拟网络中的任何内容。 它使用 Windows 内置的 VPN 客户端。 在进行点到站点配置时，需要安装证书和 VPN 客户端配置包，其中包含的设置允许计算机连接到虚拟网络中的任何虚拟机或角色实例。 此连接适用于需要连接到虚拟网络但该虚拟网络不在本地的情况。 无法访问 VPN 硬件或面向外部的 IPv4 地址（二者是进行站点到站点连接所必需的）时，它也是一个很好的选项。

可以将虚拟网络配置为同时使用站点到站点连接和点到站点连接，前提是使用基于路由的 VPN 类型为网关创建站点到站点连接。 在经典部署模型中，基于路由的 VPN 类型称为动态网关。

## <a name="gateways"></a>虚拟网关

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>VPN 网关是否为虚拟网关？

VPN 网关是一类虚拟网关。 VPN 网关可跨公共连接在虚拟网络和本地位置发送加密的流量。 还可使用 VPN 网关在虚拟网络之间发送流量。 在创建 VPN 网关时，可以使用 -GatewayType 值 'Vpn'。 有关详细信息，请参阅[关于 VPN 网关配置设置](vpn-gateway-about-vpn-gateway-settings.md)。

### <a name="what-is-a-policy-based-static-routing-gateway"></a>什么是基于策略的（静态路由）网关？

基于策略的网关实施基于策略的 VPN。 基于策略的 VPN 会根据本地网络和 Azure VNet 之间的地址前缀的各种组合，加密数据包并引导其通过 IPsec 隧道。 通常会在 VPN 配置中将策略（或流量选择器）定义为访问列表。

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>什么是基于路由的（动态路由）网关？

基于路由的网关可实施基于路由的 VPN。 基于路由的 VPN 使用 IP 转发或路由表中的“路由”将数据包引导到相应的隧道接口中。 然后，隧道接口会加密或解密出入隧道的数据包。 基于路由的 VPN 的策略或流量选择器配置为任意到任意（或通配符）。

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>能否将基于策略的 VPN 网关更新为基于路由的？
不。 Azure Vnet 网关类型不能从基于策略更改为基于路由，反之亦然。 必须先删除该网关，然后再重新创建，此过程需时约 60 分钟。 不会保留网关的 IP 地址，也不会保留预共享密钥 (PSK)。
1. 删除与要删除的网关相关联的任何连接。
1. 删除网关：
1. [Azure 门户](vpn-gateway-delete-vnet-gateway-portal.md)
1. [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
1. [Azure Powershell - 经典](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
1. [创建所需类型的新网关并完成 VPN 设置](vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway)

### <a name="do-i-need-a-gatewaysubnet"></a>是否需要 'GatewaySubnet'？

是的。 网关子网包含虚拟网络网关服务使用的 IP 地址。 若要配置虚拟网关，需要先为 VNet 创建网关子网。 所有网关子网都必须命名为 'GatewaySubnet' 才能正常工作。 不要对网关子网使用其他名称。 此外，不要在网关子网中部署 VM 或其他组件。

创建网关子网时，需指定子网包含的 IP 地址数。 网关子网中的 IP 地址分配到网关服务。 某些配置相对于其他配置需要将更多 IP 地址分配到网关服务。 需确保网关子网包含足够多的 IP 地址，以便应对将来的业务增长以及可能会添加的新连接配置。 因此，尽管网关子网最小可创建为 /29，但建议创建 /27 或更大（/27、/26 和 /25 等）的网关子网。 查看要创建的配置的要求，并验证所拥有的网关子网是否可满足这些要求。

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>是否可以将虚拟机或角色实例部署到网关子网？

不。

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>能否先获得 VPN 网关 IP 地址，再创建网关？

不。 必须先创建网关，才能获得 IP 地址。 如果删除再重新创建 VPN 网关，IP 地址将更改。

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>能否为 VPN 网关请求静态公共 IP 地址？

不。 仅支持动态 IP 地址分配。 但这并不意味着 IP 地址在分配到 VPN 网关后会更改。 VPN 网关 IP 地址只在删除或重新创建网关时更改。 VPN 网关公共 IP 地址不会因为重新调整大小、重置或其他 VPN 网关内部维护/升级而更改。 

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>VPN 隧道如何进行身份验证？

Azure VPN 使用 PSK（预共享密钥）身份验证。 我们在创建 VPN 网关时生成一个预共享密钥 (PSK)。 可以使用设置预共享密钥 PowerShell cmdlet 或 REST API 会自动生成的 PSK 更改成自己的 PSK。

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>能否使用“设置预共享密钥 API”来配置基于策略的（静态路由）网关 VPN？

可以，“设置预共享密钥 API”和 PowerShell cmdlet 可用于配置基于 Azure Policy 的（静态）VPN 和基于路由的（动态）路由 VPN。

### <a name="can-i-use-other-authentication-options"></a>能否使用其他身份验证选项？

我们只能使用预共享密钥 (PSK) 进行身份验证。

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>如何指定通过 VPN 网关的流量？

#### <a name="resource-manager-deployment-model"></a>Resource Manager 部署模型

* PowerShell：使用“AddressPrefix”指定本地网络网关的流量。
* Azure 门户：导航到“本地网络网关”>“配置”>“地址空间”。

#### <a name="classic-deployment-model"></a>经典部署模型

* Azure 门户：导航到“经典虚拟网络”>“VPN 连接”>“站点到站点 VPN 连接”>“本地站点名称”>“本地站点”>“客户端地址空间”。 

### <a name="can-i-configure-force-tunneling"></a>是否可以配置强制隧道？

是的。 请参阅[配置强制隧道](vpn-gateway-about-forced-tunneling.md)。

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>能否在 Azure 中设置自己的 VPN 服务器，并使用该服务器连接到本地网络？

能。可以在 Azure 中部署自己的 VPN 网关或服务器，可以从 Azure 市场部署，也可以通过创建自己的 VPN 路由器来部署。 需要在虚拟网络中配置用户定义的路由，确保流量在本地网络和虚拟网络子网之间正确路由。

### <a name="gatewayports"></a>我的虚拟网络网关上的某些端口为何处于打开状态？

这些端口是进行 Azure 基础结构通信所必需的。 它们受 Azure 证书的保护（处于锁定状态）。 如果没有适当的证书，外部实体（包括这些网关的客户）将无法对这些终结点施加任何影响。

虚拟网络网关基本上是一个多宿主设备，其中一个 NIC 进入客户专用网络，另一个 NIC 面向公共网络。 因合规性原因，Azure 基础结构实体无法进入客户专用网络，因此需利用公共终结点进行基础结构通信。 Azure 安全审核会定期扫描公共终结点。

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>有关网关类型、要求和吞吐量的详细信息

有关详细信息，请参阅[关于 VPN 网关配置设置](vpn-gateway-about-vpn-gateway-settings.md)。

## <a name="s2s"></a>站点到站点连接和 VPN 设备

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>选择 VPN 设备时应考虑什么？

我们在与设备供应商合作的过程中验证了一系列的标准站点到站点 VPN 设备。 可在[关于 VPN 设备](vpn-gateway-about-vpn-devices.md)一文中找到已知兼容的 VPN 设备及其相应的配置说明/示例和设备规范的列表。 设备系列中列为已知兼容设备的所有设备都应适用于虚拟网络。 若要获取配置 VPN 设备的帮助，请参考对应于相应设备系列的设备配置示例或链接。

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>在哪里可以找到 VPN 设备配置设置？

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>如何编辑 VPN 设备配置示例？

若要了解如何编辑设备配置示例，请参阅[编辑示例](vpn-gateway-about-vpn-devices.md#editing)。

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>在何处查找 IPsec 和 IKE 参数？

对于 IPsec/IKE 参数，请参阅[参数](vpn-gateway-about-vpn-devices.md#ipsec)。

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>在流量处于空闲状态时，为何我那基于策略的 VPN 隧道会关闭？

对于基于策略（也称为静态路由）的 VPN 网关来说，这是预期的行为。 当经过隧道的流量处于空闲状态 5 分钟以上时，将销毁该隧道。 当流量朝任一方向开始流动时，该隧道将立刻重新建立。

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>我可以使用软件 VPN 连接到 Azure 吗？

我们支持将 Windows Server 2012 路由和远程访问 (RRAS) 服务器用于站点到站点跨界配置。

其他软件 VPN 解决方案只要遵循行业标准 IPsec 实现，就会与我们的网关兼容。 有关配置和支持说明，请与该软件的供应商联系。

## <a name="P2S"></a>点到站点 - 使用本机 Azure 证书身份验证

本部分适用于资源管理器部署模型。

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="P2SRADIUS"></a>点到站点 - 使用 RADIUS 身份验证

本部分适用于资源管理器部署模型。

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="V2VMulti"></a>VNet 到 VNet 连接和多站点连接

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>能否使用 Azure VPN 网关在我的本地站点之间传输流量或将流量传输到其他虚拟网络？

**Resource Manager 部署模型**<br>
是的。 有关详细信息，请参阅 [BGP](#bgp) 部分。

**经典部署模型**<br>
使用经典部署模型通过 Azure VPN 网关传输流量是可行的，但需要依赖网络配置文件中静态定义的地址空间。 使用经典部署模型的 Azure 虚拟网络和 VPN 网关尚不支持 BGP。 没有 BGP，手动定义传输地址空间很容易出错，不建议这样做。

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure 会为同一虚拟网络的所有 VPN 连接生成同一 IPsec/IKE 预共享密钥吗？

不会。默认情况下，Azure 会为不同 VPN 连接生成不同的预共享密钥。 但是，可以使用设置 VPN 网关密钥 REST API 或 PowerShell cmdlet 设置你想要的密钥值。 密钥必须是可打印 ASCII 字符集。

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>使用更多站点到站点 VPN 我会为单个虚拟网络获取更多带宽吗？

不会，所有 VPN 隧道（包括点到站点 VPN）共享同一 Azure VPN 网关和可用带宽。

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>是否可以使用多站点 VPN 在我的虚拟网络和本地站点之间配置多个隧道？

是，但必须在两个通向同一位置的隧道上配置 BGP。

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>能否将点到站点 VPN 用于具有多个 VPN 隧道的虚拟网络？

能，可以将点到站点 (P2S) VPN 用于连接到多个本地站点的 VPN 网关和其他虚拟网络。

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>能否将使用 IPsec VPN 的虚拟网络连接到我的 ExpressRoute 线路？

能，系统支持该操作。 有关详细信息，请参阅 [配置可共存的 ExpressRoute 连接和站点到站点 VPN 连接](../expressroute/expressroute-howto-coexist-classic.md)。

## <a name="ipsecike"></a>IPsec/IKE 策略

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="bgp"></a>BGP

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="vms"></a>跨界连接和 VM

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>如果虚拟机位于虚拟网络中，而连接是跨界连接，应如何连接到该 VM？

有几个选择。 如果为 VM 启用了 RDP，则可使用专用 IP 地址连接到虚拟机。 在这种情况下，需要指定要连接到的专用 IP 地址和端口（通常为 3389）。 需要配置用于流量的虚拟机端口。

也可以使用位于同一虚拟网络中的另一个虚拟机的专用 IP 地址连接到虚拟机。 如果要从虚拟网络外部的位置进行连接，则无法使用专用 IP 地址 RDP 到虚拟机。 例如，如果配置了点到站点虚拟网络，并且未从计算机建立连接，则无法通过专用 IP 地址连接到虚拟机。

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>如果我的虚拟机位于使用跨界连接的虚拟网络中，从我的 VM 流出的所有流量是否都会经过该连接？

不。 只有其目标 IP 包含在指定虚拟网络本地网络 IP 地址范围内的流量才会通过虚拟网络网关。 流量有一个目标 IP 位于虚拟网络中，并将保留在虚拟网络中。 其他流量通过负载均衡器发送到公共网络，或者在使用强制隧道的情况下通过 Azure VPN 网关发送。

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>如何排查到 VM 的 RDP 连接的问题

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]


## <a name="faq"></a>虚拟网络常见问题解答

请在 [虚拟网络常见问题](../virtual-network/virtual-networks-faq.md)中查看更多虚拟网络信息。

## <a name="next-steps"></a>后续步骤

* 有关 VPN 网关的详细信息，请参阅[关于 VPN 网关](vpn-gateway-about-vpngateways.md)。
* 有关 VPN 网关配置设置的详细信息，请参阅[关于 VPN 网关配置设置](vpn-gateway-about-vpn-gateway-settings.md)。

**"OpenVPN"是 OpenVPN Inc.的商标**
