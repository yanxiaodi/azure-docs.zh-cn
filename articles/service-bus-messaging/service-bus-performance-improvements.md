---
title: 使用 Azure 服务总线提高性能的最佳做法 | Microsoft 文档
description: 介绍如何使用服务总线在交换中转消息时优化性能。
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: f5ce8a237bc2ba7fe15acfcd6afa0edcda7ef713
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60589647"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>使用服务总线消息传递改进性能的最佳实践

本文介绍如何使用 Azure 服务总线在交换中转消息时优化性能。 本文的第一部分介绍有助于提高性能的各种不同机制。 第二部分指导用户如何针对给定方案以能够提供最佳性能的方式使用服务总线。

在本文中，术语“客户端”是指任何访问服务总线的实体。 客户端可以充当发送方或接收方的角色。 术语“发件人”用于将消息发送到服务总线队列或主题订阅的服务总线队列或主题客户端。 术语“接收方”是指从服务总线队列或订阅接收消息的服务总线队列或订阅客户端。

以下几部分介绍服务总线用以帮助提高性能的几个概念。

## <a name="protocols"></a>协议

服务总线支持客户端通过以下三种协议之一发送和接收消息：

1. 高级消息队列协议 (AMQP)
2. 服务总线邮件协议 (SBMP)
3. HTTP

AMQP 和 SBMP 更有效，因为只要消息工厂存在，它们便会维护与服务总线的连接。 它还实现批处理和预提取。 除非明确提到，本文中的所有内容都假定使用 AMQP 或 SBMP。

## <a name="reusing-factories-and-clients"></a>重用工厂和客户端

[QueueClient][QueueClient] 或 [MessageSender][MessageSender] 等服务总线客户端对象是通过 [MessagingFactory][MessagingFactory] 对象创建的，该对象还提供连接的内部管理。 发送消息后，建议不关闭消息工厂或队列、主题和订阅客户端，并在发送下一条消息时再重新创建它们。 关闭消息工厂将删除与服务总线服务的连接，并且会在重新创建工厂时建立新的连接。 建立连接是一项成本高昂的操作，可通过针对多个操作重复使用相同的工厂和客户端对象来避免这一操作。 这些客户端对象可安全地用于并发异步操作及从多个线程安全地使用。 

## <a name="concurrent-operations"></a>并发操作

执行某项操作（发送、接收、删除等）需要花费一定时间。 这一时间包括服务总线服务处理该操作的时间，外加延迟处理请求和答复的时间。 若要增加每次操作的数目，操作必须同时执行。 

客户端通过执行异步操作来计划并发操作。 前一个请求完成之前便启动下一个请求。 以下代码片段是异步发送操作的示例：
  
 ```csharp
  Message m1 = new BrokeredMessage(body);
  Message m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  以下代码是异步接收操作的示例。 请在[此处](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues)查看完整程序：
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>接收模式

创建队列或订阅客户端时，可以指定接收模式：*Peek-lock* 或 *Receive and Delete*。 默认接收模式是 [PeekLock][PeekLock]。 在此模式下操作时，客户端发送请求以从服务总线接收消息。 客户端收到消息后，将发送完成消息的请求。

如果将接收模式设置为 [ReceiveAndDelete][ReceiveAndDelete] 时，这两个步骤将合并到单个请求中。 这些步骤减少了操作的总体数目，并可以提高总消息吞吐量。 性能提高的同时也出现丢失消息的风险。

服务总线不支持“接收与删除”操作的事务。 此外，在客户端想要延迟消息或将其放入[死信队列](service-bus-dead-letter-queues.md)的情况下，需要使用扫视-锁定语义。

## <a name="client-side-batching"></a>客户端批处理

客户端批处理允许队列或主题客户端延迟一段时间发送消息。 如果客户端在这段时间内发送其他消息，则会将这些消息以单个批次传送。 客户端批处理还会导致队列或订阅客户端将多个**完成**请求批处理为单个请求。 批处理仅适用于异步**发送**和**完成**操作。 同步操作会立即发送到服务总线服务。 不会针对扫视或接收操作执行批处理，也不会跨客户端执行批处理。

默认情况下，客户端的批处理间隔时间为 20 毫秒。 可通过在创建消息工厂之前，设置 [BatchFlushInterval][BatchFlushInterval] 属性，更改批处理的间隔时间。 此设置会影响此工厂创建的所有客户端。

要禁用批处理，请将 [BatchFlushInterval][BatchFlushInterval] 属性设置为 **TimeSpan.Zero**。 例如：

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

批处理不会影响可计费的消息操作的数目，且仅适用于使用 [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 库的服务总线客户端协议。 HTTP 协议不支持批处理。

> [!NOTE]
> 设置 BatchFlushInterval 可确保批处理从应用程序的角度来看是隐式的。 例如，应用程序进行 SendAsync() 和 CompleteAsync() 调用而不进行具体的批量调用。
>
> 显式客户端批处理可以通过利用下述方法调用来实现 - 
> ```csharp
> Task SendBatchAsync (IEnumerable<BrokeredMessage> messages);
> ```
> 在这里，消息的组合大小必须小于定价层支持的最大大小。

## <a name="batching-store-access"></a>批处理存储访问

为了增加队列、主题或订阅的吞吐量，服务总线在写入其内部存储时会对多条消息进行批处理。 如果在队列或主题中启用，则会将消息批量写入存储区。 如果在队列或订阅中启用，则会从存储区批量删除消息。 如果对实体启用了批量存储访问，服务总线会将有关此实体的存储写入操作延迟多达 20 毫秒的时间。 

> [!NOTE]
> 使用批处理不存在丢失消息的风险，即使在 20 毫秒的批处理间隔结束时出现服务总线故障，也是如此。 

在此间隔期间发生的其他存储操作会被添加到此批中。 批量存储访问仅影响**发送**和**完成**操作；接收操作不会受到影响。 批量存储访问是实体上的一个属性。 将跨所有启用了批量存储访问的实体实施批处理。

在创建新队列、主题或订阅时，默认情况下启用批量存储访问。 要禁用批量存储访问，请在创建实体之前将 [EnableBatchedOperations][EnableBatchedOperations] 属性设置为 **false** 。 例如：

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

批量存储访问不会影响计费的消息传递操作数，它是队列、主题或订阅的属性。 它不依赖于接收模式以及客户端和服务总线服务之间所使用的协议。

## <a name="prefetching"></a>预提取

[预提取](service-bus-prefetch.md)允许队列或订阅客户端在执行接收操作时从服务加载其他消息。 客户端将这些消息存储在本地缓存中。 缓存大小由 [QueueClient.PrefetchCount][QueueClient.PrefetchCount] 属性或 [SubscriptionClient.PrefetchCount][SubscriptionClient.PrefetchCount] 属性决定。 启用预提取的每个客户端维护其自己的缓存。 客户端之间不共享缓存。 如果客户端启动接收操作，而其缓存是空的，则服务会传输一批消息。 批的大小等于缓存的大小或 256 KB，以二者中较小者为准。 如果客户端启动接收操作，而其缓存中包含一个消息，则将从缓存中取出消息。

预提取一条消息后，服务将锁定此预提取的消息。 通过此锁定操作，其他接收方则无法接收到此预提取的消息。 如果接收方在锁定过期之前无法完成此消息，则该消息便对其他接收方可用。 预提取的消息的副本则保留在缓存中。 使用过期的缓存副本的接收方会在尝试完成该消息时接收到一个异常。 默认情况下，消息锁定在 60 秒后过期。 这一值可延长到 5 分钟。 若要阻止过期消息的使用，缓存大小应始终小于客户端可在锁定超时间隔内使用的消息数。

使用 60 秒的默认锁定时限时，[PrefetchCount][SubscriptionClient.PrefetchCount] 的合理值是工厂所有接收方最大处理速率的 20 倍。 例如，某个工厂创建了 3 个接收方，并且每个接收方每秒可以处理最多 10 个消息。 预提取计数不应超过 20 X 3 X 10 = 600。 默认情况下，[PrefetchCount][QueueClient.PrefetchCount] 设置为 0，这表示不会从服务中提取额外消息。

预提取消息会增加队列或订阅的总体吞吐量，因为它减少了消息操作或往返行程的总数。 但是，提取第一条消息会耗用更长的时间（因消息大小增加所致）。 由于预提取的消息已经被客户端下载，因此预提取消息的接收速度将变快。

服务器会在向客户端发送消息时检查消息的“生存时间 (TTL)”属性。 收到邮件时，客户端不检查消息的 TTL 属性。 即使消息被客户端缓存时该消息的 TTL 已通过，该消息仍可被接收。

预提取不会影响可计费的消息操作的数目，且仅适用于服务总线客户端协议。 HTTP 协议不支持预提取。 预提取可用于同步和异步接收操作。

## <a name="prefetching-and-receivebatch"></a>预提取和 ReceiveBatch

在一起的多个消息预提取的概念有类似语义处理一批 (ReceiveBatch) 中的消息，而有利用它们合并到一起时必须牢记一些细微差别。

预提取 （QueueClient 和 SubscriptionClient） 在客户端上进行配置 （或模式） 和 ReceiveBatch 是具有的操作 （请求-响应语义）。

同时使用它们合并到一起，请考虑以下情况下的

* 预提取应大于或等于应为从 ReceiveBatch 接收的消息数。
* 预提取可达 n/3 时间处理每秒，其中 n 是默认锁定持续时间的消息数。

有一些难题使用贪婪方法 （即保持预提取计数非常高），因为它意味着该消息已锁定到特定接收方。 建议是尝试出预值提取之间上面提到的阈值并根据经验确定实际。

## <a name="multiple-queues"></a>多个队列

如果预期的负载不能由单个分区的队列或主题处理，则必须使用多个消息实体。 在使用多个实体时，为每个实体创建专用客户端，而不是针对所有实体使用同一个客户端。

## <a name="development-and-testing-features"></a>开发和测试功能

服务总线具有一项专门用于开发的功能，该功能**永远不应在生产配置中使用**：[TopicDescription.EnableFilteringMessagesBeforePublishing][]。

向主题添加了新规则或筛选器时，可以使用 [TopicDescription.EnableFilteringMessagesBeforePublishing][] 验证新的筛选器表达式是否按预期工作。

## <a name="scenarios"></a>方案

以下各节介绍典型的消息传递方案，并概述首选服务总线设置。 吞吐速率分为小（小于 1 条消息/秒）、中等（1 条消息/秒或更大，但不超过 100 条消息/秒）和高（100 条消息/秒或更大）。 客户端数分为小（5 个或更少）、中等（5 个以上但小于或等于 20 个）和大（超过 20 个）。

### <a name="high-throughput-queue"></a>高吞吐量队列

目标：将单个队列的吞吐量最大化。 发送方和接收方的数目较小。

* 如要增加面向队列的总发送速率，则使用多个消息工厂来创建发送方。 为每个发送方使用异步操作或多个线程。
* 如要增加从队列接收的总体接收速率，则使用多个消息工厂来创建接收方。
* 使用异步操作可利用客户端批处理。
* 将批处理间隔时间设置为 50 毫秒以减少服务总线客户端协议传输的数量。 如果使用多个发送方，则将批处理间隔时间增加到 100 毫秒。
* 将批量存储访问保留为启用状态。 该访问会增加可将消息写入队列的总速率。
* 将预提取计数设置为工厂所有接收方最大处理速率的 20 倍。 此计数会减少服务总线客户端协议传输的数量。
* 使用分区的队列提高性能和可用性。

### <a name="multiple-high-throughput-queues"></a>多个高吞吐量队列

目标：将多个队列的整体吞吐量最大化。 单个队列的吞吐量中等或高。

要在多个队列之间获得最大的吞吐量，则使用所述设置将单个队列的吞吐量最大化。 此外，使用不同工厂来创建向不同的队列发送或从其接收的客户端。

### <a name="low-latency-queue"></a>低延迟队列

目标：将队列或主题的端到端延迟时间最小化。 发送方和接收方的数目较小。 队列的吞吐量较小或为中等。

* 禁用客户端批处理。 客户端会立即发送一条消息。
* 禁用批量存储访问。 该服务会立即将消息写入存储。
* 如果使用单个客户端，将预提取计数设置为接收方处理速率的 20 倍。 如果多条消息同时到达队列，服务总线客户端协议会将这些消息全部同时传输。 当客户端收到下一条消息时，该消息便已存在于本地缓存中。 缓存应较小。
* 如果使用多个客户端，则将预提取计数设置为 0。 通过设置此计数，在第一个客户端仍在处理第一条消息时，第二个客户端可以接收第二条消息。
* 使用分区的队列提高性能和可用性。

### <a name="queue-with-a-large-number-of-senders"></a>包含大量发送方的队列

目标：将包含大量发送方的队列或主题的吞吐量最大化。 每个发送方均以中等速率发送消息。 接收方的数目较小。

服务总线允许最多 1000 个与消息传递实体之间的并发连接（使用 AMQP 则为 5000 个）。 在命名空间级别强制实施此限制，并且队列/主题/订阅受每个命名空间的并发连接限制约束。 就队列而言，此数值在发送方和接收方之间共享。 如果发件人需要所有 1000 个连接，则将队列替换为主题和单个订阅。 主题接受来自发件人的最多 1000 个并发连接，而订阅则可接受来自接收方的其他 1000 个并发连接。 如果需要超过 1000 个并发发送方，则发送方应通过 HTTP 向服务总线协议发送消息。

若要使吞吐量最大化，则执行以下步骤：

* 如果每个发送方驻留在不同进程中，则每个进程仅使用单个工厂。
* 使用异步操作可利用客户端批处理。
* 使用 20 毫秒的默认批处理间隔时间以减少服务总线客户端协议传输的数量。
* 将批量存储访问保留为启用状态。 此访问会增加可将消息写入队列或主题的总速率。
* 将预提取计数设置为工厂所有接收方最大处理速率的 20 倍。 此计数会减少服务总线客户端协议传输的数量。
* 使用分区的队列提高性能和可用性。

### <a name="queue-with-a-large-number-of-receivers"></a>包含大量接收方的队列

目标：将包含大量接收方的队列或订阅的接收速率最大化。 每个接收方以中等接收速率接收消息。 发送方的数目较小。

服务总线允许最多 1000 个与实体之间的并发连接。 如果队列需要超过 1000 个接收方，则将队列替换为主题和多个订阅。 每个订阅可支持最多 1000 个并发连接。 或者，接收方可通过 HTTP 协议访问队列。

若要使吞吐量最大化，则执行以下操作：

* 如果每个接收方驻留在不同进程中，则每个进程仅使用单个工厂。
* 接收方可使用同步或异步操作。 如果独立接收方的接收速率给定为中等级别，则客户端对“完成”请求的批处理不会影响接收方的吞吐量。
* 将批量存储访问保留为启用状态。 此访问会减少实体的总负载。 这还将降低可将消息写入队列或主题的总速率。
* 将预提取计数设置为较小值（例如，PrefetchCount = 10）。 此计数可防止接收方在其他接收方已缓存大量消息时处于闲置状态。
* 使用分区的队列提高性能和可用性。

### <a name="topic-with-a-small-number-of-subscriptions"></a>包含少量订阅的主题

目标：将包含少量订阅的主题的吞吐量最大化。 消息由多个订阅接收，这意味着对所有订阅的组合接收速率比发送速率要大得多。 发送方的数目较小。 每个订阅的接收方的数目较小。

若要使吞吐量最大化，则执行以下操作：

* 如要增加面向主题的总发送速率，则使用多个消息工厂来创建发送方。 为每个发送方使用异步操作或多个线程。
* 如要增加从订阅接收的总体接收速率，则使用多个消息工厂来创建接收方。 为每个接收方使用异步操作或多个线程。
* 使用异步操作可利用客户端批处理。
* 使用 20 毫秒的默认批处理间隔时间以减少服务总线客户端协议传输的数量。
* 将批量存储访问保留为启用状态。 此访问会增加可将消息写入主题的总写入速率。
* 将预提取计数设置为工厂所有接收方最大处理速率的 20 倍。 此计数会减少服务总线客户端协议传输的数量。
* 使用分区的主题提高性能和可用性。

### <a name="topic-with-a-large-number-of-subscriptions"></a>包含大量订阅的主题

目标：将包含大量订阅的主题的吞吐量最大化。 消息由多个订阅接收，这意味着对所有订阅的组合接收速率比发送速率要大得多。 发送方的数目较小。 每个订阅的接收方的数目较小。

如果所有消息都路由到所有订阅，具有大量订阅的主题则通常会公开低的总吞吐量。 低吞吐量的原因是，每个消息被多次接收，并且一个主题中包含的所有消息以及所有订阅都存储在同一存储。 假定每个订阅的发送方数量和接收方数量很小。 服务总线支持每个主题最多 2,000 个订阅。

若要使吞吐量最大化，则尝试执行以下步骤：

* 使用异步操作可利用客户端批处理。
* 使用 20 毫秒的默认批处理间隔时间以减少服务总线客户端协议传输的数量。
* 将批量存储访问保留为启用状态。 此访问会增加可将消息写入主题的总写入速率。
* 将预提取计数设置为预期接收速率的 20 倍（以秒为单位）。 此计数会减少服务总线客户端协议传输的数量。
* 使用分区的主题提高性能和可用性。

## <a name="next-steps"></a>后续步骤

若要了解有关优化服务总线性能的详细信息，请参阅[分区的消息实体][Partitioned messaging entities]。

[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[PeekLock]: /dotnet/api/microsoft.azure.servicebus.receivemode
[ReceiveAndDelete]: /dotnet/api/microsoft.azure.servicebus.receivemode
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[EnableBatchedOperations]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations
[QueueClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount
[SubscriptionClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[Partitioned messaging entities]: service-bus-partitioning.md
[TopicDescription.EnableFilteringMessagesBeforePublishing]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
