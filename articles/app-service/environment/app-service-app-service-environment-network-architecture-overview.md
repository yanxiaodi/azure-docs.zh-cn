---
title: 应用服务环境的网络体系结构概述 - Azure
description: 应用服务网络拓扑的体系结构概述。
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 13d03a37-1fe2-4e3e-9d57-46dfb330ba52
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 98eb4d7440126bedb3d2e1de5711141eaac8b07a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070071"
---
# <a name="network-architecture-overview-of-app-service-environments"></a>应用服务环境的网络体系结构概述
## <a name="introduction"></a>简介
始终在[虚拟网络][virtualnetwork]的子网中创建应用服务环境-在应用服务环境中运行的应用可以与位于同一虚拟网络拓扑中的专用终结点通信。  由于客户可能锁定了其虚拟网络基础结构的组件，因此请务必了解与应用服务环境发生的网络通信流类型。

## <a name="general-network-flow"></a>常规网络流
当应用服务环境 (ASE) 将公共虚拟 IP 地址 (VIP) 用于应用时，所有入站流量都将到达该公共 VIP。  包括应用的 HTTP 和 HTTPS 流量，以及 FTP、远程调试功能和 Azure 管理操作的其他流量。  有关公共 VIP 上可用特定端口 (必需和可选) 的完整列表, 请参阅有关控制到应用服务环境的[入站流量][controllinginboundtraffic]的文章。 

应用服务环境还支持运行只绑定到一个虚拟网络内部地址（也称为 ILB，即内部负载均衡器）地址的应用。  在启用 ILB 的 ASE 上，应用和远程调试调用的 HTTP 和 HTTPS 流量都将到达 ILB 地址。  对于最常见的 ILB-ASE 配置，FTP/FTPS 流量也将到达 ILB 地址。  但是 Azure 管理操作仍将流传输到启用 ILB 的 ASE 的公共 VIP 上的端口 454/455。

下图显示了一个应用服务环境的各种入站和出站网络流的概述，此环境中应用绑定到公共虚拟 IP 地址：

![常规网络流][GeneralNetworkFlows]

应用服务环境可与各种专用客户终结点进行通信。  例如，在应用服务环境中运行的应用可以连接到在相同虚拟网络拓扑中的 IaaS 虚拟机上运行的数据库服务器。

> [!IMPORTANT]
> 请查看网络图，“其他计算资源”部署在与应用服务环境不同的子网中。 将资源部署于和 ASE 相同的子网中，阻止从 ASE 连接到这些资源的连接（除了特定的内部 ASE 路由之外）。 请改为部署到（相同 VNET 中）不同的子网。 然后，应用服务环境将能够连接。 不需要任何其他配置。
> 
> 

应用服务环境还能与管理和操作应用服务环境所需的 Sql DB 与 Azure 存储资源进行通信。  与应用服务环境通信的一些 SQL 和存储资源位于与应用服务环境相同的区域中，有些则位于远程 Azure 区域中。  因此，只有与 Internet 建立了出站连接，应用服务环境才能正常工作。 

由于应用服务环境是在子网中部署的，因此可以使用网络安全组来控制发往子网的入站流量。  有关如何控制到应用服务环境的入站流量的详细信息, 请参阅以下[文章][controllinginboundtraffic]。

有关如何允许来自应用服务环境的出站 Internet 连接的详细信息, 请参阅以下有关使用[Express Route][ExpressRoute]的文章。  此文章所述的方法同样适用于使用站点到站点连接以及使用强制隧道的情况。

## <a name="outbound-network-addresses"></a>出站网络地址
应用服务环境执行出站调用时，IP 地址始终与出站调用相关联。  使用的特定 IP 地址取决于所调用的终结点是位于虚拟网络拓扑内部还是外部。

如果调用的终结点在虚拟网络拓扑**外部**，则使用的出站地址（也称为出站 NAT 地址）是应用服务环境的公共 VIP。  可以在应用服务环境的门户用户界面上的“属性”边栏选项卡中找到此地址。

![出站 IP 地址][OutboundIPAddress]

对于只有公共 VIP 的 ASE，也可以通过在应用服务环境中创建一个应用，并对该应用的地址执行 *nslookup*，来确定此地址。 最终的 IP 地址既是公共 VIP，也是应用服务环境的出站 NAT 地址。

如果调用的终结点在虚拟网络拓扑**内部**，则调用端应用的出站地址是运行应用的单个计算资源的内部 IP 地址。  但是，虚拟网络内部 IP 地址与应用之间不存在持久性的映射。  应用可以在不同的计算资源之间移动，并且可以基于缩放操作更改应用服务环境中的可用计算资源池。

但是，由于应用服务环境始终位在子网内，可以保证运行应用的计算资源的内部 IP 地址始终处于子网的 CIDR 范围内。  因此，使用精细 ACL 或网络安全组来保护虚拟网络中其他终结点的访问时，需要将访问权限授予包含应用服务环境的子网范围。

下图更详细地演示了这些概念：

![出站网络地址][OutboundNetworkAddresses]

在上图中：

* 由于应用服务环境的公共 VIP 是 192.23.1.2，因此这是调用“Internet”终结点时使用的出站 IP 地址。
* 应用服务环境的包含子网的 CIDR 范围是 10.0.1.0/26。  同一虚拟网络基础结构中的其他终结点将看到源自此地址范围内某个应用的调用。

## <a name="calls-between-app-service-environments"></a>在应用服务环境之间调用
如果在相同的虚拟网络中部署多个应用服务环境，并从一个应用服务环境传出调用到另一个应用服务环境，则可能会出现更复杂的情景。  这些跨应用服务环境的调用也被视为“Internet”调用。

下图显示了一个分层体系结构示例，其中应用在一个应用服务环境上（例如“前端”Web 应用）调用第二个应用服务环境上的应用（例如：内部后端 API 应用不需要可从 Internet 访问）。 

![在应用服务环境之间调用][CallsBetweenAppServiceEnvironments] 

在上述示例中，应用服务环境“ASE One”有一个出站 IP 地址 192.23.1.2。  如果应用服务环境中运行的应用，对位于同一虚拟网络中第二个应用服务环境（“ASE Two”）上运行的应用进行出站调用，则此出站调用将视为“Internet”调用。  因此，到达第二个应用服务环境的网络流量将来源显示为来自 192.23.1.2（即，不是第一个应用服务环境的子网地址范围）。

即使不同应用服务环境之间的调用视为“Internet”调用，当两个应用服务环境同时位于相同的 Azure 区域时，网络流量将保留在区域性 Azure 网络上，而不实际在公共 Internet 上流动。  因此，可以使用第二个应用服务环境的子网上的网络安全组，仅允许来自第一个应用服务环境（其出站 IP 地址为 192.23.1.2）的入站调用，从而确保应用服务环境之间的通信安全。

## <a name="additional-links-and-information"></a>其他链接和信息
[此处][controllinginboundtraffic]提供了有关应用服务环境使用的入站端口以及使用网络安全组控制入站流量的详细信息。

[本文][ExpressRoute]提供了有关使用用户定义路由来授予对应用服务环境的出站 Internet 访问权限的详细信息。 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[ExpressRoute]:  app-service-app-service-environment-network-configuration-expressroute.md

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

