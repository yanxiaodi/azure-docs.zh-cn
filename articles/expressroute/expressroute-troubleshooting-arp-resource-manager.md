---
title: 获取 ARP 表 - 故障排除 - ExpressRoute：Azure | Microsoft Docs
description: 此页说明了如何为 ExpressRoute 线路获取 ARP 表
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 76e242adb07f4e6176bbdc6c03c75950e3732c2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151588"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>在 Resource Manager 部署模型中获取 ARP 表
> [!div class="op_single_selector"]
> * [PowerShell - 资源管理器](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - 经典](expressroute-troubleshooting-arp-classic.md)
> 
> 

本文指导完成相关步骤，以便了解 ExpressRoute 线路的 ARP 表。

> [!IMPORTANT]
> 本文档旨在帮助你诊断和修复简单问题。 它不是为了替代 Microsoft 支持部门。 如果无法通过下述指南解决问题，则必须通过 [Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)开具支持票证。
> 
> 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>地址解析协议 (ARP) 和 ARP 表
地址解析协议 (ARP) 是在 [RFC 826](https://tools.ietf.org/html/rfc826) 中定义的第二层协议。 ARP 用于映射以太网地址（MAC 地址）和 IP 地址。

可以通过 ARP 表来映射 IPv4 地址和 MAC 地址，以便实现特定的对等互连。 用于 ExpressRoute 线路对等互连的 ARP 表为每个接口（主接口和辅助接口）提供以下信息

1. 将本地路由器接口 IP 地址映射到 MAC 地址
2. 将 ExpressRoute 路由器接口 IP 地址映射到 MAC 地址
3. 映射的使用期限

ARP 表可帮助验证第 2 层配置，并可针对第 2 层的基本连接问题进行故障诊断。 

ARP 表示例： 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


以下部分介绍如何查看供 ExpressRoute 边缘路由器查看的 ARP 表。 

## <a name="prerequisites-for-learning-arp-tables"></a>了解 ARP 表需具备的先决条件
在继续下一步之前，请确保已具备以下条件

* 配置了至少一个对等互连的有效的 ExpressRoute 线路。 该线路必须由连接提供商进行完整的配置。 你（或连接提供商）必须已经在该线路上配置了 Azure 专用、Azure 公共和 Microsoft 这三者中的至少一个对等互连。
* 用于配置对等互连（Azure 专用、Azure 公共和 Microsoft）的 IP 地址范围。 查看 [ExpressRoute 路由要求页](expressroute-routing.md)中的 IP 地址分配示例，了解如何将 IP 地址映射到所在的一侧和 ExpressRoute 侧的接口。 可通过查看 [ExpressRoute 对等互连配置页](expressroute-howto-routing-arm.md)了解对等互连配置。
* 网络团队/连接提供商提供的有关接口（用于这些 IP 地址）的 MAC 地址的信息。
* 必须安装 Azure 的最新 PowerShell 模块（1.50 或更高版本）。

> [!NOTE]
> 如果服务提供商提供第 3 层且 ARP 表在下面的门户/输出中为空，请使用门户中的刷新按钮来刷新线路配置。 此操作会将正确的线路配置应用到你的线路。 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>获取 ExpressRoute 线路的 ARP 表
本部分说明了如何使用 PowerShell 根据对等互连来查看 ARP 表。 你或连接提供商必须在执行下一步之前配置好对等互连。 每个线路有两个路径（主路径和辅助路径）。 可以独立地检查每个路径的 ARP 表。

### <a name="arp-tables-for-azure-private-peering"></a>Azure 专用对等互连的 ARP 表
以下 cmdlet 为 Azure 专用对等互连提供 ARP 表

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure private peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

        # ARP table for Azure private peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

下面为其中一个路径显示了示例性输出

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1   ffff.eeee.dddd
          0 Microsoft         10.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Azure 公共对等互连的 ARP 表
以下 cmdlet 为 Azure 公共对等互连提供 ARP 表

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Azure public peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

        # ARP table for Azure public peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


下面为其中一个路径显示了示例性输出

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1   ffff.eeee.dddd
          0 Microsoft         64.0.0.2   aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft 对等互连的 ARP 表
以下 cmdlet 提供 Microsoft 对等互连的 ARP 表

        # Required Variables
        $RG = "<Your Resource Group Name Here>"
        $Name = "<Your ExpressRoute Circuit Name Here>"

        # ARP table for Microsoft peering - Primary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

        # ARP table for Microsoft peering - Secondary path
        Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


下面为其中一个路径显示了示例性输出

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>如何使用此信息
对等互连的 ARP 表可用于确定/验证第 2 层配置和连接。 本部分概述了不同方案的 ARP 表的外观。

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>当线路处于运行状态（预期状态）时的 ARP 表
* ARP 表会有一个针对本地端且带有有效 IP 地址和 MAC 地址的条目，以及一个类似的针对 Microsoft 端的条目。 
* 本地 IP 地址的最后一个八位字节将始终是奇数。
* Microsoft IP 地址的最后一个八位字节将始终是偶数。
* 所有 3 种对等互连（主/辅助）在 Microsoft 端都会显示相同的 MAC 地址。 

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1   ffff.eeee.dddd
          0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>当本地端/连接提供商端出现问题时的 ARP 表
如果本地或连接提供商有问题，则可能会看到只有一个条目出现在 ARP 表中，或者本地 MAC 地址会显示不完整。 此时会显示在 Microsoft 端使用的 MAC 地址与 IP 地址之间的映射。 
  
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------    
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc

或
       
       Age InterfaceProperty IpAddress  MacAddress    
       --- ----------------- ---------  ----------   
         0 On-Prem           65.0.0.1   Incomplete
         0 Microsoft         65.0.0.2   aaaa.bbbb.cccc


> [!NOTE]
> 通过连接提供商提出支持请求，以便进行此类问题的调试。 如果 ARP 表没有将接口的 IP 地址映射到 MAC 地址，请查询以下信息：
> 
> 1. 为 MSEE-PR 和 MSEE 之间的链接分配的 /30 子网的第一个 IP 地址是否用在 MSEE-PR 的接口上。 Azure 始终使用 MSEE 的第二个 IP 地址。
> 2. 验证客户型 (C-Tag) 和服务型 (S-Tag) VLAN 标记在 MSEE-PR 和 MSEE 对上是否均匹配。
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>当 Microsoft 端出现问题时的 ARP 表
* 如果 Microsoft 端存在问题，则不会为对等互连显示 ARP 表。 
* 通过 [Microsoft 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)开具一个支持票证。 指出第 2 层连接有问题。 

## <a name="next-steps"></a>后续步骤
* 验证 ExpressRoute 线路的第 3 层配置
  * 获取路由摘要以确定 BGP 会话的状态 
  * 获取路由表以确定哪些前缀跨 ExpressRoute 播发
* 通过查看输入/输出中的字节数来验证数据传输
* 如果仍然存在问题，请通过 [Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)开具一个支持票证。

