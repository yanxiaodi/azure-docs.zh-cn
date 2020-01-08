---
title: 快速入门 - 使用 Azure PowerShell 创建服务总线队列 | Microsoft Docs
description: 在本快速入门中，你将了解如何使用 Azure PowerShell 创建服务总线队列。 然后，你将使用示例应用程序向队列发送消息以及从中接收消息。
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: 80cef3efd65a3f68dba1856a892a9a120f7d95cd
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990286"
---
# <a name="quickstart-use-azure-powershell-to-create-a-service-bus-queue"></a>快速入门：使用 Azure PowerShell 创建服务总线队列
本快速入门介绍如何使用 PowerShell 创建消息命名空间并在该命名空间中创建队列，以及如何获取该命名空间上的授权凭据，以便将消息发送到服务总线队列及从中接收消息。 然后该过程展示了如何使用 [.NET Standard 库](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus)从此队列发送和接收消息。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保已安装：

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][]。 
- [Visual Studio 2017 Update 3（版本 15.3 (26730.01)）](https://www.visualstudio.com/vs)或更高版本。 请使用 Visual Studio 生成一个示例，以便向队列发送消息以及从队列接收消息。 示例用于测试在门户中创建的队列。 
- [NET Core SDK](https://www.microsoft.com/net/download/windows) 2.0 或更高版本。

本快速入门要求运行最新版本的 Azure PowerShell。 如需进行安装或升级，请参阅[安装和配置 Azure PowerShell][]。 如果熟悉 Azure Cloud Shell，则可使用它，不需在计算机上安装 Azure PowerShell。 有关 Azure Cloud Shell 的详细信息，请参阅 [Azure Cloud Shell 概述](../cloud-shell/overview.md)

## <a name="sign-in-to-azure"></a>登录 Azure

1. 首先，如果尚未安装服务总线 PowerShell 模块，请安装：

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. 运行以下命令来登录到 Azure：

   ```azurepowershell-interactive
   Login-AzAccount
   ```

3. 发出以下命令来设置当前的订阅上下文，或者查看当前活动的订阅：

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>预配资源

在 PowerShell 提示符下，发出以下命令以预配服务总线资源。 请务必将所有占位符替换为适当的值：

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

`Get-AzServiceBusKey` cmdlet 运行后，将所选的连接字符串和队列名称复制并粘贴到记事本等临时位置。 在下一步中将要使用它。

## <a name="send-and-receive-messages"></a>发送和接收消息

创建命名空间和队列并且拥有所需的凭据后，便可以发送和接收消息。 可以观察[此 GitHub 示例文件夹](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart)中的代码。

若要运行此代码，请执行以下操作：

1. 通过发出以下命令克隆[服务总线 GitHub 存储库](https://github.com/Azure/azure-service-bus/)：

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. 导航到示例文件夹 `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`。

4. 如果尚未这样做，请使用以下 PowerShell cmdlet 获取连接字符串。 请务必将 `my-resourcegroup`  和  `namespace-name` 替换为具体值： 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5. 在 PowerShell 提示符下，键入以下命令：

   ```shell
   dotnet build
   ```

6. 导航到 `bin\Debug\netcoreapp2.0` 文件夹。

7. 键入以下命令以运行程序。 请务必将 `myConnectionString` 替换为先前获取的值，将 `myQueueName` 替换为所创建队列的名称：

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. 观察发送到队列并随后从队列中接收的 10 条消息：

   ![程序输出](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>清理资源

运行以下命令来删除资源组、命名空间和所有相关资源：

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>了解示例代码

此部分包含有关示例代码功能的更多详细信息。 

### <a name="get-connection-string-and-queue"></a>获取连接字符串和队列

连接字符串和队列名称作为命令行参数传递给 `Main()` 方法。 `Main()` 声明了两个字符串变量来保存这些值：

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
然后，`Main()` 方法启动异步消息循环 `MainAsync()`。

### <a name="message-loop"></a>消息循环

MainAsync() 方法使用命令行参数创建队列客户端、调用名为 `RegisterOnMessageHandlerAndReceiveMessages()` 的接收消息处理程序，并发送消息集：

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

`RegisterOnMessageHandlerAndReceiveMessages()` 方法只是设置一些消息处理程序选项，然后调用队列客户端的 `RegisterMessageHandler()` 方法，该方法开始接收消息：

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>发送消息

消息创建和发送操作发生在 `SendMessagesAsync()` 方法中：

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>处理消息

`ProcessMessagesAsync()` 方法确认、处理并完成消息的接收：

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

> [!NOTE]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 服务总线资源管理器允许用户连接到服务总线命名空间并以一种简单的方式管理消息传送实体。 该工具提供高级功能，如导入/导出功能或用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。 

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建一个服务总线命名空间并从队列发送和接收消息所需的其他资源。 若要详细了解如何编写收发消息的代码，请继续阅读教程的“发送和接收消息”部分  。 

> [!div class="nextstepaction"]
> [发送和接收消息](service-bus-dotnet-get-started-with-queues.md)

[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[安装和配置 Azure PowerShell]: /powershell/azure/install-Az-ps
