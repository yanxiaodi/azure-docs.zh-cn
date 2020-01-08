---
title: 获取 ARP 表 - ExpressRoute 故障排除：经典：Azure | Microsoft Docs
description: 此页说明了如何为 ExpressRoute 线路获取 ARP 表 - 经典部署模型。
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 3e49a1da0e8ea83faf5fc5a10d4c01a41d62fa88
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60883090"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>在经典部署模型中获取 ARP 表
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - 经典](expressroute-troubleshooting-arp-classic.md)
> 
> 

本文指导完成为 Azure ExpressRoute 线路获取地址解析协议 (ARP) 表的步骤。

> [!IMPORTANT]
> 本文档旨在帮助你诊断和修复简单问题。 它不是为了替代 Microsoft 支持部门。 如果使用以下指南无法解决问题，请使用 [Microsoft Azure 帮助+支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)建立支持请求。
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>地址解析协议 (ARP) 和 ARP 表
ARP 是 [RFC 826](https://tools.ietf.org/html/rfc826) 中定义的第 2 层协议。 ARP 用于将以太网地址（MAC 地址）映射到 IP 地址。

可以通过 ARP 表来映射 IPv4 地址和 MAC 地址，以便实现特定的对等互连。 用于 ExpressRoute 线路对等互连的 ARP 表为每个接口（主接口和辅助接口）提供以下信息：

1. 将本地路由器接口 IP 地址映射到 MAC 地址
2. 将 ExpressRoute 路由器接口 IP 地址映射到 MAC 地址
3. 映射的使用期限

ARP 表可帮助验证第 2 层配置，并可针对第 2 层的基本连接问题进行故障诊断。

下面是一个 ARP 表的示例：

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


以下部分介绍如何查看供 ExpressRoute 边缘路由器查看的 ARP 表。

## <a name="prerequisites-for-using-arp-tables"></a>使用 ARP 表的先决条件
在继续之前，请确保具备以下条件：

* 配置了至少一个对等互连的有效的 ExpressRoute 线路。 该线路必须由连接提供商进行完整的配置。 用户（或用户的连接提供商）必须在该线路上配置至少一个对等互连（Azure 专用、Azure 公共或 Microsoft）。
* 用于配置对等互连（Azure 专用、Azure 公共和 Microsoft）的 IP 地址范围。 查看中的 IP 地址分配示例[ExpressRoute 路由要求页](expressroute-routing.md)若要了解如何将 IP 地址映射到在侧和 ExpressRoute 侧的接口。 可通过查看 [ExpressRoute 对等互连配置页](expressroute-howto-routing-classic.md)了解对等互连配置。
* 网络团队或连接提供商提供的有关接口（用于这些 IP 地址）的 MAC 地址的信息。
* Azure 的最新 Windows PowerShell 模块（1.50 版或更高版本）。

## <a name="arp-tables-for-your-expressroute-circuit"></a>ExpressRoute 线路的 ARP 表
本部分说明如何使用 PowerShell 查看每种类型的对等互连的 ARP 表。 在继续之前，你或连接提供商必须配置对等互连。 每个线路有两个路径（主路径和辅助路径）。 可以独立地检查每个路径的 ARP 表。

### <a name="arp-tables-for-azure-private-peering"></a>Azure 专用对等互连的 ARP 表
以下 cmdlet 提供 Azure 专用对等互连的 ARP 表：

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

下面是其中一条路径的示例输出：

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Azure 公共对等互连的 ARP 表：
以下 cmdlet 提供 Azure 公共对等互连的 ARP 表：

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

下面是其中一条路径的示例输出：

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


下面是其中一条路径的示例输出：

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft 对等互连的 ARP 表
以下 cmdlet 提供 Microsoft 对等互连的 ARP 表：

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


下面是其中一条路径的示例性输出：

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>如何使用此信息
对等互连的 ARP 表可用于验证第 2 层配置和连接。 本部分概述了不同情况下的 ARP 表的外观。

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>当线路处于运行（预期）状态时的 ARP 表
* ARP 表会有一个针对本地端且包含有效 IP 地址和 MAC 地址的条目，以及一个类似的针对 Microsoft 端的条目。
* 本地 IP 地址的最后一个八位字节始终是奇数。
* Microsoft IP 地址的最后一个八位字节始终是偶数。
* 所有 3 种对等互连（主/辅助）在 Microsoft 端都会显示相同的 MAC 地址。

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>当 ARP 表在本地端或连接提供商端出现问题时的 ARP 表
 ARP 表中只显示一个条目。 它显示在 Microsoft 端使用的 MAC 地址与 IP 地址之间的映射。

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> 如果遇到此类问题，请通过连接提供商联系建立支持请求以解决它。
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>当 Microsoft 端出现问题时的 ARP 表
* 如果 Microsoft 端存在问题，则不会为对等互连显示 ARP 表。
* 使用 [Microsoft Azure 帮助+支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)建立支持请求。 指出第 2 层连接有问题。

## <a name="next-steps"></a>后续步骤
* 验证 ExpressRoute 线路的第 3 层配置：
  * 获取路由摘要以确定 BGP 会话的状态。
  * 获取路由表以确定哪些前缀跨 ExpressRoute 播发。
* 通过查看输入/输出中的字节数来验证数据传输。
* 如果仍然遇到问题，请使用 [Microsoft Azure 帮助+支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)建立支持请求。

