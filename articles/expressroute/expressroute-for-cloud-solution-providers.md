---
title: 适用于云解决方案提供商的 ExpressRoute - Azure | Microsoft Docs
description: 本文提供的信息适用于想要将 Azure 服务和 ExpressRoute 纳入到产品/服务中去的云解决方案提供商。
services: expressroute
author: richcar
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: ricarlse
ms.custom: seodec18
ms.openlocfilehash: a3bd48f32dfcee1a666ff842cfcab2384a5459ec
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849267"
---
# <a name="expressroute-for-cloud-solution-providers-csp"></a>适用于云解决方案提供商 (CSP) 的 ExpressRoute
Microsoft 为传统经销商和分销商 (CSP) 提供超大规模的服务，允许他们为客户快速预配新服务和解决方案，而不需投资开发这些新服务。 为了让云解决方案提供商 (CSP) 能够直接管理这些新服务，Microsoft 提供了相应的程序和 API，以便 CSP 代表客户管理 Microsoft Azure 资源。 其中一项资源就是 ExpressRoute。 ExpressRoute 允许 CSP 将现有客户资源连接到 Azure 服务。 ExpressRoute 是一种高速专用通信链路，可以链接到 Azure 中的服务。 

ExpressRoute 由一对可以实现高可用性的线路组成，这对线路连接到单个客户订阅，不能由多个客户共享。 每个线路都会在不同的路由器中终止，目的是维持高可用性。

> [!NOTE]
> ExpressRoute 存在带宽和连接限制，也就是说，进行大型/复杂的实施时，需要为单个客户提供多个 ExpressRoute 线路。
> 
> 

Microsoft Azure 提供越来越多的服务，可以将这些服务提供给客户。 ExpressRoute 允许对 Microsoft Azure 环境进行高速、低延迟访问，有助于你和你的客户充分利用这些服务。

## <a name="microsoft-azure-management"></a>Microsoft Azure 管理
Microsoft 为 CSP 提供管理 Azure 客户订阅所需的 API，允许通过编程方式与自己的服务管理系统集成。 可在 [此处](https://msdn.microsoft.com/library/partnercenter/dn974944.aspx)找到受支持的管理功能。

## <a name="microsoft-azure-resource-management"></a>Microsoft Azure 资源管理
订阅管理方式将取决于你与客户签署的协定。 可以由 CSP 直接管理资源的创建和维护，也可以始终由客户对 Microsoft Azure 订阅进行控制，并根据需要来创建 Azure 资源。 如果客户在其 Microsoft Azure 订阅中管理资源的创建，他们会使用以下两种模型之一：“Connect-Through”模型或“Direct-To”模型。   将在下面各节中详细介绍这些模型。  

### <a name="connect-through-model"></a>Connect-Through 模型
![替换文字](./media/expressroute-for-cloud-solution-providers/connect-through.png)  

在 Connect-Through 模型中，CSP 在数据中心和客户的 Azure 订阅之间创建直接连接。 使用 ExpressRoute 进行直接连接，将网络与 Azure 相连。 然后，客户再连接到网络。 此方案要求客户通过 CSP 网络来访问 Azure 服务。 

如果客户有其他不由你管理的 Azure 订阅，他们会使用公共 Internet 或自己的专用连接来连接到这些在非 CSP 订阅下预配的服务。 

对于管理 Azure 服务的 CSP 来说，所作的假定是该 CSP 有一个以前建立的客户标识存储，该存储随后会复制到 Azure Active Directory 中，以便通过 Administrate-On-Behalf-Of (AOBO) 对其 CSP 订阅进行管理。 此方案的关键驱动因素包括：既定的合作伙伴或服务提供商已建立与客户的合作关系、客户目前正使用提供商的服务，或者合作伙伴希望提供包括提供商托管型解决方案和 Azure 托管型解决方案在内的组合型解决方案，以便增加灵活性，并解决客户遇到的无法单独通过 CSP 来解决的挑战。 此模型见下 **图**。

![替换文字](./media/expressroute-for-cloud-solution-providers/connect-through-model.png)

### <a name="connect-to-model"></a>Connect-To 模型
![替换文字](./media/expressroute-for-cloud-solution-providers/connect-to.png)

在 Connect-To 模型中，服务提供商使用基于客户网络的 ExpressRoute 在其客户的数据中心和 CSP 预配的 Azure 订阅之间创建直接连接。

> [!NOTE]
> 就 ExpressRoute 来说，客户需创建和维护 ExpressRoute 线路。  
> 
> 

此连接方案要求客户在访问由 CSP 管理的 Azure 订阅时通过客户网络直接进行连接，使用全部或部分由客户创建、拥有和管理的直接网络连接。 对于这些客户来说，所作的假定是提供商目前并没有建立客户标识存储，而且提供商会帮助客户将当前的标识存储复制到 Azure Active Directory 中，以便通过 AOBO 管理其订阅。 此方案的关键驱动因素包括：既定的合作伙伴或服务提供商已建立与客户的合作关系、客户目前正使用提供商的服务，或者合作伙伴希望提供完全基于 Azure 托管型解决方案的服务，而不需要使用现有的提供商数据中心或基础结构。

![替换文字](./media/expressroute-for-cloud-solution-providers/connect-to-model.png)

如何选择这两种模式取决于客户的需求，以及目前提供 Azure 服务的具体需要。 有关这些模型的详细信息以及关联的基于角色的访问控制、网络和标识设计模式在以下链接中有详尽介绍：

* **基于角色的访问控制 (RBAC)** – RBAC 基于 Azure Active Directory。  有关 Azure RBAC 的详细信息，请参阅 [此处](../role-based-access-control/role-assignments-portal.md)。
* **网络** – 介绍有关 Microsoft Azure 中网络的各种主题。
* **Azure Active Directory (Azure AD)** – Azure AD 提供针对 Microsoft Azure 和第三方 SaaS 应用程序的标识管理。 有关 Azure AD 的详细信息，请参阅 [此处](https://azure.microsoft.com/documentation/services/active-directory/)。  

## <a name="network-speeds"></a>网络速度
ExpressRoute 支持的网络速度其范围为 50 Mb/秒到 10Gb/秒。 因此，客户可以根据其具体环境购买所需的网络带宽。

> [!NOTE]
> 网络带宽可以在不中断通信的情况下，根据需要进行提升，但要降低网络速度，则需先拆卸线路，然后按较低的网络速度重新创建。  
> 
> 

ExpressRoute 支持将多个 vNet 连接到单个 ExpressRoute 线路，以便更好地利用速度更高的连接。 单个 ExpressRoute 线路可以在同一客户拥有的多个 Azure 订阅之间共享。

## <a name="configuring-expressroute"></a>配置 ExpressRoute
可以将 ExpressRoute 配置为在单个 ExpressRoute 线路上支持三种类型的流量（[路由域](#expressroute-routing-domains)）。 该流量可分成 Microsoft 对等互连、Azure 公共对等互连和专用对等互连。 可以选择一种类型的或所有类型的需通过单个 ExpressRoute 线路发送的流量，也可以使用多个 ExpressRoute 线路，具体取决于 ExpressRoute 线路的大小以及客户的隔离要求。 客户所面临的安全状况可能不允许公共流量和专用流量经过相同的线路。

### <a name="connect-through-model"></a>Connect-Through 模型
在 Connect-Through 配置中，需要负责所有网络基础结构，确保将客户数据中心资源连接到 Azure 中托管的订阅。 每个想要使用 Azure 功能的客户都需要建立自己的 ExpressRoute 连接，由你进行管理。 将使用客户所用的相同方法来采购 ExpressRoute 线路。 按照适用于线路预配和线路状态的 [ExpressRoute 工作流](expressroute-workflows.md)一文中概述的相同步骤进行操作。 然后配置边界网关协议 (BGP) 路由，控制在本地网络和 Azure vNet 之间的流量。

### <a name="connect-to-model"></a>Connect-To 模型
在 Connect-To 配置中，客户已经建立了到 Azure 的连接，或者会启动一个到 Internet 服务提供商的连接，将 ExpressRoute 从你客户自己的数据中心直接链接到 Azure 而不是数据中心。 客户将遵循上述 Connect-Through 模型中描述的步骤来开始预配过程。 建立线路以后，客户需先配置本地路由器，才能访问你的网络和 Azure vNet。

你可以帮助设置连接和配置路由，以允许你的数据中心的资源与数据中心的客户端资源或者在 Azure 中托管的资源进行通信。

## <a name="expressroute-routing-domains"></a>ExpressRoute 路由域
ExpressRoute 提供三种路由域：公共对等互连、专用对等互连和 Microsoft 对等互连。 在主动-主动配置中，每个路由域都配置了相同的路由器，以确保高可用性。 有关 ExpressRoute 路由域的详细信息，请查看[此处](expressroute-circuit-peerings.md)。

可以自定义路由筛选器，根据需要来允许相关路由。 如需详细信息，或者需要了解如何进行此类更改，请参阅详细介绍路由筛选器的以下文章：[使用 PowerShell 创建和修改 ExpressRoute 线路的路由](expressroute-howto-routing-classic.md)。

> [!NOTE]
> 对于 Microsoft 对等互连和公共对等互连，必须通过客户或 CSP 拥有的公共 IP 地址进行连接，并且必须遵循所有定义的规则。 有关详细信息，请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md) 页。  
> 
> 

## <a name="routing"></a>路由
ExpressRoute 通过 Azure 虚拟网络网关连接到 Azure 网络。 网络网关为 Azure 虚拟网络提供路由功能。

创建 Azure 虚拟网络时，还会为 vNet 创建默认的路由表，以便引导进出 vNet 子网的流量。 如果默认路由表对解决方案来说不够用，则可创建自定义路由，以便将传出流量路由到自定义设备，或者阻止到特定子网或外部网络的路由。

### <a name="default-routing"></a>默认路由
默认路由表包含以下路由：

* 在子网内路由
* 在虚拟网络中从子网到子网路由
* 到 Internet
* 从虚拟网络到虚拟网络的路由，使用 VPN 网关
* 从虚拟网络到本地网络的路由，使用 VPN 或 ExpressRoute 网关

![替换文字](./media/expressroute-for-cloud-solution-providers/default-routing.png)  

### <a name="user-defined-routing-udr"></a>用户定义的路由 (UDR)
使用用户定义的路由，你可以控制虚拟网络中从分配的子网到其他子网的出站流量，或者控制经过其他某个预定义网关（ExpressRoute、Internet 或 VPN）的出站流量。 可以将默认的系统路由表替换为用户定义的路由表，以便将默认路由表替换为自定义路由。 使用用户定义的路由，客户可以创建到某些设备（例如防火墙或入侵检测设备）的特定路由，或者阻止他人从托管用户定义的路由的子网访问特定的子网。 有关用户定义的路由的概述，请查看 [此处](../virtual-network/virtual-networks-udr-overview.md)。 

## <a name="security"></a>安全性
根据所用的模型（Connect-To 或 Connect-Through），客户可在其 vNet 中定义安全策略，或者向 CSP 提供针对其 vNet 进行定义时的安全策略要求。 可以定义以下安全标准：

1. **客户隔离** — Azure 平台通过将客户 ID 和 vNet 信息存储在安全的数据库中，将每个客户的流量封装在 GRE 隧道中，从而实现客户隔离。
2. **网络安全组 (NSG)** 规则用于在 Azure 的 vNet 中定义允许进出子网的流量。 默认情况下，NSG 包含的“阻止”规则将阻止从 Internet 到 vNet 的流量，包含的“允许”规则将允许 vNet 内部的流量。 有关网络安全组的详细信息，请查看 [此处](https://azure.microsoft.com/blog/network-security-groups/)。
3. **强制隧道** — 此选项可将源自 Azure 的面向 Internet 的流量通过 ExpressRoute 连接重定向到本地数据中心。 有关强制隧道的详细信息，请查看[此处](expressroute-routing.md#advertising-default-routes)。  
4. **加密** — 虽然 ExpressRoute 线路专用于特定客户，但也可以使用该线路访问网络提供商，这会导致入侵者查看数据包流量。 为了解决这种可能存在的问题，可以让客户或 CSP 加密连接中的流量，即为本地资源和 Azure 资源之间的所有流量定义 IPSec 隧道模式策略（请参阅图 5 上方针对客户 1 的可选“隧道”模式 IPSec：ExpressRoute 安全性）。 第二个选项是在 ExpressRoute 线路的每个终结点处使用防火墙设备。 这需要在两端安装其他的第三方防火墙 VM/设备，以便加密 ExpressRoute 线路上的流量。

![替换文字](./media/expressroute-for-cloud-solution-providers/expressroute-security.png)  

## <a name="next-steps"></a>后续步骤
使用云解决方案提供商服务，可以在不需要购买昂贵的基础结构和功能的情况下，提升你对客户的价值，让你一直充当主要的外包提供商。 与 Microsoft Azure 的无缝集成可以通过 CSP API 来完成，这样即可在现有的管理框架内集成 Microsoft Azure 的管理。  

如需更多信息，可单击以下链接：

[云解决方案提供商计划中的 Azure](https://docs.microsoft.com/azure/cloud-solution-provider)。  
[做好以云解决方案提供商身份进行事务处理的准备](https://partner.microsoft.com/en-us/solutions/cloud-reseller-pre-launch)。  
[Microsoft 云解决方案提供商资源](https://partner.microsoft.com/en-us/solutions/cloud-reseller-resources)。
