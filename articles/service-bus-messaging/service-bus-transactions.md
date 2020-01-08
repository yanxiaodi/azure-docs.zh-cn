---
title: Azure 服务总线中事务处理概述 | Microsoft Docs
description: Azure 服务总线原子事务和发送方式概述
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2018
ms.author: aschhab
ms.openlocfilehash: a839a4cad824a74bde388317cf3aaddf9c5bd47f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332340"
---
# <a name="overview-of-service-bus-transaction-processing"></a>服务总线事务处理概述

本文将讨论 Microsoft Azure 服务总线的事务功能。 很多讨论进行了说明[AMQP 事务与服务总线示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia)。 本文仅限于概述服务总线中的事务处理和发送方式  功能，虽然原子事务示例在作用域内更广泛且更复杂。

## <a name="transactions-in-service-bus"></a>服务总线中的事务

一个*事务*将两个或更多操作组合成执行范围  。 就本质而言，此类事务必须确保所有操作属于给定的操作组，无论联合成功还是失败。 在这方面，事务作为一个单元进行操作，通常称为原子性  。

服务总线是事务性消息代理，并确保针对其消息存储的所有内部操作的事务完整性。 服务总线内部的所有消息传输，如将消息移到[死信队列](service-bus-dead-letter-queues.md)或在实体之间[自动转发](service-bus-auto-forwarding.md)消息，都是事务性的。 因此，如果服务总线接受一条消息，则该消息已存储并标有一个序列号。 从那时起，服务总线内的任何消息传输都是实体之间协调的操作，将从不会导致消息丢失（源成功而目标失败）或重复（源失败而目标成功）。

服务总线支持对事务作用域内的消息传送实体（队列、主题、订阅）执行分组操作。 例如，可以从事务范围内将多条消息发送到一个队列，在事务成功完成时，这些消息将仅提交到该队列的日志。

## <a name="operations-within-a-transaction-scope"></a>事务作用域内的操作

可以在事务作用域内执行的操作如下所示：

* **[QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient)、[MessageSender](/dotnet/api/microsoft.azure.servicebus.core.messagesender)、[TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)** ：Send、SendAsync、SendBatch、SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)** ：Complete、CompleteAsync、Abandon、AbandonAsync、Deadletter、DeadletterAsync、Defer、DeferAsync、RenewLock、RenewLockAsync 

不包括接收操作，因为假定应用程序在某个接收循环内使用 [ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) 模式或通过 [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) 回调获取消息，而且只有那时才打开用于处理消息的事务作用域。

然后，消息的处置（完成、放弃、死信、延迟）将在事务范围内进行，并依赖于在事务处理的整体结果。

## <a name="transfers-and-send-via"></a>传输和“发送方式”

要启用将数据从队列到处理器，然后到另一个队列的事务性移交，服务总线支持传输  。 在传输操作中，发送程序先将消息发送到“传输队列”  ，然后传输队列立即使用自动转发功能依赖的同一可靠传输实现代码，将消息移到预期的目标队列。 消息永远不会以对传输队列的使用者可见的方式提交到传输队列的日志中。

当传输队列本身是发送方的输入消息的源时，此事务功能的优势越明显。 换而言之，服务总线在对输入消息执行完成（或延迟或死信）操作时，可以“通过”传输队列将消息传输到目标队列中，所有这一切都通过一个原子操作完成。 

### <a name="see-it-in-code"></a>在代码中查看它

若要设置此类传输，需创建通过传输队列以目标队列为目标的消息发送方。 还将设置接收方，以便从该同一队列拉取消息。 例如：

```csharp
var connection = new ServiceBusConnection(connectionString);

var sender = new MessageSender(connection, QueueName);
var receiver = new MessageReceiver(connection, QueueName);
```

简单事务处理随后使用这些元素，如以下示例所示。 若要引用的完整示例，请参阅[源代码在 GitHub 上](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TransactionsAndSendVia/TransactionsAndSendVia/AMQPTransactionsSendVia):

```csharp
var receivedMessage = await receiver.ReceiveAsync();

using (var ts = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
{
    try
    {
        // do some processing
        if (receivedMessage != null)
            await receiver.CompleteAsync(receivedMessage.SystemProperties.LockToken);

        var myMsgBody = new MyMessage
        {
            Name = "Some name",
            Address = "Some street address",
            ZipCode = "Some zip code"
        };

        // send message
        var message = myMsgBody.AsMessage();
        await sender.SendAsync(message).ConfigureAwait(false);
        Console.WriteLine("Message has been sent");

        // complete the transaction
        ts.Complete();
    }
    catch (Exception ex)
    {
        // This rolls back send and complete in case an exception happens
        ts.Dispose();
        Console.WriteLine(ex.ToString());
    }
}
```

## <a name="next-steps"></a>后续步骤

有关服务总线队列的详细信息，请参阅以下文章：

* [如何使用 Service Bus 队列](service-bus-dotnet-get-started-with-queues.md)
* [使用自动转发链接服务总线实体](service-bus-auto-forwarding.md)
* [自动转发示例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Atomic Transactions with Service Bus sample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)（服务总线中的原子事务示例）
* [Azure 队列和服务总线队列比较](service-bus-azure-and-service-bus-queues-compared-contrasted.md)


