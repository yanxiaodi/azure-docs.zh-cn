---
title: 可用性和一致性 - Azure 事件中心 | Microsoft Docs
description: 如何使用分区在 Azure 事件中心中提供最大程度的可用性和一致性。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 425f4d9dbd6478af834bee6c88d0f13bdaa45b16
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273686"
---
# <a name="availability-and-consistency-in-event-hubs"></a>事件中心中的可用性和一致性

## <a name="overview"></a>概述
Azure 事件中心使用[分区模型](event-hubs-scalability.md#partitions)在单个事件中心内提高可用性和并行化。 例如，如果事件中心具有四个分区，并且其中一个分区要在负载均衡操作中从一台服务器移动到另一台服务器，则仍可以通过其他三个分区进行发送和接收。 此外，具有更多分区可以让更多并发读取器处理数据，从而提高聚合吞吐量。 了解分布式系统中分区和排序的意义是解决方案设计的重要方面。

为了帮助说明排序与可用性之间的权衡，请参阅 [CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)（也称为 Brewer 的定理）。 此定理论述了如何在一致性、可用性和分区容差之间进行选择。 它指出对于由网络分区的系统，始终在一致性与可用性之间作出权衡。

Brewer 的定理按如下所示定义一致性和可用性：
* 分区容差：系统即使在出现分区故障时也能继续处理数据的数据处理能力。
* 可用性：非故障节点在合理时间量内返回合理响应（没有错误或超时）。
* 一致性：保证读取针对给定客户端返回最新写入。

## <a name="partition-tolerance"></a>分区容差
事件中心在分区数据模型的基础上构建。 可以在设置过程中配置事件中心内的分区数，但以后无法更改此值。 由于必须对事件中心使用分区，因此需要在应用程序的可用性和一致性方面进行决策。

## <a name="availability"></a>可用性
开始使用事件中心的最简单方法是使用默认行为。 如果创建新的 **[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)** 对象并使用 **[Send](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync?view=azure-dotnet#Microsoft_Azure_EventHubs_EventHubClient_SendAsync_Microsoft_Azure_EventHubs_EventData_)** 方法，会自动在事件中心内的各个分区之间分发这些事件。 此行为可实现最大运行时间量。

对于需要最大运行时间的用例，此模型是首选模型。

## <a name="consistency"></a>一致性
在某些方案中，事件的排序可能十分重要。 例如，可能希望后端系统在删除命令之前处理更新命令。 在此情况下，可以对事件设置分区键，也可以使用 `PartitionSender` 对象仅将事件发送到特定分区。 这样做可确保从分区读取这些事件时，按顺序读取它们。

使用此配置，请记住，如果发送到的特定分区不可用，则会收到错误响应。 作为对比，如果未与单个分区关联，则事件中心服务会将事件发送到下一个可用分区。

确保排序的一个可能解决方法（同时还最大限度地延长运行时间）是将事件作为事件处理应用程序的一部分进行聚合。 实现此目的的最简单方法是使用自定义序号属性标记事件。 以下代码展示一个示例：

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

此示例将事件发送到事件中心内的一个可用分区，并从应用程序设置对应序号。 此解决方案要求处理应用程序保持状态，不过会为发送者提供更可能可用的终结点。

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [事件中心服务概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
