---
title: Azure 负载均衡器的多个前端
titlesuffix: Azure Load Balancer
description: Azure 负载均衡器上的多个前端概述
services: load-balancer
documentationcenter: na
author: chkuhtz
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: chkuhtz
ms.openlocfilehash: b109e87a8fcbef0bfca356c83716509ebc6cecd4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884217"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Azure 负载均衡器的多个前端

使用 Azure 负载均衡器可对多个端口和/或多个 IP 地址上的服务进行负载均衡。 可以使用公共和内部负载均衡器定义来对一组 VM 之间的流量进行负载均衡。

本文介绍此功能的基础知识、重要概念和约束。 如果只想要公开一个 IP 地址上的服务，可以查看[公共](load-balancer-get-started-internet-portal.md)或[内部](load-balancer-get-started-ilb-arm-portal.md)负载均衡器配置的简要说明。 添加多个前端是对单个前端配置的递增。 使用本文中的概念，随时可以扩展简化的配置。

定义 Azure 负载均衡器时，前端和后端池配置与规则相连接。 规则引用的运行状况探测用于确定如何将新流量发送到后端池中的节点。 前端（也称为 VIP）由负载均衡规则中的 IP 地址（公共或内部）、传输协议（UDP 或 TCP）和端口号组成的 3 元组定义。 后端池是引用负载均衡器后端池的虚拟机 IP 配置（NIC 资源的一部分）的集合。

下表包含一些示例前端配置：

| 前端 | IP 地址 | 协议 | 端口 |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

该表显示了四个不同的前端。 前端 #1、#2 和 #3 是具有多个规则的单一前端。 每个前端使用相同的 IP 地址，但端口或协议不同。 前端 #1 和 #4 是多个前端的示例，在多个前端中重复使用相同的前端协议和端口。

在 Azure 负载均衡器中可以灵活定义负载均衡规则。 规则声明如何将前端上的地址和端口映射到后端上的目标地址和端口。 是否在不同的规则中重复使用后端端口取决于规则的类型。 每种类型的规则有特定的要求，可能会影响主机配置和探测设计。 有两种类型的规则：

1. 默认规则，不重复使用后端端口
2. 浮动 IP 规则，重复使用后端端口

Azure Load Balancer 允许在相同的负载均衡器配置中混用这两种规则类型。 负载均衡器可以针对给定的 VM 同时使用这两种规则或两者的任意组合，只要遵守规则的约束即可。 选择哪种规则类型取决于应用程序要求以及支持该配置的复杂性。 应该评估哪种规则类型最适合自己的方案。

我们将从默认行为开始进一步探讨这些方案。

## <a name="rule-type-1-no-backend-port-reuse"></a>规则类型 #1：不重复使用后端端口

![具有绿色前端和紫色前端的多个前端插图](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

在此方案中，前端的配置如下：

| 前端 | IP 地址 | 协议 | 端口 |
| --- | --- | --- | --- |
| ![绿色前端](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![紫色前端](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP 是入站流量的目标。 在后端池中，每个 VM 公开 DIP 上唯一端口上的所需服务。 此服务通过规则定义与前端关联。

我们定义了两个规则：

| 规则 | 映射前端 | 目标后端池 |
| --- | --- | --- |
| 1 |![绿色前端](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![后端](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![后端](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![后端](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![后端](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

现在，Azure 负载均衡器的完整映射如下：

| 规则 | 前端 IP 地址 | protocol | 端口 | 目标 | 端口 |
| --- | --- | --- | --- | --- | --- |
| ![绿色规则](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP IP 地址 |80 |
| ![紫色规则](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP IP 地址 |81 |

每个规则必须生成具有目标 IP 地址和目标端口唯一组合的流量。 通过改变流量的目标端口，多个规则可将流量发送到不同端口上的相同 DIP。

运行状况探测始终定向到 VM 的 DIP。 必须确保探测反映 VM 的运行状况。

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>规则类型 #2：使用浮动 IP 来重复使用后端端口

使用 Azure 负载均衡器可以灵活地在多个前端中重复使用前端端口，而不管使用哪种规则类型。 此外，在某些应用程序方案中，后端池中单个 VM 上的多个应用程序实例偏好或必须使用相同端口。 重复使用端口的常见示例包括提供高可用性群集、网络虚拟设备，以及公开多个不重新加密的 TLS 终结点。

如果想要在多个规则中重复使用后端端口，必须在规则定义中启用浮动 IP。

“浮动 IP”是 Azure 的术语，表示是所谓的直接服务器返回 (DSR) 的一部分。 DSR 包括两个组成部分：流拓扑和 IP 地址映射方案。 在平台级别，Azure 负载均衡器始终在 DSR 流拓扑中运行，无论是否已启用浮动 IP。 这意味着，流的出站部分始终正确重写为直接流回到来源。

使用默认规则类型时，Azure 将公开传统的负载均衡 IP 地址映射方案以便于使用。 启用浮动 IP 会更改 IP 地址映射方案，提供更大的灵活性，请参阅下面的说明。

下图演示了此配置：

![具有使用 DSR 的绿色前端和紫色前端的多个前端插图](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

此方案中，后端池中的每个 VM 有三个网络接口：

* DIP：与 VM 关联的虚拟 NIC（Azure NIC 资源的 IP 配置）
* 前端 1：来宾 OS 中的环回接口，该接口上已配置前端 1 的 IP 地址
* 前端 2：来宾 OS 中的环回接口，该接口上已配置前端 2 的 IP 地址

> [!IMPORTANT]
> 环回接口的配置在来宾 OS 中执行。 此配置不是由 Azure 执行或管理。 如果没有此配置，规则将无法正常运行。 运行状况探测定义使用 VM 的 DIP（而不是环回接口）表示 DSR 前端。 因此，服务必须在 DIP 端口上提供探测响应，以反映表示 DSR 前端的环回接口上提供的服务的状态。

假设上述方案使用相同的前端配置：

| 前端 | IP 地址 | 协议 | 端口 |
| --- | --- | --- | --- |
| ![绿色前端](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![紫色前端](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

我们定义了两个规则：

| 规则 | 前端 | 映射到后端池 |
| --- | --- | --- |
| 1 |![rule](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![后端](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80（在 VM1 和 VM2 中） |
| 2 |![rule](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![后端](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80（在 VM1 和 VM2 中） |

下表显示负载均衡器中的完整映射：

| 规则 | 前端 IP 地址 | protocol | 端口 | 目标 | 端口 |
| --- | --- | --- | --- | --- | --- |
| ![绿色规则](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |与前端 (65.52.0.1) 相同 |与前端 (80) 相同 |
| ![紫色规则](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |与前端 (65.52.0.2) 相同 |与前端 (80) 相同 |

入站流量的目标是 VM 中环回接口上的前端 IP 地址。 每个规则必须生成具有目标 IP 地址和目标端口唯一组合的流量。 通过改变流量的目标 IP 地址，可以在同一 VM 上重复使用端口。 通过将服务绑定到前端的 IP 地址和相应环回接口的端口，可以向负载均衡器公开服务。

请注意，本示例未更改目标端口。 这是一个浮动 IP 方案，不过 Azure 负载均衡器也支持定义规则来重写后端的目标端口，使其与前端的目标端口不同。

浮动 IP 规则类型是多种负载均衡器配置模式的基础。 [具有多个侦听器的 SQL AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) 配置是目前提供的示例之一。 一段时间后，我们会介绍更多此类方案。

## <a name="limitations"></a>限制

* 只有 IaaS VM 支持多个前端配置。
* 使用浮点 IP 规则时, 应用程序必须为出站 SNAT 流使用主 IP 配置。 如果应用程序绑定到来宾 OS 中环回接口上配置的前端 IP 地址, 则无法使用 Azure 的出站 SNAT 来重写出站流, 流将失败。  查看[出站方案](load-balancer-outbound-connections.md)。
* 公共 IP 地址会影响计费。 有关详细信息，请参阅 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses/)
* 订阅有所限制。 有关详细信息，请参阅[服务限制](../azure-subscription-service-limits.md#networking-limits)。

## <a name="next-steps"></a>后续步骤

- 查看[出站连接](load-balancer-outbound-connections.md)，了解多个前端对出站连接行为的影响。
