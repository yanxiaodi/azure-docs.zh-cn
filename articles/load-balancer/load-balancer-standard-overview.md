---
title: 什么是 Azure 标准负载均衡器？
titlesuffix: Azure Load Balancer
description: Azure 标准负载均衡器功能概述
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2019
ms.author: allensu
ms.openlocfilehash: 8eb8134452685add53b9dc339437ac262ecc8a9f
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274401"
---
# <a name="azure-standard-load-balancer-overview"></a>Azure 标准负载均衡器概述

使用 Azure 负载均衡器可以缩放应用程序，并为服务提供高可用性。 负载均衡器可用于入站和出站方案、提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。 

本文着重介绍标准负载均衡器。  有关 Azure 负载均衡器的详细常规概述，还可以查看[负载均衡器概述](load-balancer-overview.md)。

## <a name="what-is-standard-load-balancer"></a>什么是标准负载均衡器？

标准负载均衡器是适用于所有 TCP 和 UDP 应用程序的新型负载均衡器产品，与基本负载均衡器相比拥有更广泛和精细的功能集。  尽管两者有许多相似之处，但请务必熟悉本文中所述的差异。

可将标准负载均衡器用作公共或内部负载均衡器。 虚拟机可以连接到一个公共负载均衡器资源和一个内部负载均衡器资源。

负载均衡器资源的功能始终表示为前端、规则、运行状况探测和后端池定义。  资源可以包含多项规则。 可通过从虚拟机的 NIC 资源指定后端池，将虚拟机放入其中。  此参数通过网络配置文件传递，并在使用虚拟机规模集时进行扩展。

资源的虚拟网络范围是一个重要方面。  尽管基本负载均衡器存在于可用性集范围内部，但标准负载均衡器与虚拟网络范围完全集成，且所有虚拟网络概念均适用。

负载均衡器资源是一些对象，可在其中表述 Azure 应如何设定其多租户基础结构，以实现想要创建的场景。  负载均衡器资源与实际基础结构之间不存在直接的关系，创建负载均衡器不会创建实例，可始终使用容量，且无需考虑启动或缩放延迟。 

>[!NOTE]
> Azure 为方案提供了一套完全托管的负载均衡解决方案。  若要寻求 TLS 终止（“SSL 卸载”）或每个 HTTP/HTTPS 请求的应用层处理，请查看[应用程序网关](../application-gateway/application-gateway-introduction.md)。  若要寻求全局 DNS 负载均衡，请查看[流量管理器](../traffic-manager/traffic-manager-overview.md)。  端到端方案可从结合所需的解决方案中受益。

## <a name="why-use-standard-load-balancer"></a>为何使用标准负载均衡器？

使用标准负载均衡器可以扩展应用程序，并为小型部署到大型复杂多区域体系结构创建高可用性。

查看下表，了解标准负载均衡器与基本负载均衡器之间的差异概述：

>[!NOTE]
> 新设计应采用标准负载均衡器。 

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

请查看[负载均衡器的服务限制](https://aka.ms/lblimits)、[定价](https://aka.ms/lbpricing)和 [SLA](https://aka.ms/lbsla)。


### <a name="backend"></a>后端池

标准负载均衡器的后端池扩展到虚拟网络中的任何虚拟机资源。  可包含多达 1000 个后端实例。  后端实例是 IP 配置（NIC 资源的属性）。

后端池可以包含独立的虚拟机、可用性集或虚拟机规模集。  还可以在后端池中混合资源。 按每个负载均衡器资源计算，最多可以在后端池中混合 150 个资源。

考虑后端池的设计方式时，可针对单个后端池资源的最小数字进行设计，从而进一步优化管理操作的持续时间。  在数据平面性能或规模中不存在任何差异。

### <a name="probes"></a>运行状况探测
  
标准负载均衡器增加了对 [HTTPS 运行状况探测](load-balancer-custom-probe-overview.md#httpprobe)（具有传输层安全 (TLS) 包装程序的 HTTP 探测）的支持来准确地监视 HTTPS 应用程序。  

此外，当整个后端池[探测关闭](load-balancer-custom-probe-overview.md#probedown)时，标准负载均衡器允许所有已建立的 TCP 连接继续运行。 （基本负载均衡器会终止所有实例的所有 TCP 连接）。

有关详细信息，请查看[负载均衡器运行状况探测](load-balancer-custom-probe-overview.md)。

### <a name="az"></a>可用性区域

>[!IMPORTANT]
>查看[可用性区域](../availability-zones/az-overview.md)相关主题, 包括任何特定于区域的信息。

标准负载均衡器在提供可用性区域的区域中支持其他功能。  这些功能可增量到所有标准负载均衡器提供的内容。  可用性区域配置可用于公共和内部标准负载均衡器。

使用可用性区域在区域中部署时，非区域性前端默认变为区域冗余前端。   区域冗余前端在发生区域故障后仍保留，并由所有区域中的专用基础结构同时提供。 

此外，可以保证特定区域的前端。 区域前端的运行状况取决于相应的区域，仅由一个区域中的专用基础结构提供。

跨区域负载均衡可用于后端池，且 VNet 中的任何虚拟机资源均可成为后端池的一部分。

请查看[有关可用性区域的功能的详细讨论](load-balancer-standard-availability-zones.md)。

### <a name="diagnostics"></a>诊断

标准负载均衡器通过 Azure Monitor 提供多维度指标。  可以就给定维度对这些指标进行筛选、分组和细分。  可便于深入了解服务的当前及历史性能和运行状况。  还支持资源运行状况。  以下是支持的诊断的简要概述：

| 指标 | 描述 |
| --- | --- |
| VIP 可用性 | 标准负载均衡器持续运用从区域内部到负载均衡器前端，直到支持 VM 的 SDN 堆栈的数据路径。 只要保留正常实例，这种度量就会遵循应用程序负载均衡的流量所用的相同路径。 此外，还会验证客户使用的数据路径。 度量对于应用程序不可见，且不会干扰其他操作。|
| DIP 可用性 | 标准负载均衡器使用分布式运行状况探测服务，根据配置设置监视应用程序终结点的运行状况。 此指标提供负载均衡器池中每个实例终结点的聚合视图或按终结点筛选的视图。  可以查看负载均衡器如何根据运行状况探测配置的指示了解应用程序的运行状况。
| SYN 数据包 | 标准负载均衡器不会终止 TCP 连接，也不会与 TCP 或 UDP 数据包流交互。 流及其握手始终位于源和 VM 实例之间。 若要更好地排查 TCP 协议方案的问题，可以使用 SYN 数据包计数器了解进行了多少次 TCP 连接尝试。 该指标将报告接收到的 TCP SYN 数据包数目。|
| SNAT 连接 | 标准负载均衡器报告公共 IP 地址前端上伪装的出站流数。 SNAT 端口是可耗竭性资源。 此指标可以指出应用程序依赖于 SNAT 获取出站发起流的程度有多高。  将报告成功和失败的出站 SNAT 流的计数器，可使用这些计数器排查和了解出站流的运行状况。|
| 字节计数器 | 标准负载均衡器按前端报告处理的数据。|
| 数据包计数器 | 标准负载均衡器按前端报告处理的数据包。|

请查看[有关标准负载均衡器诊断的详细讨论](load-balancer-standard-diagnostics.md)。

### <a name="haports"></a>HA 端口

标准负载均衡器支持一种新型规则。  

可以配置负载均衡规则，让应用程序具有缩放性，并且变得高度可靠。 使用 HA 端口负载均衡规则时，在内部标准负载均衡器的前端 IP 地址的每个临时端口上，标准负载均衡器对每个流提供负载均衡。  该功能对无法或不需要指定单个端口的其他方案也很有用。

使用 HA 端口负载均衡规则，可以为网络虚拟设备以及任何需要大范围入站端口的应用程序创建主动-被动或主动-被动 n+1 方案。  运行状况探测可用于确定接收新流的后端。  可使用网络安全组模拟端口范围方案。

>[!IMPORTANT]
> 如果计划使用网络虚拟设备，请咨询供应商以获取指南，了解他们的产品是否测试了 HA 端口，然后按照他们提供的特定指南进行实现。 

请查看[有关 HA 端口的详细讨论](load-balancer-ha-ports-overview.md)。

### <a name="securebydefault"></a>默认保护

标准负载均衡器已完全载入虚拟网络。  虚拟网络是封闭的专用网络。  标准负载均衡器和标准公共 IP 地址旨在允许从虚拟网络外部访问该虚拟网络，因此，这些资源现在默认处于关闭状态，除非手动打开。 这意味着网络安全组 (NSG) 现在可用于显式允许并将允许的流量添加到允许列表。  可以创建整个虚拟数据中心，并通过 NSG 决定其提供的内容和可用的时间。  如果虚拟机资源的子网或 NIC 上没有 NSG，禁止流量到达此资源。

若要详细了解 NSG 以及如何将其应用于自己的方案，请参阅[网络安全组](../virtual-network/security-overview.md)。

### <a name="outbound"></a>出站连接

负载均衡器支持入站和出站方案。  对于出站连接，标准负载均衡器与基本负载均衡器之间存在明显差异。

源网络地址转换 (SNAT) 用于将虚拟网络上的内部专用 IP 地址映射到负载均衡器前端的公共 IP 地址。

标准负载均衡器为实现[更可靠、可缩放且可预测的 SNAT 算法](load-balancer-outbound-connections.md#snat)引入了新算法，并启用新功能、去除多义性并强制实现显式配置，不会产生副作用。 若要允许新功能的出现，这些更改是必要的。 

使用标准负载均衡器时，请牢记以下关键原则：

- 驱动负载均衡器资源的是规则完成。  Azure 的所有编程均派生自其配置。
- 多个前端可用时，会使用所有前端，每个前端成倍增加可用的 SNAT 端口数。
- 如果不希望某特定前端用于出站连接，可进行选择和控制。
- 出站方案处于显式状态，指定出站连接后，该连接才会存在。
- 负载均衡规则推断 SNAT 的编程方式。 负载均衡规则特定于协议。 SNAT 特定于协议，配置应反映这一点，而不是产生副作用。

#### <a name="multiple-frontends"></a>多个前端
希望出现或已遇到出站连接的高需求时，如果想要更多 SNAT 端口，还可以通过配置其他前端、规则和后端池，将增量 SNAT 端口库存添加到相同的虚拟机资源。

#### <a name="control-which-frontend-is-used-for-outbound"></a>控制用于出站的前端
如果要将出站连接限制为仅来自于特定前端 IP 地址，可以按需在表示出站映射的规则上禁用出站 SNAT。

#### <a name="control-outbound-connectivity"></a>控制出站连接
标准负载均衡器存在于虚拟网络的上下文中。  虚拟网络是独立的专用网络。  除非存在与公共 IP 地址的关联，否则不允许公共连接。  可以访问 [VNet 服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)，因为它们在虚拟网络内部并位于本地。  若要对虚拟网络外部的目标建立出站连接，可执行以下两个选项：
- 将标准 SKU 公共 IP 地址作为实例层级公共 IP 地址分配到虚拟机资源；
- 或者，将虚拟机资源放入公共标准负载均衡器的后端池中。

上述两个选项均允许通过出站连接从虚拟网络访问虚拟网络的外部。 

如果只有  一个内部标准负载均衡器与虚拟机资源所在的后端池关联，虚拟机仅可以访问虚拟网络资源和 [VNet 终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。  可以按照上一段描述的步骤创建出站连接。

未与标准 SKU 关联的虚拟机资源的出站连接保持不变。

请查看[有关出站连接的详细讨论](load-balancer-outbound-connections.md)。

### <a name="multife"></a>多个前端
负载均衡器使用多个前端支持多项规则。  标准负载均衡器将其扩展到出站方案。  出站方案与入站负载均衡规则实质上存在逆反关系。  入站负载均衡规则还创建了出站连接的关联。 标准负载均衡器通过负载均衡规则使用与虚拟机资源关联的所有前端。  此外，使用负载均衡规则上的参数可以为了出站连接取消负载均衡规则，并允许选择特定前端（包括无前端）。

为进行比较，基本负载均衡器随机选择一个前端，且无法控制选择哪一个前端。

请查看[有关出站连接的详细讨论](load-balancer-outbound-connections.md)。

### <a name="operations"></a>管理操作

标准负载均衡器资源存在于全新的基础结构平台中。  这使得标准 SKU 可以提高管理操作的速度，对于每个标准 SKU 资源，完成时间通常少于 30 秒。  当后端池增大时，其更改所需的持续时间也随之延长。

可以修改标准负载均衡器资源，显著提高在虚拟机之间移动标准公共 IP 地址的速度。

## <a name="migration-between-skus"></a>SKU 之间的迁移

SKU 不可变。 按照本部分中的步骤从一个资源 SKU 移动到另一个资源 SKU。

>[!IMPORTANT]
>全面查看本文档，了解 SKU 之间的差异并仔细检查你的方案。  可能需要进行其他更改，以与你的方案一致。

### <a name="migrate-from-basic-to-standard-sku"></a>从基本 SKU 迁移到标准 SKU

1. 根据需要创建新的标准版资源（负载均衡器和公共 IP）。 重新创建规则和探测定义。  如果之前在使用针对 443/tcp 的 TCP 探测，请考虑将此探测协议更改为 HTTPS 探测并添加路径。

2. 为 NIC 或子网创建新的 NSG 或更新现有 NSG，以便将负载均衡流量、探测以及你想要允许的任何其他流量加入允许列表。

3. 如果适用，从所有 VM 实例中删除基本 SKU 资源（负载均衡器和公共 IP）。 确保还会删除可用性集的所有 VM 实例。

4. 将所有 VM 实例附加到新的标准 SKU 资源。

### <a name="migrate-from-standard-to-basic-sku"></a>从标准 SKU 迁移到基本 SKU

1. 根据需要创建新的基本版资源（负载均衡器和公共 IP）。 重新创建规则和探测定义。  将 HTTPS 探测更改为针对 443/tcp 的 TCP 探测。 

2. 如果适用，从所有 VM 实例中删除标准 SKU 资源（负载均衡器和公共 IP）。 确保还会删除可用性集的所有 VM 实例。

3. 将所有 VM 实例附加到新的基本 SKU 资源。

>[!IMPORTANT]
>
>使用基本 SKU 和标准 SKU 具有以下限制。
>
>标准 SKU 的 HA 端口和诊断只能在标准 SKU 中使用。 无法从标准 SKU 迁移到基本 SKU，并同时保留这些功能。
>
>根据本文所述，基本和标准 SKU 存在一定差异。  请确保理解这些差异并做好相应准备。
>
>必须对负载均衡器和公共 IP 资源使用匹配的 SKU。 不能混合使用基本 SKU 资源和标准 SKU 资源。 无法将独立的虚拟机、可用性集资源中的虚拟机或虚拟机规模集资源同时附加到两个 SKU。

## <a name="region-availability"></a>上市区域

标准负载均衡器目前已在所有公有云区域推出。

## <a name="sla"></a>SLA

可以使用标准负载均衡器，其 SLA 为 99.99%。  有关详细信息，请查看[标准负载均衡器 SLA](https://aka.ms/lbsla)。

## <a name="pricing"></a>定价

使用标准负载均衡器是收费的。

- 已配置的负载均衡规则和出站规则的数量（入站 NAT 规则不计入规则总数）
- 处理的入站和出站数据的数量，与规则无关。 

有关标准负载均衡器的定价信息，请访问[负载均衡器定价](https://azure.microsoft.com/pricing/details/load-balancer/)页。

## <a name="limitations"></a>限制

- SKU 不可变。 无法更改现有资源的 SKU。
- 独立的虚拟机资源、可用性集资源或虚拟机规模集资源可以引用一个 SKU，绝不能同时引用两个。
- 负载均衡器规则不能跨越两个虚拟网络。  前端及其相关的后端实例必须位于相同的虚拟网络中。  
- 标准 SKU LB 和 PIP 资源不支持[移动订阅操作](../azure-resource-manager/resource-group-move-resources.md)。
- 由于 VNet 之前的服务和其他平台服务功能的副作用，如果仅使用内部标准负载均衡器，则可以访问没有 VNet 和其他 Microsoft 平台服务的辅助角色。 请勿依赖此服务，因为相应的服务本身或底层平台可能会在不通知的情况下进行更改。 在仅使用内部标准负载均衡器时，必须始终假定需要明确创建[出站连接](load-balancer-outbound-connections.md)。
- 负载均衡器属于 TCP 或 UDP 产品，用于对这些特定的 IP 协议进行负载均衡和端口转发。  负载均衡规则和入站 NAT 规则支持 TCP 和 UDP，但不支持其他 IP 协议（包括 ICMP）。 负载均衡器不会终止、响应 UDP 或 TCP 流的有效负载，也不与之交互。 它不是一个代理。 必须使用负载均衡或入站 NAT 规则（TCP 或 UDP）中所用的同一协议在带内成功验证与前端的连接，并且必须至少有一个虚拟机为客户端生成了响应，这样才能看到前端发出的响应。   未从前端负载均衡器收到带内响应表明没有任何虚拟机能够做出响应。  在虚拟机都不能做出响应的情况下，无法与负载均衡器前端交互。  这一点也适用于出站连接，其中的[端口伪装 SNAT](load-balancer-outbound-connections.md#snat) 仅支持 TCP 和 UDP；其他任何 IP 协议（包括 ICMP）也会失败。  分配实例级公共 IP 地址即可缓解问题。
- 公共负载均衡器在将虚拟网络中的专用 IP 地址转换为公共 IP 地址时提供[出站连接](load-balancer-outbound-connections.md)，而内部负载均衡器则与此不同，它不会将出站发起连接转换为内部负载均衡器的前端，因为两者都位于专用的 IP 地址空间中。  这可以避免不需要转换的唯一内部 IP 地址空间内发生 SNAT 耗尽。  负面影响是，如果来自后端池中 VM 的出站流尝试流向该 VM 所在池中内部负载均衡器的前端，并映射回到自身，则这两个流的分支不会匹配，并且该流将会失败。   如果该流未映射回到后端池中的同一 VM（在前端中创建了流的 VM），则该流将会成功。   如果流映射回到自身，则出站流显示为源自 VM 并发往前端，并且相应的入站流显示为源自 VM 并发往自身。 从来宾 OS 的角度看，同一流的入站和出站部分在虚拟机内部不匹配。 TCP 堆栈不会将同一流的这两半看作是同一流的组成部分，因为源和目标不匹配。  当流映射到后端池中的任何其他 VM 时，流的两半将会匹配，且 VM 可以成功响应流。  此方案的症状是间歇性的连接超时。 可通过几种常用解决方法来可靠地实现此方案（从后端池发起流，并将其传送到后端池的相应内部负载均衡器前端），包括在内部负载均衡器的后面插入第三方代理，或[使用 DSR 式规则](load-balancer-multivip-overview.md)。  尽管可以使用公共负载均衡器来缓解问题，但最终的方案很容易导致 [SNAT 耗尽](load-balancer-outbound-connections.md#snat)，除非有精心的管理，否则应避免这种做法。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。
- 了解[运行状况探测](load-balancer-custom-probe-overview.md)。
- 详细了解[可用性区域](../availability-zones/az-overview.md)。
- 了解有关[标准负载均衡器诊断](load-balancer-standard-diagnostics.md)的信息。
- 在 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) 中了解用于诊断的[支持的多维度指标](../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers)。
- 了解如何[对出站连接使用负载均衡器](load-balancer-outbound-connections.md)。
- 了解[出站规则](load-balancer-outbound-rules-overview.md)。
- 了解如何[在空闲时重置 TCP](load-balancer-tcp-reset.md)。
- 了解[具有 HA 端口负载均衡规则的标准负载均衡器](load-balancer-ha-ports-overview.md)。
- 了解如何使用[具有多个前端的负载均衡器](load-balancer-multivip-overview.md)。
- 了解有关[虚拟网络](../virtual-network/virtual-networks-overview.md)的信息。
- 详细了解[网络安全组](../virtual-network/security-overview.md)。
- 了解 [VNet 服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。
- 了解 Azure 的部分其他关键[网络功能](../networking/networking-overview.md)。
- 详细了解[负载均衡器](load-balancer-overview.md)。
