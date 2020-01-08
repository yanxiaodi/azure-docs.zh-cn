---
title: 配置 ExpressRoute 线路的对等互连：Azure | Microsoft Docs
description: 本文介绍创建和预配 ExpressRoute 专用对等互连和 Microsoft 对等互连的步骤。 本文还演示了如何检查线路的状态、更新或删除线路的对等互连。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 08d8103c4b35148a87d347e31b11c7c8c968598b
ms.sourcegitcommit: dda9fc615db84e6849963b20e1dce74c9fe51821
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622335"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>创建和修改 ExpressRoute 线路的对等互连

本文可帮助你使用 Azure 门户为 Azure 资源管理器 (ARM) ExpressRoute 线路创建和管理路由配置。 还可以检查状态，以及更新、删除和取消预配 ExpressRoute 线路的对等互连。 如果想使用不同的方法处理线路，请从以下列表中选择一篇文章进行参阅：

> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [视频 - 专用对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [视频 - 公共对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [视频 - Microsoft 对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-routing-classic.md)
> 

你可以配置 Azure 专用和 Microsoft 对等互连的 ExpressRoute 线路 （Azure 公共对等互连不推荐使用适用于新线路）。 可以按照所选的任意顺序配置对等互连。 但是，必须确保一次只完成一个对等互连的配置。 有关路由域和对等互连的详细信息，请参阅[关于线路和对等互连](expressroute-circuit-peerings.md)。

## <a name="configuration-prerequisites"></a>配置先决条件

* 在开始配置之前，请务必查看[先决条件](expressroute-prerequisites.md)页、[路由要求](expressroute-routing.md)页和[工作流](expressroute-workflows.md)页。
* 必须有一个活动的 ExpressRoute 线路。 在继续下一步之前，请按说明 [创建 ExpressRoute 线路](expressroute-howto-circuit-portal-resource-manager.md) ，并通过连接提供商启用该线路。 若要配置对等互连，ExpressRoute 线路必须处于已预配且已启用状态。 
* 如果计划使用共享密钥/MD5 哈希，请确保在隧道两端都使用该哈希，并将最大字母数字字符数限制为 25。 不支持特殊字符。 

这些说明只适用于由提供第 2 层连接服务的服务提供商创建的线路。 如果服务提供商提供第 3 层托管服务（通常是 IPVPN，如 MPLS），则连接服务提供商会配置和管理路由。 

> [!IMPORTANT]
> 我们目前无法通过服务管理门户播发服务提供商配置的对等互连。 我们正在努力不久就实现这一功能。 请在配置 BGP 对等互连之前与服务提供商核对。
> 
> 

## <a name="msft"></a>Microsoft 对等互连

本文介绍如何为 ExpressRoute 线路创建、获取、更新和删除 Microsoft 对等互连配置。

> [!IMPORTANT]
> 在 2017 年 8 月 1 日之前配置的 ExpressRoute 线路的 Microsoft 对等互连会通过 Microsoft 对等互连播发所有服务前缀，即使未定义路由筛选器。 在 2017 年 8 月 1 日或之后配置的 ExpressRoute 线路的 Microsoft 对等互连的任何前缀只有在路由筛选器附加到线路之后才会播发。 有关详细信息，请参阅[配置用于 Microsoft 对等互连的路由筛选器](how-to-routefilter-powershell.md)。
> 
> 

### <a name="to-create-microsoft-peering"></a>创建 Microsoft 对等互连

1. 配置 ExpressRoute 线路。 检查**提供程序状态**以确保线路在进一步继续之前完全设置通过连接提供商。

   如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Microsoft 对等互连。 在这种情况下，不需要遵循后续部分中所列的说明。 但是，如果连接服务提供商不管理路由，在创建线路后继续执行这些步骤。

   **线路的提供程序状态：未预配**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "提供程序状态：未预配")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **线路的提供程序状态：预配**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "提供程序状态 = 已预配")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. 配置线路的 Microsoft 对等互连。 在继续下一步之前，请确保已准备好以下信息。

   * 主链路的 /30 子网。 这必须是你拥有的且已在 RIR/IRR 中注册的有效公共 IPv4 前缀。 在此子网中，Microsoft 将第二个可用的 IP 用于其路由器时，你将为你的路由器分配第一个可用的 IP 地址。
   * 辅助链路的 /30 子网。 这必须是你拥有的且已在 RIR/IRR 中注册的有效公共 IPv4 前缀。 在此子网中，Microsoft 将第二个可用的 IP 用于其路由器时，你将为你的路由器分配第一个可用的 IP 地址。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。 主要链接和次要链接必须使用相同的 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * 播发的前缀：必须提供要通过 BGP 会话播发的所有前缀列表。 只接受公共 IP 地址前缀。 如果打算发送一组前缀，可以发送逗号分隔列表。 这些前缀必须已在 RIR/IRR 中注册。
   * “可选”- 客户 ASN  ：如果要播发的前缀未注册到对等互连 AS 编号，可以指定它们要注册到的 AS 编号。
   * 路由注册表名称：可以指定 AS 编号和前缀要注册到的 RIR/IRR。
   * **可选** - MD5 哈希（如果选择使用）。
3. 可以选择想要配置的对等互连，如以下示例中所示。 选择 Microsoft 对等互连行。

   [![选择 Microsoft 对等互连行](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "选择 Microsoft 对等互连行")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. 配置 Microsoft 对等互连。 **保存**指定所有参数后的配置。 下图显示了一个示例配置：

   ![配置 Microsoft 对等互连](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

   如果线路到达需要验证的状态，则必须打开支持票证以显示我们的支持团队为前缀所有权的证明。 可以直接从门户中打开支持票证，如以下示例中所示：

   ![验证所需的支持票证](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. 已成功接受配置后，将看到类似于下图中的内容：

   ![对等互连状态：配置](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "对等互连状态：配置")]

### <a name="getmsft"></a>查看 Microsoft 对等互连详细信息

可以查看 Microsoft 对等互连选择的对等互连的行的属性。

[![查看 Microsoft 对等互连属性](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "查看属性")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>更新 Microsoft 对等互连配置

可以选择的对等互连，你想要修改，然后修改对等互连的属性并保存您的修改的行。

![选择对等互连行](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>删除 Microsoft 对等互连

下图中所示，可以通过单击删除图标，删除对等互连配置：

![删除对等互连](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Azure 专用对等互连

本文介绍如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 专用对等互连配置。

### <a name="to-create-azure-private-peering"></a>创建 Azure 专用对等互连

1. 配置 ExpressRoute 线路。 在继续之前，请确保线路完全由连接提供商设置。 

   如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Azure 专用对等互连。 在这种情况下，不需要遵循后续部分中所列的说明。 但是，如果连接提供商未为你管理路由，则在创建线路后，请继续执行后续步骤。

   **线路的提供程序状态：未预配**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "提供程序状态 = 未预配")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **线路的提供程序状态：预配**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "提供程序状态 = 预配")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. 配置线路的 Azure 专用对等互连。 在继续执行后续步骤之前，请确保已准备好以下各项：

   * 主链路的 /30 子网。 此子网不能是保留给虚拟网络使用的任何地址空间的一部分。 在此子网中，Microsoft 将第二个可用的 IP 用于其路由器时，你将为你的路由器分配第一个可用的 IP 地址。
   * 辅助链路的 /30 子网。 此子网不能是保留给虚拟网络使用的任何地址空间的一部分。 在此子网中，Microsoft 将第二个可用的 IP 用于其路由器时，你将为你的路由器分配第一个可用的 IP 地址。
   * 用于建立此对等互连的有效 VLAN ID。 请确保线路中没有其他对等互连使用同一个 VLAN ID。 主要链接和次要链接必须使用相同的 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。 可以使用专用 AS 编号建立对等互连（65515 到 65520 之间的数字除外）。
   * 设置专用对等互连时，必须播发从你的本地边缘路由器通过 BGP 的 azure 的路由。
   * **可选** - MD5 哈希（如果选择使用）。
3. 选择 Azure 专用对等互连行，如下面的示例中所示：

   [![选择专用对等互连行](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "选择专用对等互连行")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. 配置专用对等互连。 **保存**指定所有参数后的配置。

   ![配置专用对等互连](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. 成功接受配置后，会看到类似于以下示例的内容：

   ![保存专用对等互连](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>查看 Azure 专用对等互连详细信息

可以通过选择对等互连查看 Azure 专用对等互连的属性。

[![查看专用对等互连属性](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "查看专用对等互连属性")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>更新 Azure 专用对等互连配置

可以选择用于对等互连的行并修改对等互连属性。 在更新后，保存所做的更改。

![更新专用对等互连](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>删除 Azure 专用对等互连

可以通过选择删除图标来删除对等互连配置，如下图中所示：

> [!WARNING]
> 运行此示例前，必须确保已删除所有虚拟网络和 ExpressRoute Global Reach 连接。 
> 
> 

![删除专用对等互连](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Azure 公共对等互连

本文介绍如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 公共对等互连配置。

> [!Note]
> Azure 公共对等互连不推荐使用适用于新线路。 有关详细信息，请参阅[ExpressRoute 对等互连](expressroute-circuit-peerings.md)。
>

### <a name="getpublic"></a>查看 Azure 公共对等互连详细信息

查看 Azure 公共对等互连选择的对等互连的属性。

### <a name="updatepublic"></a>更新 Azure 公共对等互连配置

选择行，对等互连，然后修改对等互连的属性。

### <a name="deletepublic"></a>删除 Azure 公共对等互连

通过选择删除图标来删除对等互连配置。

## <a name="next-steps"></a>后续步骤

下一步，[将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-portal-resource-manager.md)
* 有关 ExpressRoute 工作流的详细信息，请参阅 [ExpressRoute 工作流](expressroute-workflows.md)。
* 有关线路对等互连的详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。
* 有关使用虚拟网络的详细信息，请参阅 [虚拟网络概述](../virtual-network/virtual-networks-overview.md)。
