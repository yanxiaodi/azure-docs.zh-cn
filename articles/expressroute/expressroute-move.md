---
title: 将线路从经典部署移动到 Resource Manager 部署 - ExpressRoute：Azure | Microsoft Docs
description: 桥接经典和资源管理器部署模型的概述。
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: dfa2bbc735a79555da0421f64ca644adbd7a1701
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60363815"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型
本文概述将 Azure ExpressRoute 线路从经典部署模型转移到 Azure 资源管理器部署模型的效果。

可以使用一条 ExpressRoute 线路连接到在经典部署模型和 Resource Manager 部署模型中部署的虚拟网络。 无论 ExpressRoute 线路的创建方式为何，现在都可以链接到这两种部署模型中的虚拟网络。

![跨两种部署模型链接到虚拟网络的 ExpressRoute 线路](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>在经典部署模型中创建的 ExpressRoute 线路
在经典部署模型中创建的 ExpressRoute 线路需先转移到 Resource Manager 部署模型，才能连接到经典部署模型和 Resource Manager 部署模型。 转移连接时，不会发生连接断开的情况。 经典部署模型中所有从线路到虚拟网络的链接（在同一订阅中的链接和跨订阅链接）将会保留。

成功完成转移后，ExpressRoute 线路的感观和执行方式与在 Resource Manager 部署模型中创建的 ExpressRoute 线路完全相同。 现在，可以在 Resource Manager 部署模型中建立与虚拟网络的连接。

将 ExpressRoute 线路转移到 Resource Manager 部署模型后，只能使用 Resource Manager 部署模型来管理 ExpressRoute 线路的生命周期。 这意味着，某些操作（例如，添加/更新/删除对等互连，更新带宽、SKU 和计费类型等线路属性，以及删除线路）只能在 Resource Manager 部署模型中执行。 请参阅下面关于在 Resource Manager 部署模型中创建的线路的部分，以便进一步详细了解如何管理对这两种部署模型的访问权限。

不需要与连接服务提供商合作即可执行转移。

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>在 Resource Manager 部署模型中创建的 ExpressRoute 线路
可以允许从这两种部署模型访问在 Resource Manager 部署模型中创建的 ExpressRoute 线路。 订阅中的任何 ExpressRoute 线路都可以从这两种部署模型访问。

* 默认情况下，在 Resource Manager 部署模型中创建的 ExpressRoute 线路无法访问经典部署模型。
* 默认情况下，可以从这两种部署模型访问从经典部署模型转移到 Resource Manager 部署模型的 ExpressRoute 线路。
* 无论是在 Resource Manager 部署模型还是经典部署模型中创建的，ExpressRoute 线路始终都可以访问 Resource Manager 部署模型。 这意味着，可以根据 [如何链接虚拟网络](expressroute-howto-linkvnet-arm.md)中的说明，与 Resource Manager 部署模型中创建的虚拟网络建立连接。
* 对经典部署模型的访问权限由 ExpressRoute 线路中的 **allowClassicOperations** 参数控制。

> [!IMPORTANT]
> 将应用[服务限制](../azure-subscription-service-limits.md)页中所述的所有配额。 例如，标准线路最多可以有 10 个跨经典部署模型和 Resource Manager 部署模型的虚拟网络链接/连接。
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>控制对经典部署模型的访问权限
设置 ExpressRoute 线路的 **allowClassicOperations** 参数，即可让单个 ExpressRoute 线路链接到这两种部署模型中的虚拟网络。

将 **allowClassicOperations** 设置为 TRUE 即可从这两种部署模型中的虚拟网络链接到 ExpressRoute 线路。 可以遵循有关 [如何链接经典部署模型中的虚拟网络](expressroute-howto-linkvnet-classic.md)的指导，链接到经典部署模型中的虚拟网络。 可以遵循有关 [如何链接 Resource Manager 部署模型中的虚拟网络](expressroute-howto-linkvnet-arm.md)的指导，链接到 Resource Manager 部署模型中的虚拟网络。

将 **allowClassicOperations** 设置为 FALSE 会阻止从经典部署模型访问线路。 但是，经典部署模型中的所有虚拟网络链接都会保留。 在此情况下，ExpressRoute 线路不显示在经典部署模型中。

## <a name="supported-operations-in-the-classic-deployment-model"></a>经典部署模型中支持的操作
将 **allowClassicOperations** 设置为 TRUE 时，ExpressRoute 线路支持以下经典操作。

* 获取 ExpressRoute 线路信息
* 创建/更新/获取/删除到经典虚拟网络的虚拟网络链接
* 创建/更新/获取/删除跨订阅连接的虚拟网络链接授权

然而，将 allowClassicOperations 设置为 TRUE 时，无法执行以下经典操作  ：

* 创建/更新/获取/删除 Azure 专用对等互连、Azure 公共对等互连和 Microsoft 对等互连的边界网关协议 (BGP) 对等互连
* 删除 ExpressRoute 线路

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>经典部署模型和 Resource Manager 部署模型之间的通信
ExpressRoute 线路相当于经典部署模型与 Resource Manager 部署模型之间的桥梁。 经典部署模型的虚拟网络中的虚拟机与 Resource Manager 部署模型的虚拟网络中的虚拟机之间的流量将流经 ExpressRoute，前提是这两个虚拟网络链接到相同的 ExpressRoute 线路。

聚合吞吐量受限于虚拟网络网关的吞吐容量。 在这种情况下，流量不进入连接服务提供商的网络或网络。 虚拟网络之间的流量完全包含在 Microsoft 网络中。

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>对 Azure 公共对等互连资源和 Microsoft 对等互连资源的访问权限
可以继续访问通常可通过 Azure 公共对等互连和 Microsoft 对等互连访问的资源，而不会出现任何中断。  

## <a name="whats-supported"></a>支持的操作
本部分介绍可通过 ExpressRoute 线路执行的操作：

* 可以使用一条 ExpressRoute 线路访问在经典部署模型和 Resource Manager 部署模型中部署的虚拟网络。
* 可以将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型。 转移后，ExpressRoute 线路的感观和执行方式与在 Resource Manager 部署模型中创建的任何其他 ExpressRoute 线路相似。
* 只能转移 ExpressRoute 线路。 无法通过此操作转移线路链接、虚拟网络和 VPN 网关。
* 将 ExpressRoute 线路转移到 Resource Manager 部署模型后，只能使用 Resource Manager 部署模型来管理 ExpressRoute 线路的生命周期。 这意味着，某些操作（例如，添加/更新/删除对等互连，更新带宽、SKU 和计费类型等线路属性，以及删除线路）只能在 Resource Manager 部署模型中执行。
* ExpressRoute 线路相当于经典部署模型与 Resource Manager 部署模型之间的桥梁。 经典部署模型的虚拟网络中的虚拟机与 Resource Manager 部署模型的虚拟网络中的虚拟机之间的流量将流经 ExpressRoute，前提是这两个虚拟网络链接到相同的 ExpressRoute 线路。
* 经典部署模型和 Resource Manager 部署模型都支持跨订阅连接。
* 将 ExpressRoute 线路从经典模型移到 Azure Resource Manager 模型后，即可[迁移链接到 ExpressRoute 线路的虚拟网络](expressroute-migration-classic-resource-manager.md)。

## <a name="whats-not-supported"></a>不支持的功能
本部分介绍不可通过 ExpressRoute 线路执行的操作：

* 从经典部署模型管理 ExpressRoute 线路的生命周期。
* 针对经典部署模型的基于角色的访问控制 (RBAC) 支持。 无法对经典部署模型中的线路执行 RBAC 控制。 订阅的任何管理员/共同管理员都可以将虚拟网络链接到线路，也都可以取消此类链接。

## <a name="configuration"></a>配置
遵循[将 ExpressRoute 线路从经典部署模型转移到 Resource Manager 部署模型](expressroute-howto-move-arm.md)中所述的说明。

## <a name="next-steps"></a>后续步骤
* [将链接到 ExpressRoute 线路的虚拟网络从经典模型迁移到 Azure Resource Manager 模型](expressroute-migration-classic-resource-manager.md)
* 有关工作流信息，请参阅[ExpressRoute 线路预配工作流和线路状态](expressroute-workflows.md)。
* 配置 ExpressRoute 连接的步骤：
  
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)
  * [配置路由](expressroute-howto-routing-arm.md)
  * [将虚拟网络链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)

