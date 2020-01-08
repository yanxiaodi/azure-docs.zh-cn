---
title: 将虚拟网络链接到 ExpressRoute 线路：CLI：Azure | Microsoft Docs
description: 本文介绍如何使用资源管理器部署模型和 CLI 将虚拟网络 (VNet) 链接到 ExpressRoute 线路。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: cherylmc
ms.reviewer: anzaman
ms.custom: seodec18
ms.openlocfilehash: d858c83fb6669e5348b4256931e080656be0ebad
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621066"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>使用 CLI 将虚拟网络连接到 ExpressRoute 线路

本文介绍如何使用 CLI 将虚拟网络 (VNet) 链接到 Azure ExpressRoute 线路。 若要使用 Azure CLI 进行链接，必须使用资源管理器部署模型创建虚拟网络。 它们可以在同一个订阅中，也可以属于另一个订阅。 如果想使用不同的方法将 VNet 连接到 ExpressRoute 线路，请从以下列表中选择一篇文章进行参阅：

> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [视频 - Azure 门户](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>配置先决条件

* 需要最新版本的命令行接口 (CLI)。 有关详细信息，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

* 在开始配置之前，需要查看[先决条件](expressroute-prerequisites.md)、[路由要求](expressroute-routing.md)和[工作流](expressroute-workflows.md)。

* 必须有一个活动的 ExpressRoute 线路。 
  * 请按说明[创建 ExpressRoute 线路](howto-circuit-cli.md)，并通过连接提供商启用该线路。 
  * 确保为线路配置 Azure 专用对等互连。 有关路由说明，请参阅[配置路由](howto-routing-cli.md)一文。 
  * 请确保已配置 Azure 专用对等互连。 必须运行网络和 Microsoft 之间的 BGP 对等互连，使你能够启用端到端的连接。
  * 确保已创建并完全预配一个虚拟网络和一个虚拟网络网关。 请按照说明[为 ExpressRoute 配置虚拟网络网关](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)。 请务必使用 `--gateway-type ExpressRoute`。

* 最多可以将 10 个虚拟网络链接到一条标准 ExpressRoute 线路。 使用标准 ExpressRoute 线路时，所有虚拟网络必须都位于同一地缘政治区域。 

* 单个 VNet 可最多连接到 4 条 ExpressRoute 线路。 通过以下流程为正在连接的每条 ExpressRoute 线路创建新的连接对象。 ExpressRoute 线路可在同一订阅、不同订阅或两者兼有。

* 如果启用 ExpressRoute 高级版外接程序，则可以链接 ExpressRoute 线路的地缘政治区域外部的虚拟网络，或者将更多虚拟网络连接到 ExpressRoute 线路。 有关高级版外接程序的详细信息，请参阅[常见问题解答](expressroute-faqs.md)。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>将同一订阅中的虚拟网络连接到线路

可以使用以下示例将虚拟网络网关连接到 ExpressRoute 线路。 在运行此命令之前，请确保虚拟网络网关已创建并可用于进行链接。

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>将另一订阅中的虚拟网络连接到线路

用户可以在多个订阅之间共享 ExpressRoute 线路。 下图是在多个订阅之间共享 ExpressRoute 线路的简单示意图。

大型云中的每个较小云用于表示属于组织中不同部门的订阅。 组织内的每个部门可以使用自己的订阅部署其服务，但可以共享单个 ExpressRoute 线路以连接回本地网络。 一个部门（此示例中为：IT 部门）可以拥有 ExpressRoute 线路。 组织内的其他订阅可以使用 ExpressRoute 线路。

> [!NOTE]
> 专用线路的连接和带宽费用将应用于 ExpressRoute 线路所有者。 所有虚拟网络共享相同的带宽。
> 
> 

![跨订阅连接](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>管理 - 线路所有者和线路用户

“线路所有者”是 ExpressRoute 线路资源的已授权超级用户。 线路所有者可以创建可由“线路用户”兑换的授权。 线路用户是虚拟网络网关的所有者（这些网关与 ExpressRoute 线路位于不同的订阅中）。 线路用户可以兑现授权（每个虚拟网络需要一个授权）。

线路所有者有权随时修改和撤销授权。 撤销授权后，会从撤销了访问权限的订阅中删除所有链路连接。

### <a name="circuit-owner-operations"></a>线路所有者操作

**若要创建授权**

线路所有者创建一个授权，这将创建一个授权密钥，供线路用户用于将其虚拟网络网关连接到 ExpressRoute 线路。 一个授权只可用于一个连接。

以下示例说明如何创建授权：

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

此响应包含授权密钥和状态：

```azurecli
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**若要查看授权**

线路所有者可以运行以下示例来查看针对特定线路发布的所有授权：

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**若要添加授权**

线路所有者可以使用以下示例来添加授权：

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**若要删除授权**

线路所有者可以运行以下示例来撤销/删除对用户的授权：

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>线路用户操作

线路用户需要对等 ID 以及线路所有者提供的授权密钥。 授权密钥是一个 GUID。

```azurecli
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**若要兑换连接授权**

线路用户可以运行以下示例来兑现链接授权：

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**若要释放连接授权**

可以通过删除 ExpressRoute 线路与虚拟网络之间的连接释放授权。

## <a name="modify-a-virtual-network-connection"></a>修改虚拟网络连接
可以更新虚拟网络连接的某些属性。 

**若要更新连接权重**

虚拟网络可以连接到多条 ExpressRoute 线路。 可以从多条 ExpressRoute 线路收到相同的前缀。 若要选择使用哪个连接发送目标为此前缀的流量，可以更改连接的 *RoutingWeight*。 会在具有最高 *RoutingWeight* 的连接上发送流量。

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

*RoutingWeight* 的范围是 0 到 32000。 默认值为 0。

## <a name="configure-expressroute-fastpath"></a>配置 ExpressRoute 快速 
可以让[ExpressRoute 快速](expressroute-about-virtual-network-gateways.md)如果你的 ExpressRoute 线路位于[ExpressRoute 直接](expressroute-erdirect-about.md)和虚拟网络网关是超高性能或 ErGw3AZ。 快速提高了数据路径性能，如每秒数据包数和每秒的本地网络与虚拟网络之间的连接。 

> [!NOTE] 
> 如果你已有的虚拟网络连接，但尚未为其启用快速需要删除虚拟网络连接，然后创建一个新。 
> 
>  

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```


## <a name="next-steps"></a>后续步骤

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
