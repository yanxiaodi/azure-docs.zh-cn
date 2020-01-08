---
title: QoS 要求 - ExpressRoute：Azure | Microsoft Docs
description: 本页提供有关配置和管理 QoS 的详细要求。 将讨论 Skype for Business/语音服务。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: eed6113442b4080341ff08b3983880f3afe66c00
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385124"
---
# <a name="expressroute-qos-requirements"></a>ExpressRoute QoS 要求
Skype for Business 具有各种工作负荷，它们要求的 QoS 处理方式各有差异。 如果打算通过 ExpressRoute 使用语音服务，应遵守以下所述要求。

![](./media/expressroute-qos/expressroute-qos.png)

> [!NOTE]
> QoS 要求仅适用于 Microsoft 对等互连。 Azure 公共对等互连和 Azure 专用对等互连上接收自网络流量中的 DSCP 值将重置为 0。 
> 
> 

下表提供了 Microsoft 团队和 Skype for Business 使用的 DSCP 标记的列表。 有关详细信息，请参阅 [管理 Skype for Business 的 QoS](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/managing-quality-of-service-QoS) 。

| **流量类** | **处理方式（DSCP 标记）** | **Microsoft 团队和 Skype for business 工作负荷** |
| --- | --- | --- |
| **语音** |EF (46) |Skype/Microsoft 团队/Lync 语音 |
| **交互式** |AF41 (34) |视频，VBSS |
| |AF21 (18) |应用共享 | 
| **默认** |AF11 (10) |文件传输 |
| |CS0 (0) |任何其他项目 |

* 应该将工作负荷分类，并标记正确的 DSCP 值。 遵循 [此处](https://docs.microsoft.com/SkypeForBusiness/manage/network-management/qos/configuring-port-ranges-for-your-skype-clients#configure-quality-of-service-policies-for-clients-running-on-windows-10) 提供的指导，了解如何在网络中设置 DSCP 标记。
* 应在网络中配置并支持多个 QoS 队列。 语音必须是独立的类，并可接收 [RFC 3246](https://www.ietf.org/rfc/rfc3246.txt) 中指定的 EF 处理方式。 
* 可以确定每个流量类的队列机制、阻塞检测策略和带宽分配。 但是，必须预留 Skype for Business 工作负荷的 DSCP 标记。 如果使用上面未列出的 DSCP 标记（例如 AF31 (26)），则必须先将此 DSCP 值重写为 0，然后再将数据包发送到 Microsoft。 Microsoft 只发送使用上表所示 DSCP 值标记的数据包。 

## <a name="next-steps"></a>后续步骤
* 请参阅[路由](expressroute-routing.md)和 [NAT](expressroute-nat.md) 的要求。
* 请参阅以下链接来配置 ExpressRoute 连接。
  
  * [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md)
  * [配置路由](expressroute-howto-routing-classic.md)
  * [将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)

