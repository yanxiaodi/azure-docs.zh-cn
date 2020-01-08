---
title: 路由要求 - ExpressRoute：Azure | Microsoft Docs
description: 本页提供有关为 ExpressRoute 线路配置和管理路由的详细要求。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9bc0d1b31ebeaecce8b4be8699cf87811047b6f9
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123259"
---
# <a name="expressroute-routing-requirements"></a>ExpressRoute 路由要求
若要使用 ExpressRoute 连接到 Microsoft 云服务，需要设置并管理路由。 某些连接服务提供商以托管服务形式提供路由的设置和管理。 请咨询连接服务提供商，以确定他们是否提供此类服务。 如果不提供，则必须遵守以下要求：

有关需要在设置后才能建立连接的路由会话的说明，请参阅[线路和路由域](expressroute-circuit-peerings.md)一文。

> [!NOTE]
> Microsoft 不支持任何高可用性配置的路由器冗余协议（例如 HSRP 和 VRRP）。 我们依赖每个对等互连的一组冗余 BGP 会话来获得高可用性。
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>用于对等互连的 IP 地址
用户需要保留一些 IP 地址，用于配置网络与 Microsoft Enterprise Edge (MSEE) 路由器之间的路由。 本部分提供了要求列表，并介绍有关如何获取和使用这些 IP 地址的规则。

### <a name="ip-addresses-used-for-azure-private-peering"></a>用于 Azure 专用对等互连的 IP 地址
可以使用专用 IP 地址或公共 IP 地址配置对等互连。 用于配置路由的地址范围不得与用于在 Azure 中创建虚拟网络的地址范围重叠。 

* 必须为路由接口保留一个 /29 子网或两个 /30 子网。
* 用于路由的子网可以是专用 IP 地址或公共 IP 地址。
* 子网不得与客户保留用于 Microsoft 云的范围冲突。
* 如果使用 /29 子网，它将拆分成两个 /30 子网。 
  * 第一个 /30 子网用于主链路，第二个 /30 子网用于辅助链路。
  * 对于每个 /30 子网，必须在路由器上使用 /30 子网的第一个 IP 地址。 Microsoft 使用 /30 子网的第二个 IP 地址设置 BGP 会话。
  * 只有设置两个 BGP 会话，我们的 [可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 才有效。  

#### <a name="example-for-private-peering"></a>专用对等互连示例
如果选择使用 a.b.c.d/29 设置对等互连，它将拆分成两个 /30 子网。 在以下示例中，请注意 a.b.c.d/29 子网的用法：

* a.b.c.d/29 拆分成 a.b.c.d/30 和 a.b.c.d+4/30 并通过预配 API 一路传递到 Microsoft。
  * 请使用 a.b.c.d+1 作为主要 PE 的 VRF IP，而 Microsoft 将使用 a.b.c.d+2 作为主要 MSEE 的 VRF IP。
  * 请使用 b.c.d+5 作为辅助 PE 的 VRF IP，而 Microsoft 将使用 a.b.c.d+6 作为辅助 MSEE 的 VRF IP。

假设选择 192.168.100.128/29 设置专用对等互连。 192.168.100.128/29 包括从 192.168.100.128 到 192.168.100.135 的地址，其中：

* 192.168.100.128/30 将分配给 link1（提供商使用 192.168.100.129，而 Microsoft 使用 192.168.100.130）。
* 192.168.100.132/30 将分配给 link2（提供商使用 192.168.100.133，而 Microsoft 使用 192.168.100.134）。

### <a name="ip-addresses-used-for-microsoft-peering"></a>用于 Microsoft 对等互连的 IP 地址
必须使用自己的公共 IP 地址来设置 BGP 会话。 Microsoft 必须能够通过路由 Internet 注册表和 Internet 路由注册表来验证 IP 地址的所有权。

* 门户中列出的与 Microsoft 对等互连的已播发公共前缀相对应的 IP 将为 Microsoft 核心路由器创建 ACL，目的是允许来自这些 IP 的入站流量。 
* 必须使用一个唯一的 /29 (IPv4) 或 /125 (IPv6) 子网或两个 /30 (IPv4) 或 /126 (IPv6) 子网为每条 ExpressRoute 线路（如果有多个）的每个对等互连设置 BGP 对等互连。
* 如果使用 /29 子网，它将拆分成两个 /30 子网。
* 第一个 /30 子网用于主链路，第二个 /30 子网将用于辅助链路。
* 对于每个 /30 子网，必须在路由器上使用 /30 子网的第一个 IP 地址。 Microsoft 使用 /30 子网的第二个 IP 地址设置 BGP 会话。
* 如果使用 /125 子网，它将拆分成两个 /126 子网。
* 第一个 /126 子网用于主链路，第二个 /126 子网将用于辅助链路。
* 对于每个 /126 子网，必须在路由器上使用 /126 子网的第一个 IP 地址。 Microsoft 使用 /126 子网的第二个 IP 地址设置 BGP 会话。
* 只有设置两个 BGP 会话，我们的 [可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 才有效。

### <a name="ip-addresses-used-for-azure-public-peering"></a>用于 Azure 公共对等互连的 IP 地址

> [!NOTE]
> Azure 公共对等互连不适用于新线路。
> 

必须使用自己的公共 IP 地址来设置 BGP 会话。 Microsoft 必须能够通过路由 Internet 注册表和 Internet 路由注册表来验证 IP 地址的所有权。 

* 必须使用一个唯一的 /29 子网或两个 /30 子网为每条 ExpressRoute 线路（如果有多个）的每个对等互连设置 BGP 对等互连。 
* 如果使用 /29 子网，它将拆分成两个 /30 子网。 
  * 第一个 /30 子网用于主链路，第二个 /30 子网用于辅助链路。
  * 对于每个 /30 子网，必须在路由器上使用 /30 子网的第一个 IP 地址。 Microsoft 使用 /30 子网的第二个 IP 地址设置 BGP 会话。
  * 只有设置两个 BGP 会话，我们的 [可用性 SLA](https://azure.microsoft.com/support/legal/sla/) 才有效。

## <a name="public-ip-address-requirement"></a>公共 IP 地址要求

### <a name="private-peering"></a>专用对等互连
进行专用对等互连时，可以选择使用公共的或专用的 IPv4 地址。 我们会对用户的流量进行端到端隔离，因此在进行专用对等互连时，不可能出现与其他客户的地址发生重叠的情况。 这些地址不会播发到 Internet。 

### <a name="microsoft-peering"></a>Microsoft 对等
可以使用 Microsoft 对等互连路径连接到 Microsoft 云服务。 服务列表包括 Office 365 服务，例如 Exchange Online、SharePoint Online、Skype for Business 和 Microsoft 团队。 Microsoft 支持在 Microsoft 对等互连时进行双向连接。 定向到 Microsoft 云服务的流量必须使用有效的公共 IPv4 地址才能进入 Microsoft 网络。

确保 IP 地址和 AS 号码已在下列其中一个注册表中注册：

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

如果没有在前述注册表中为你分配前缀和 AS 编号，需开立一个支持案例，以便手动验证前缀和 ASN。 支持需要文档，例如证明你有权使用相关资源的授权书。

专用 AS 编号可以用于 Microsoft 对等互连，但也需手动验证。 此外，对于收到的前缀，我们会删除 AS PATH 中的专用 AS 数字。 因此，无法在 AS PATH 中追加专用 AS 数字来[影响 Microsoft 对等互连的路由](expressroute-optimize-routing.md)。 

> [!IMPORTANT]
> 不要将相同的公共 IP 路由播发到公共 Internet 和通过 ExpressRoute 播发。 为了降低错误配置导致不对称路由的风险，我们强烈建议通过 ExpressRoute 播发到 Microsoft 的 [NAT IP 地址](expressroute-nat.md)应该来自完全没有播发到 Internet 的范围。 如果无法实现这一点，则必须确保通过 ExpressRoute 播发的范围比 Internet 连接上的范围更具体。 除了要进行 NAT 的公共路由外，还可以在本地网络中通过 ExpressRoute 播发与 Microsoft 中的 Office 365 终结点通信的服务器使用的公共 IP 地址。 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>公共对等互连（已弃用 - 不适用于新线路）
Azure 公共对等互连路径用于连接到托管于 Azure 中的所有服务的公共 IP 地址。 其中包括 [ExpessRoute 常见问题](expressroute-faqs.md) 中列出的服务以及由 Microsoft Azure 上的 ISV 托管的任何服务。 始终从用户网络向 Microsoft 网络发起与公共对等互连中 Microsoft Azure 服务的连接。 必须使用公共 IP 地址才能将流量发往 Microsoft 网络。

> [!IMPORTANT]
> 所有 Azure PaaS 服务可通过 Microsoft 对等互连访问。
>   

公共对等互连允许使用专用 AS 编号。

## <a name="dynamic-route-exchange"></a>动态路由交换
路由交换将通过 eBGP 协议进行。 EBGP 会话在 MSEE 与路由器之间建立。 不要求对 BGP 会话进行身份验证。 如果需要，可以配置 MD5 哈希。 有关配置 BGP 会话的信息，请参阅[配置路由](how-to-routefilter-portal.md)及[线路预配工作流和线路状态](expressroute-workflows.md)。

## <a name="autonomous-system-numbers"></a>自治系统编号
Microsoft 使用 AS 12076 进行 Azure 公共、Azure 专用和 Microsoft 对等互连。 我们保留了 ASN 65515-65520 供内部使用。 支持 16 和 32 位 AS 编号。

数据传输对称没有相关要求。 转发与返回路径可以遍历不同的路由器对。 相同的路由必须从属于你的多个线路对的任何一端播发。 路由指标不需要完全相同。

## <a name="route-aggregation-and-prefix-limits"></a>路由聚合与前缀限制
我们支持通过 Azure 专用对等互连向我们播发最多 4000 个前缀。 如果已启用 ExpressRoute 高级版附加组件，则可增加到 10,000 个前缀。 我们接受为每个 BGP 会话最多使用 200 个前缀建立 Azure 公共和 Microsoft 对等互连。 

如果前缀数目超过此限制，将丢弃 BGP 会话。 我们只接受专用对等互连链路上的默认路由。 提供商必须从 Azure 公共和 Microsoft 对等互连路径中筛选出默认路由和专用 IP 地址 (RFC 1918)。 

## <a name="transit-routing-and-cross-region-routing"></a>传输路由和跨区域路由
ExpressRoute 不能配置为传输路由器。 必须依赖连接服务提供商的传输路由服务。

## <a name="advertising-default-routes"></a>播发默认路由
只有 Azure 专用对等互连会话允许默认路由。 在这种情况下，我们将所有流量从关联的虚拟网络路由到网络。 在专用对等互连中播发默认路由会导致来自 Azure 的 Internet 路径遭到阻止。 必须依赖企业网络边缘，为 Azure 中托管的服务往返路由 Internet 的流量。 

 要连接其他 Azure 服务和基础结构服务，必须确保已准备好下列其中一项：

* 已启用 Azure 公共对等互连，以将流量路由到公共终结点。
* 使用用户定义的路由，为需要 Internet 连接的每个子网建立 Internet 连接。

> [!NOTE]
> 播发默认路由会中断 Windows 和其他 VM 许可证激活。 请按照 [此处](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) 的说明来解决此问题。
> 
> 

## <a name="bgp"></a>BGP 社区支持
本部分概述如何配合 ExpressRoute 使用 BGP 社区。 Microsoft 将播发公共和 Microsoft 对等互连路径中的路由并为路由标记适当的社区值。 下面会介绍这种方案的理由以及有关社区值的详细信息。 但是，Microsoft 不遵循向 Microsoft 播发的路由的任何标记社区值。

如果要在某个地缘政治区域内的任何一个对等互连位置通过 ExpressRoute 连接到 Microsoft，必须能够访问该地缘政治边界内所有区域中的所有 Microsoft 云服务。 

例如，如果在阿姆斯特丹通过 ExpressRoute 连接到 Microsoft，则能够访问在北欧和西欧托管的所有 Microsoft 云服务。 

有关地缘政治地区、关联的 Azure 区域和对应的 ExpressRoute 对等互连位置的详细列表，请参阅 [ExpressRoute 合作伙伴和对等位置](expressroute-locations.md) 。

可以针对每个地缘政治区域购买多个 ExpressRoute 线路。 如果拥有多个连接，则可以从异地冗余中获得明显的高可用性优势。 如果具有多条 ExpressRoute 线路，将在 Microsoft 对等互连和公共对等互连路径收到 Microsoft 播发的同一组前缀。 这意味着可以使用多个路径从网络接入 Microsoft。 这可能会导致在网络中做出欠佳的路由决策。 因此，可能会在不同的服务上遇到欠佳的连接体验。 可以依赖社区值做出适当的路由决策，[向用户提供最佳路由](expressroute-optimize-routing.md)。

| **Microsoft Azure 区域** | **区域 BGP 社区** | **存储 BGP 社区** | **SQL BGP 社区** | **Cosmos DB BGP 社区** |
| --- | --- | --- | --- | --- |
| **北美** | |
| 美国东部 | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 |
| 美国东部 2 | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 |
| 美国西部 | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 |
| 美国西部 2 | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 |
| 美国中西部 | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 |
| 美国中北部 | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 |
| 美国中南部 | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 |
| 美国中部 | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 |
| 加拿大中部 | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 |
| 加拿大东部 | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 |
| **南美洲** | |
| 巴西南部 | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 |
| **欧洲** | |
| 北欧 | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 |
| 西欧 | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 |
| 英国南部 | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 |
| 英国西部 | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 |
| 法国中部 | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 |
| 法国南部 | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 |
| **亚太区** | |
| 东亚 | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 |
| 东南亚 | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 |
| **日本** | |
| 日本东部 | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 |
| 日本西部 | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 |
| **澳大利亚** | |
| 澳大利亚东部 | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 |
| 澳大利亚东南部 | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 |
| **澳大利亚政府** | |
| 澳大利亚中部 | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 |
| 澳大利亚中部 2 | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 |
| **印度** | |
| 印度南部 | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 |
| 印度西部 | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 |
| 印度中部 | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 |
| **韩国** | |
| 韩国南部 | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 |
| 韩国中部 | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 |
| **南非**| |
| 南非北部 | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 |
| 南非西部 | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 |
| **阿拉伯联合酋长国**| |
| 阿拉伯联合酋长国北部 | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 |
| 阿拉伯联合酋长国中部 | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 |


所有 Microsoft 播发的路由都标有适当的社区值。 

> [!IMPORTANT]
> 全局前缀将使用相应的社区值进行标记。
> 
> 

### <a name="service-to-bgp-community-value"></a>服务到 BGP 团体值
除了上述各项，Microsoft 还会根据其所属的服务加上标记及前缀。 这只适用于 Microsoft 对等互连。 下表提供了服务与 BGP 社区值之间的映射。 若要获取最新值的完整列表，可以运行 "AzBgpServiceCommunity" cmdlet。

| **服务** | **BGP 社区值** |
| --- | --- |
| Exchange Online | 12076:5010 |
| SharePoint Online | 12076:5020 |
| Skype For Business Online | 12076:5030 |
| Azure 全球服务* | 12076:5050 |
| 其他 Office 365 Online 服务 | 12076:5100 |

*本次 Azure 全球服务仅包括 Azure DevOps。


> [!NOTE]
> Microsoft 不遵循你在播发到 Microsoft 的路由上设置的任何 BGP 社区值。
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>区域云中的 BGP 社区支持

| **国家/地区云 Azure 区域**| **BGP 社区值** |
| --- | --- |
| **美国政府** |  |
| 美国亚利桑那州政府 | 12076:51106 |
| US Gov 爱荷华州 | 12076:51109 |
| 美国政府弗吉尼亚州 | 12076:51105 |
| 美国德克萨斯州政府 | 12076:51108 |
| 美国 DoD 中部 | 12076:51209 |
| 美国 DoD 东部 | 12076:51205 |


| **国家/地区云中的服务** | **BGP 社区值** |
| --- | --- |
| **美国政府** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype for Business Online |12076:5130 |
| 其他 Office 365 Online 服务 |12076:5200 |

## <a name="next-steps"></a>后续步骤
* 配置 ExpressRoute 连接。
  
  * [创建和修改线路](expressroute-howto-circuit-arm.md)
  * [创建和修改对等互连配置](expressroute-howto-routing-arm.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)
