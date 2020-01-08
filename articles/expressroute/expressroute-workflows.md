---
title: 线路配置工作流 - ExpressRoute:Azure | Microsoft Docs
description: 本页显示配置 ExpressRoute 线路和对等互连的工作流
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 1e0f4d356bb6addf735bf148a80e6be3ca550c8f
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123384"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute 线路预配工作流和线路状态
本页从较高层面引导完成服务预配和路由配置工作流。

![线路工作流](./media/expressroute-workflows/expressroute-circuit-workflow.png)

下图和相应的步骤说明了预配端到端 ExpressRoute 线路所要执行的任务。 

1. 使用 PowerShell 配置 ExpressRoute 线路。 有关更多详细信息，请遵循[创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md)一文中的说明。
2. 从服务提供商订购连接。 此过程根据情况而有所不同。 有关如何订购连接的详细信息，请联系连接服务提供商。
3. 通过 PowerShell 验证 ExpressRoute 线路预配状态，以确保线路预配成功。 
4. 配置路由域 如果连接服务提供商管理第 3 层，他们将为你的线路配置路由。 如果连接服务提供商只提供第 2 层服务，必须根据[路由要求](expressroute-routing.md)和[路由配置](expressroute-howto-routing-classic.md)页中所述的每条指导原则来配置路由。
   
   * 启用 Azure 专用对等互连 - 启用此对等互连以连接到部署在虚拟网络中的 VM/云服务。

   * 启用 Microsoft 对等互连-启用此项以访问 Office 365。 此外，所有的 Azure PaaS 服务也可通过 Microsoft 对等互连访问。
     
     > [!IMPORTANT]
     > 必须确保使用独立的代理/边缘，而不是用于 Internet 的 代理/边缘来连接 Microsoft。 对 ExpressRoute 和 Internet 使用相同的边缘会导致路由不对称，并造成网络连接中断。
     > 
     > 
     
     ![路由工作流](./media/expressroute-workflows/routing-workflow.png)
5. 将虚拟网络链接到 ExpressRoute 线路 - 可以将虚拟网络链接到 ExpressRoute 线路。 请按照说明[将 VNet 链接](expressroute-howto-linkvnet-arm.md)到你的线路。 这些 VNet 可以位于 ExpressRoute 线路所在的同一 Azure 订阅中，也可以位于不同的订阅中。

## <a name="expressroute-circuit-provisioning-states"></a>ExpressRoute 线路预配状态
每条 ExpressRoute 线路有两种状态：

* 服务提供商预配状态
* 状态

“状态”表示 Microsoft 的预配状态。 创建 Expressroute 线路时，此属性设置为 Enabled

连接服务提供商预配状态表示连接服务提供商一端的状态。 该状态可能是 NotProvisioned、Provisioning 或 Provisioned。 ExpressRoute 线路必须处于 Provisioned 状态才可供使用。

### <a name="possible-states-of-an-expressroute-circuit"></a>ExpressRoute 线路的可能状态
本部分列出 ExpressRoute 线路的可能状态。

**创建时**

运行 PowerShell cmdlet 创建 ExpressRoute 线路后，很快就会看到 ExpressRoute 线路处于以下状态。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**连接服务提供商正在预配线路时**

将服务密钥传递给连接服务提供商并且他们已启动预配过程时，很快就会看到 ExpressRoute 线路处于以下状态。

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**连接服务提供商完成预配过程时**

当连接服务提供商完成预配过程后，很快就会看到 ExpressRoute 线路处于以下状态。

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

线路只有处于 Provisioned 和 Enabled 状态时才可供使用。 如果使用第 2 层服务提供商，则只有在线路处于此状态时才能配置路由。

**连接服务提供商正在取消预配线路时**

如果已请求服务提供商取消预配 ExpressRoute 线路，当服务提供商完成取消预配过程后，将看到线路已设置为以下状态。

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


如果需要，可以选择重新启用线路，或运行 PowerShell cmdlet 删除线路。  

> [!IMPORTANT]
> 当 ServiceProviderProvisioningState 为 Provisioning 或 Provisioned 时，如果运行该 PowerShell cmdlet 来删除线路，操作会失败。 请先让连接服务提供商取消设置 ExpressRoute 线路，然后删除该线路。 在运行 PowerShell cmdlet 删除线路之前，Microsoft 会持续收取线路费用。
> 
> 

## <a name="routing-session-configuration-state"></a>路由会话配置状态
BGP 预配状态可让你知道 Microsoft 边缘是否已启用 BGP 会话。 必须处于已启用状态才能使用对等互连。

必须特意检查 Microsoft 对等互连的 BGP 会话状态。 除了 BGP 预配状态，还有另一个状态称为播发的公共前缀状态。 播发的公共前缀状态必须是已配置状态，BGP 会话才能启动，才能进行端到端的路由。 

如果播发的公共前缀状态设置为需要验证状态，则不会启用 BGP 会话，因为播发的前缀不符合任何路由注册表中的 AS 编号。 

> [!IMPORTANT]
> 如果播发的公共前缀状态是手动验证状态，则必须向 [Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)开具支持票证，并提供拥有播发 IP 地址的证明以及相关的自治系统编号。
> 
> 

## <a name="next-steps"></a>后续步骤
* 配置 ExpressRoute 连接。
  
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)
  * [配置路由](expressroute-howto-routing-arm.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-arm.md)

