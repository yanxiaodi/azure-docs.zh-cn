---
title: 使用 Apache Kafka 应用中的事件中心 - Azure 事件中心 | Microsoft Docs
description: 本文介绍有关 Azure 事件中心提供的 Apache Kafka 支持的信息。
services: event-hubs
documentationcenter: .net
author: shvija
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 8bf381e7c66e06bbaa140ed865f0f7c9b4f001af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821701"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>使用 Apache Kafka 应用程序中的 Azure 事件中心
事件中心提供 Kafka 终结点，现有的基于 Kafka 的应用程序可将该终结点用作运行你自己的 Kafka 群集的替代方案。 事件中心支持 [Apache Kafka 协议 1.0 及更高版本](https://kafka.apache.org/documentation/)，并且可与现有 Kafka 应用程序（包括 MirrorMaker）配合使用。  

## <a name="what-does-event-hubs-for-kafka-provide"></a>适用于 Kafka 的事件中心可提供什么？

适用于 Kafka 功能的事件中心基于 Azure 事件中心提供协议头，该协议头与 Kafka 1.0 和更高版本二进制兼容，可以读取和写入 Kafka 主题。 可以通过应用程序开始使用 Kafka 终结点，而无需更改任何代码，只需进行最小的配置更改。 更新配置中的连接字符串以指向事件中心公开的 Kafka 终结点，而不是指向 Kafka 群集。 然后，可以开始将使用 Kafka 协议的应用程序中的事件流式传输到事件中心。 此集成还支持 [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) 等框架，该框架目前处于预览状态。 

从概念上讲，Kafka 和事件中心几乎完全相同：它们都是为流式传输数据而生成的分区日志。 下表映射 Kafka 和事件中心之间的概念。

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka 和事件中心概念映射

| Kafka 概念 | 事件中心概念|
| --- | --- |
| 群集 | 命名空间 |
| 主题 | 事件中心 |
| Partition | Partition|
| 使用者组 | 使用者组 |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kafka 和事件中心之间的主要区别

[Apache Kafka](https://kafka.apache.org/) 是可在所选的任意位置运行的软件，而事件中心是类似 Azure Blob 存储的云服务。 没有需要管理的服务器或网络，也没有需要配置的代理。 你可创建一个命名空间（它是主题所在的 FQDN），然后在该命名空间内创建事件中心或主题。 有关事件中心和命名空间的详细信息，请参阅[事件中心功能](event-hubs-features.md#namespace)。 作为云服务，事件中心使用单一稳定的虚拟 IP 地址作为终结点，因此客户端无需了解群集中代理或计算机的情况。 

事件中心的规模由购买的吞吐量单位数量控制，每个吞吐量单位可提供每秒 1 MB 的速度或每秒 1,000 个事件的流入量。 默认情况下，如果达到限制，事件中心会使用[自动膨胀](event-hubs-auto-inflate.md)功能按比例增加吞吐量单位；此功能也可与适用于 Kafka 功能的事件中心配合使用。 

### <a name="security-and-authentication"></a>安全性和身份验证

Azure 事件中心要求所有通信都使用 SSL 或 TLS，并要求使用共享访问签名 (SAS) 进行身份验证。 此要求同样适用于事件中心内的 Kafka 终结点。 为了与 Kafka 兼容，事件中心使用 SASL PLAIN 进行身份验证，并使用 SASL SSL 确保传输安全。 有关事件中心安全性的详细信息，请参阅[事件中心身份验证和安全性](event-hubs-authentication-and-security-model-overview.md)。

## <a name="other-event-hubs-features-available-for-kafka"></a>Kafka 可用的其他事件中心功能

借助适用于 Kafka 功能的事件中心，你能够使用一种协议写入并使用另一种协议读取，以便当前 Kafka 生成方可通过 Kafka 继续发布，而你可使用 Azure 流分析或 Azure Functions 等事件中心添加读取器。 此外，[捕获](event-hubs-capture-overview.md)和[异地灾难恢复](event-hubs-geo-dr.md)等事件中心功能也可与适用于 Kafka 功能的事件中心配合使用。

## <a name="features-that-are-not-yet-supported"></a>尚不支持的功能 

下面是尚不支持的 Kafka 功能列表：

*   幂等生成方
*   事务
*   压缩
*   基于大小的保留
*   日志压缩
*   将分区添加到现有主题
*   HTTP Kafka API 支持
*   Kafka Stream

## <a name="next-steps"></a>后续步骤

本文介绍了适用于 Kafka 的事件中心。 若要了解详细信息，请参阅下列链接：

- [如何创建启用 Kafka 的事件中心](event-hubs-create-kafka-enabled.md)
- [从 Kafka 应用程序流式传输到事件中心](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [将 Kafka 代理镜像到已启用 Kafka 的事件中心](event-hubs-kafka-mirror-maker-tutorial.md)
- [将 Apache Spark 连接到已启用 Kafka 的事件中心](event-hubs-kafka-spark-tutorial.md)
- [将 Apache Flink 连接到已启用 Kafka 的事件中心](event-hubs-kafka-flink-tutorial.md)
- [将 Kafka Connect 与已启用 Kafka 的事件中心集成](event-hubs-kafka-connect-tutorial.md)
- [将 Akka Streams 连接到已启用 Kafka 的事件中心](event-hubs-kafka-akka-streams-tutorial.md)
- [了解 GitHub 上的示例](https://github.com/Azure/azure-event-hubs-for-kafka)


