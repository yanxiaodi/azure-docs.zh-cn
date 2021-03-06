---
title: 开始使用队列存储和 Visual Studio 连接服务（云服务）| Microsoft Docs
description: 在使用 Visual Studio 连接服务连接到存储帐户后，如何开始在 Visual Studio 的云服务项目中使用 Azure 队列存储
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 5ea0af23ef8cf41b20de033d38e4d8652f9f8310
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510695"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>开始使用 Azure 队列存储和 Visual Studio 连接服务（云服务项目）
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概述
本文介绍通过使用 Visual Studio 中的“添加连接服务”对话框在云服务项目中创建或引用 Azure 存储帐户之后，如何开始在 Visual Studio 中使用 Azure 队列存储。

我们将向你展示如何使用代码创建队列。 此外，我们将展示如何执行基本的队列操作，例如添加、修改、读取和删除队列消息。 示例是使用 C# 代码编写的并使用了[适用于 .NET 的 Microsoft Azure 存储客户端库](https://msdn.microsoft.com/library/azure/dn261237.aspx)。

执行“添加连接服务”操作会安装相应的 NuGet 程序包，以访问项目中的 Azure 存储，并将存储帐户的连接字符串添加到项目配置文件中。

* 有关以代码方式操作队列的详细信息，请参阅[通过 .NET 开始使用 Azure 队列存储](../storage/queues/storage-dotnet-how-to-use-queues.md)。
* 有关 Azure 存储的常规信息，请参阅[存储文档](https://azure.microsoft.com/documentation/services/storage/)。
* 有关 Azure 云服务的常规信息，请参阅[云服务文档](https://azure.microsoft.com/documentation/services/cloud-services/)。
* 有关编写 ASP.NET 应用程序的详细信息，请参阅 [ASP.NET](https://www.asp.net)。

Azure 队列存储是一项可存储大量消息的服务，用户可以通过经验证的呼叫，使用 HTTP 或 HTTPS 从世界任何地方访问这些消息。 一条队列消息的大小最多可为 64 KB，一个队列中可以包含数百万条消息，直至达到存储帐户的总容量限值。

## <a name="access-queues-in-code"></a>使用代码访问队列
若要在 Visual Studio 云服务项目中访问队列，需要在可访问 Azure 队列存储的任何 C# 源文件中包含以下项。

1. 请确保 C# 文件顶部的命名空间声明包括以下 **using** 语句。
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;
2. 获取表示存储帐户信息的 **CloudStorageAccount** 对象。 使用下面的代码从 Azure 服务配置获取存储连接字符串和存储帐户信息。
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
3. 获取 **CloudQueueClient** 对象，以引用存储帐户中的队列对象。  
   
        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
4. 获取 **CloudQueue** 对象，以引用特定队列。
   
        // Get a reference to a queue named "messageQueue"
        CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");

**注意：** 在下列示例中，在代码的前面使用上述全部代码。

## <a name="create-a-queue-in-code"></a>使用代码创建队列
若要在代码中创建队列，只需添加对 **CreateIfNotExists** 的调用。

    // Create the CloudQueue if it does not exist
    messageQueue.CreateIfNotExists();

## <a name="add-a-message-to-a-queue"></a>向队列添加消息
要在现有队列中插入消息，请创建新的 **CloudQueueMessage** 对象，并调用 **AddMessage** 方法。

可从字符串（UTF-8 格式）或字节数组创建 **CloudQueueMessage** 对象。

以下示例将插入消息“Hello, World”。

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    messageQueue.AddMessage(message);

## <a name="read-a-message-in-a-queue"></a>读取队列中的消息
通过调用 **PeekMessage** 方法，可以查看队列前面的消息，而不必从队列中将其删除。

    // Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

## <a name="read-and-remove-a-message-in-a-queue"></a>读取和删除队列中的消息
代码分两步从队列中删除消息（取消对消息的排队）。

1. 调用 **GetMessage** 可获取队列中的下一条消息。 从 **GetMessage** 返回的消息变得对从此队列读取消息的任何其他代码不可见。 默认情况下，此消息将持续 30 秒不可见。
2. 若要完成从队列中删除消息，请调用 **DeleteMessage**。

此删除消息的两步过程可确保，如果代码因硬件或软件故障而无法处理消息，则代码的其他实例可以获取相同消息并重试。 以下代码在处理消息后立即调用 **DeleteMessage** 。

    // Get the next message in the queue.
    CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

    // Process the message in less than 30 seconds

    // Then delete the message.
    await messageQueue.DeleteMessage(retrievedMessage);


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>使用其他选项来处理和删除队列消息
可以通过两种方式自定义队列中的消息检索。

* 可以获取一批消息（最多 32 条）。
* 可以设置更长或更短的不可见超时时间，从而允许代码使用更多或更少的时间来完全处理每个消息。 以下代码示例使用 **GetMessages** 方法在一次调用中获取 20 条消息。 然后，它使用 **foreach** 循环处理每条消息。 它还将每条消息的不可见超时时间设置为 5 分钟。 请注意，5 分钟超时时间对于所有消息都是同时开始的，因此在调用 **GetMessages**5 分钟后，尚未删除的任何消息都将再次变得可见。

以下是一个示例：

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        // Then delete the message after processing
        messageQueue.DeleteMessage(message);

    }

## <a name="get-the-queue-length"></a>获取队列长度
可以获取队列中消息的估计数。 使用 **FetchAttributes** 方法可请求队列服务检索队列属性，包括消息计数。 **ApproximateMethodCount** 属性返回 **FetchAttributes** 方法检索到的最后一个值，而不会调用队列服务。

    // Fetch the queue attributes.
    messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>将 Async-Await 模式与公用 Azure 队列 API 配合使用
此示例演示如何将 Async-Await 模式与公用 Azure 队列 API 配合使用。 示例会调用每个给定方法的异步版本，这可以通过每个方法的 **Async** 后修补程序查看。 使用异步方法时，async-await 模式将暂停本地执行，直到调用完成。 此行为允许当前的线程执行其他工作，这有助于避免性能瓶颈并提高应用程序的整体响应能力。 有关在 .NET 中使用 Async-Await 模式的详细信息，请参阅 [Async 和 Await（C# 和 Visual Basic）](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## <a name="delete-a-queue"></a>删除队列
若要删除队列及其包含的所有消息，请对队列对象调用 **Delete** 方法。

    // Delete the queue.
    messageQueue.Delete();

## <a name="next-steps"></a>后续步骤
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]

