---
title: 什么是 Azure 负载均衡器？
titlesuffix: Azure Load Balancer
description: Azure 负载均衡器功能、体系结构和实现概述。 了解负载均衡器工作原理，并在云中对其进行利用。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/11/2019
ms.author: allensu
ms.openlocfilehash: 349d8afd46a06455edcd25e2a7ea48f407d285ef
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130418"
---
# <a name="what-is-azure-load-balancer"></a>什么是 Azure 负载均衡器？

使用 Azure 负载均衡器可以缩放应用程序，并为服务创建高可用性。 负载均衡器支持入站和出站方案、提供低延迟和高吞吐量，以及为所有 TCP 和 UDP 应用程序纵向扩展到数以百万计的流。  

负载均衡器根据规则和运行状况探测，将抵达负载均衡器前端的新入站流量分配到后端池实例。 

此外，公共负载均衡器还可将虚拟网络中虚拟机 (VM) 的专用 IP 地址转换为公共 IP 地址，从而为这些虚拟机提供出站连接。

Azure 负载均衡器提供了两种 SKU ：“基本”和“标准”。 规模、功能和定价方面有差异。 尽管使用基本负载均衡器可以实现的任何场景也可以通过标准负载均衡器来创建，但创建方法略有不同。 在学习负载均衡器的过程中，必须熟悉基础知识和 SKU 方面的差异。

## <a name="why-use-load-balancer"></a>为何使用负载均衡器？ 

使用 Azure 负载均衡器可以：

* 对传入到 VM 的 Internet 流量进行负载均衡。 此配置称为[公共负载均衡器](#publicloadbalancer)。
* 对虚拟网络中 VM 之间的流量进行负载均衡。 还可以在混合方案中从本地网络访问负载均衡器前端。 这两种方案都使用称作[内部负载均衡器](#internalloadbalancer)的配置。
* 使用入站网络地址转换 (NAT) 规则通过端口转发将流量转发到特定 VM 上的特定端口。
* 使用公共负载均衡器为虚拟网络中的 VM 提供[出站连接](load-balancer-outbound-connections.md)。


>[!NOTE]
> Azure 为方案提供了一套完全托管的负载均衡解决方案。 若要寻求传输层安全性 (TLS) 协议终止（“SSL 卸载”）或每个 HTTP/HTTPS 请求的应用层处理，请查看[应用程序网关](../application-gateway/application-gateway-introduction.md)。 若要寻求全局 DNS 负载均衡，请查看[流量管理器](../traffic-manager/traffic-manager-overview.md)。 端到端场景可从结合所需的解决方案中受益。

## <a name="what-are-load-balancer-resources"></a>什么是负载均衡器资源？

负载均衡器资源可以是公共负载均衡器或内部负载均衡器。 负载均衡器资源的功能表示为前端、规则、运行状况探测和后端池定义。 通过从 VM 指定后端池，将 VM 放入后端池。

负载均衡器资源是一些对象，可在其中表述 Azure 应如何设定其多租户基础结构，以实现想要创建的方案。 负载均衡器资源与实际基础结构之间不存在直接的关系。 创建负载均衡器不会创建实例，容量始终可用。 

## <a name="fundamental-load-balancer-features"></a>基本的负载均衡器功能

负载均衡器为 TCP 和 UDP 应用程序提供以下基本功能：

* **负载均衡**

    使用 Azure 负载均衡器可以创建负载均衡规则，以便将抵达前端的流量分配到后端池实例。 负载均衡器使用基于哈希的算法来分配入站流量，并相应地重写发往后端池实例的流量的标头。 当运行状况探测指示后端终结点运行正常时，可以使用一个服务器来接收新流量。
    
    默认情况下，负载均衡器使用 5 元组哈希（包括源 IP 地址、源端口、目标 IP 地址、目标端口和 IP 协议编号）将流量映射到可用服务器。 可以启用给定规则的 2 元组或 3 元组哈希，来与特定的源 IP 地址创建关联。 同一数据包流量的所有数据包将会抵达负载均衡前端后面的同一实例。 当客户端从同一源 IP 发起新流量时，源端口将会更改。 因此，5 元组可能导致流量定向到不同的后端终结点。

    有关详细信息，请参阅[负载均衡器分配模式](load-balancer-distribution-mode.md)。 下图显示了基于哈希的分配：

    ![基于哈希的分发](./media/load-balancer-overview/load-balancer-distribution.png)

    图：  基于哈希的分发

* **端口转发**

    使用 Azure 负载均衡器可以创建入站 NAT 规则，以便通过端口转发，将来自特定前端 IP 地址的特定端口的流量转发到虚拟网络中特定后端实例的特定端口。 也可以通过与负载均衡相同的基于哈希的分配来实现此目的。 此功能的常见应用方案是与 Azure 虚拟网络中的单个 VM 实例建立远程桌面协议 (RDP) 或安全外壳 (SSH) 会话。 可将多个内部终结点映射到相同前端 IP 地址上的不同端口。 可以使用前端 IP 地址通过 Internet 远程管理 VM，而无需额外配置跳转盒。

* **应用程序不可知性和透明性**

    负载均衡器不直接与 TCP、UDP 或应用层交互，可支持任何 TCP 或 UDP 应用方案。  负载均衡器不会终止或发起流、不会与流的有效负载交互，也不会提供任何应用层网关功能。协议握手始终在客户端与后端池实例之间直接发生。  对入站流做出的响应始终是来自虚拟机的响应。  当流抵达虚拟机时，也会保留原始的源 IP 地址。  下面通过几个示例来进一步演示透明度：
    - 每个终结点仅由某个 VM 应答。  例如，TCP 握手始终在客户端与选定的后端 VM 之间发生。  前端请求的响应是由后端 VM 生成的。 成功验证与前端的连接后，将会验证与至少一个后端虚拟机的端到端连接。
    - 应用程序有效负载对负载均衡器透明，可支持任何 UDP 或 TCP 应用程序。 对于需要根据 HTTP 请求进行处理或操作应用层有效负载（例如，分析 HTTP URL）的工作负荷，应使用[应用程序网关](https://azure.microsoft.com/services/application-gateway)等第 7 层负载均衡器。
    - 由于负载均衡器不能识别 TCP 有效负载，并且不会提供 TLS 卸载（“SSL”），因此，你可以使用负载均衡器构建端到端的已加密方案，并通过在 VM 本身上终止 TLS 连接对 TLS 应用程序进行大规模的横向扩展。  例如，只会根据添加到后端池的 VM 类型和数目限制 TLS 会话密钥容量。  如果需要“SSL 卸载”、应用层处理或想要将证书管理权委托给 Azure，应改用 Azure 的第 7 层负载均衡器[应用程序网关](https://azure.microsoft.com/services/application-gateway)。
        

* **自动重新配置**

    增加或减少实例时，负载均衡器会立即自行重新配置。 在后端池中添加或删除 VM 后，会重新配置负载均衡器，无需针对负载均衡器资源执行其他操作。

* **运行状况探测**

    为确定后端池中实例的运行状况，负载均衡器会使用定义的运行状况探测。 当探测无法响应时，负载均衡器会停止向状况不良的实例发送新连接。 现有连接不受影响，会一直保留到应用程序终止了流、发生空闲超时或 VM 关闭为止。
     
    负载均衡器为 TCP、HTTP 和 HTTPS 终结点提供了[不同的运行状况探测类型](load-balancer-custom-probe-overview.md#types)。

    此外，使用经典云服务时允许使用其他类型：[来宾代理](load-balancer-custom-probe-overview.md#guestagent)。  这应作为运行状况探测的最后手段，当其他选项可行时不建议使用此选项。
    
* **出站连接 (SNAT)**

    从虚拟网络中的专用 IP 地址发往 Internet 上的公共 IP 地址的所有出站流量可以转换为负载均衡器的前端 IP 地址。 通过负载均衡规则将公共前端绑定到后端 VM 后，Azure 会将出站连接设定为自动转换成公共前端的 IP 地址。

  * 可以轻松地对服务进行升级和灾难恢复操作，因为前端可以动态映射到服务的其他实例。
  * 简化了访问控制列表 (ACL) 管理。 以前端 IP 表示的 ACL 不会随着服务的缩放或重新部署而更改。  将出站连接转换为较小数量的 IP 地址而不是计算机，可以减少允许列表的负担。

    有关详细信息，请参阅[出站连接](load-balancer-outbound-connections.md)。

除这些基本功能以外，标准负载均衡器还提供其他特定于 SKU 的功能。 有关详细信息，请查看本文的余下部分。

## <a name="skus"></a>负载均衡器 SKU 的比较

负载均衡器支持“基本”和“标准”SKU，两者的方案规模、功能和定价有差别。 使用基本负载均衡器可以实现的任何场景也可以通过标准负载均衡器来创建。 事实上，这两个 SKU 的 API 类似，都可以通过 SKU 的规范来调用。 从 2017-08-01 API 开始，提供了支持负载均衡器和公共 IP 的 SKU 的 API。 这两个 SKU 具有相同的常规 API 和结构。

但是，根据所选的 SKU，完整的方案配置可能略有不同。 如果某篇文章仅适用于特定的 SKU，负载均衡器文档中会做出相应的标识。 请参阅下表来比较和了解差别。 有关详细信息，请参阅[标准负载均衡器概述](load-balancer-standard-overview.md)。

>[!NOTE]
> 新设计应采用标准负载均衡器。 

独立 VM、可用性集和虚拟机规模集只能连接到一个 SKU，永远无法同时连接到两个 SKU。 与公共 IP 地址配合使用时，负载均衡器和公共 IP 地址 SKU 必须匹配。 负载均衡器和公共 IP SKU 不可变。

_最佳做法是显式指定 SKU，尽管目前不强制要求这样做。_  目前，所需的更改保持在最低的限度。 如果未指定 SKU，则认为有意使用基本 SKU 的 2017-08-01 API 版本。

>[!IMPORTANT]
>标准负载均衡器是一款新的负载均衡器产品，在很大程度上是基本负载均衡器的超集。 这两款产品之间存在重要的且有意而为的差异。 使用基本负载均衡器可以实现的任何端到端方案也可以通过标准负载均衡器来创建。 如果你惯于使用基本负载均衡器，则应该也对标准负载均衡器很熟悉，能够理解两者之间的最新行为差异，以及这种差异造成的影响。 请认真阅读本部分。

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

有关详细信息，请参阅[负载均衡器的服务限制](https://aka.ms/lblimits)。 对于标准负载均衡器，请参阅[概述](load-balancer-standard-overview.md)、[定价](https://aka.ms/lbpricing)和 [SLA](https://aka.ms/lbsla)。

## <a name="concepts"></a>概念

### <a name = "publicloadbalancer"></a>公共负载均衡器

公共负载均衡器将传入流量的公共 IP 地址和端口号映射到 VM 的专用 IP 地址和端口号，对于来自 VM 的响应流量，则进行反向的映射。 应用负载均衡规则，可在多个 VM 或服务之间分配特定类型的流量。 例如，可将 Web 请求流量负载分配到多个 Web 服务器。

下图显示了公共端口和 TCP 端口 80 之间的 Web 流量的负载均衡终结点，该流量由三个 VM 共享。 三个 VM 位于负载均衡集中。

![公共负载均衡器示例](./media/load-balancer-overview/IC727496.png)

图：*使用公共负载均衡器对 Web 流量进行负载均衡*

当 Internet 客户端将网页请求发送到 TCP 端口 80 上的 Web 应用的公共 IP 地址时，Azure 负载均衡器会在负载均衡集中的三个 VM 之间分配请求。 有关负载均衡器算法的详细信息，请参阅本文的[负载均衡器功能](load-balancer-overview.md##fundamental-load-balancer-features)部分。

默认情况下，Azure 负载均衡器在多个 VM 实例之间平均分发网络流量。 还可以配置会话关联。 有关详细信息，请参阅[负载均衡器分配模式](load-balancer-distribution-mode.md)。

### <a name = "internalloadbalancer"></a>内部负载均衡器。

内部负载均衡器仅将流量定向到虚拟网络中的资源，或定向到使用 VPN 访问 Azure 基础结构的资源。 在此方面，内部负载均衡器不同于公共负载均衡器。 Azure 基础结构会限制对虚拟网络的负载均衡前端 IP 地址的访问。 前端 IP 地址和虚拟网络不会直接在 Internet 终结点上公开。 内部业务线应用程序可在 Azure 中运行，并可从 Azure 内或从本地资源访问这些应用程序。

内部负载均衡器支持以下类型的负载均衡：

* **在虚拟网络中**：从虚拟网络中的 VM 负载均衡到驻留在同一虚拟网络中的一组 VM。
* **对于跨界虚拟网络**：从本地计算机负载均衡到驻留在同一虚拟网络中的一组 VM。 
* **对于多层应用程序**：针对面向 Internet 的多层应用程序进行负载均衡，其中后端层不面向 Internet。 后端层需要针对面向 Internet 的层发出的流量进行负载均衡（参阅下图）。
* **对于业务线应用程序**：使托管在 Azure 中的业务线应用程序实现负载均衡，而无需其他负载均衡器硬件或软件。 此方案将本地服务器包含在一组流量已实现负载均衡的计算机中。

![内部负载均衡器示例](./media/load-balancer-overview/IC744147.png)

图：  使用公共和内部负载均衡器对多层应用程序进行负载均衡

## <a name="pricing"></a>定价

使用标准负载均衡器是收费的。

- 已配置的负载均衡规则和出站规则的数量（入站 NAT 规则不计入规则总数）
- 处理的入站和出站数据的数量，与规则无关。 

有关标准负载均衡器的定价信息，请访问[负载均衡器定价](https://azure.microsoft.com/pricing/details/load-balancer/)页。

基本负载均衡器是免费提供的。

## <a name="sla"></a>SLA

有关标准负载均衡器 SLA 的信息，请访问[负载均衡器 SLA](https://aka.ms/lbsla) 页。 

## <a name="limitations"></a>限制

- 负载均衡器属于 TCP 或 UDP 产品，用于对这些特定的 IP 协议进行负载均衡和端口转发。  负载均衡规则和入站 NAT 规则支持 TCP 和 UDP，但不支持其他 IP 协议（包括 ICMP）。 负载均衡器不会终止、响应 UDP 或 TCP 流的有效负载，也不与之交互。 它不是一个代理。 必须使用负载均衡或入站 NAT 规则（TCP 或 UDP）中所用的同一协议在带内成功验证与前端的连接，并且必须至少有一个虚拟机为客户端生成了响应，这样才能看到前端发出的响应  。  未从前端负载均衡器收到带内响应即表明没有任何虚拟机能够做出响应。  在虚拟机都不能做出响应的情况下，无法与负载均衡器前端交互。  这一点也适用于出站连接，其中的[端口伪装 SNAT](load-balancer-outbound-connections.md#snat) 仅支持 TCP 和 UDP；其他任何 IP 协议（包括 ICMP）也会失败。  分配实例级公共 IP 地址即可缓解问题。
- 公共负载均衡器在将虚拟网络中的专用 IP 地址转换为公共 IP 地址时提供[出站连接](load-balancer-outbound-connections.md)，而内部负载均衡器则与此不同，它不会将出站发起连接转换为内部负载均衡器的前端，因为两者都位于专用 IP 地址空间中。  这可以避免不需要转换的唯一内部 IP 地址空间内发生 SNAT 端口耗尽。  负面影响是，如果来自后端池中 VM 的出站流尝试流向其所在池中的内部负载均衡器前端，并映射回到自身，则这两个流的分支不会匹配，并且该流将会失败  。  如果该流未映射回到后端池中的同一 VM（在前端中创建了流的 VM），则该流将会成功。   如果流映射回到自身，则出站流显示为源自 VM 并发往前端，并且相应的入站流显示为源自 VM 并发往自身。 从来宾 OS 的角度看，同一流的入站和出站部分在虚拟机内部不匹配。 TCP 堆栈不会将同一流的这两半看作是同一流的组成部分，因为源和目标不匹配。  当流映射到后端池中的任何其他 VM 时，流的两半将会匹配，且 VM 可以成功响应流。  此方案的缺点在于，当流返回到发起该流的同一后端时将出现间歇性的连接超时。 可通过几种常用解决方法来可靠地实现此方案（从后端池发起流，并将其传送到后端池的相应内部负载均衡器前端），包括在内部负载均衡器后方插入代理层，或[使用 DSR 式规则](load-balancer-multivip-overview.md)。  客户可将内部负载均衡器与任何第三方代理相结合，或使用内部[应用程序网关](../application-gateway/application-gateway-introduction.md)替代限制为 HTTP/HTTPS 的代理方案。 尽管可以使用公共负载均衡器来缓解问题，但最终的方案很容易导致 [SNAT 耗尽](load-balancer-outbound-connections.md#snat)，除非有精心的管理，否则应避免这种做法。
- 通常，负载均衡规则不支持转发 IP 片段或对 UDP 和 TCP 数据包执行 IP 分段。  [HA 端口负载均衡规则](load-balancer-ha-ports-overview.md)是此一般声明的例外，可用于转发现有 IP 片段。

## <a name="next-steps"></a>后续步骤

现在，我们已大致了解了 Azure 负载均衡器。 若要开始使用负载均衡器，请创建一个负载均衡器，在安装自定义 IIS 扩展的情况下创建 VM，然后对 VM 之间的 Web 应用进行负载均衡。 若要了解操作方法，请参阅[创建基本负载均衡器](quickstart-create-basic-load-balancer-portal.md)快速入门。
