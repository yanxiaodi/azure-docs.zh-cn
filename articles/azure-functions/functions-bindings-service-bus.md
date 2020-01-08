---
title: Azure Functions 的 Azure 服务总线绑定
description: 了解如何在 Azure Functions 中使用 Azure 服务总线触发器和绑定。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions，函数，事件处理，动态计算，无服务体系结构
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 7dcc69434e017d6564030d83b14098344bc8ac0d
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178340"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Functions 的 Azure 服务总线绑定

本文介绍如何在 Azure Functions 中使用 Azure 服务总线绑定。 Azure Functions 支持对服务总线队列和主题的触发器和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>包 - Functions 1.x

[Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet 包 2.x 版中提供了服务总线绑定。 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>包 - Functions 2.x

[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) NuGet 包 3.x 版中提供了服务总线绑定。 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/) GitHub 存储库中提供了此包的源代码。

> [!NOTE]
> 版本2.x 不会创建`ServiceBusTrigger`实例中配置的主题或订阅。 版本2.x 基于[Azure](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) ，并且不处理队列管理。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>触发器

使用服务总线触发器响应来自服务总线队列或主题的消息。 

## <a name="trigger---example"></a>触发器 - 示例

参阅语言特定的示例：

* [C#](#trigger---c-example)
* [C# 脚本 (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>触发器 - C# 示例

以下示例演示了读取[消息元数据](#trigger---message-metadata)并记录服务总线队列消息的 [C# 函数](functions-dotnet-class-library.md)：

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

此示例适用于 Azure Functions 版本 1.x。 要使此代码适用于 2.x：

- [省略访问权限参数](#trigger---configuration)
- 将日志参数的类型从 `TraceWriter` 更改为 `ILogger`
- 将 `log.Info` 更改为 `log.LogInformation`

### <a name="trigger---c-script-example"></a>触发器 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个服务总线触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 此函数将读取[消息元数据](#trigger---message-metadata)并记录服务总线队列消息。

下面是 function.json 文件中的绑定数据：

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

C# 脚本代码如下所示：

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>触发器 - F# 示例

以下示例演示 *function.json* 文件中的一个服务总线触发器绑定以及使用该绑定的 [F# 函数](functions-reference-fsharp.md)。 该函数记录服务总线队列消息。 

下面是 function.json 文件中的绑定数据：

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

F# 脚本代码如下所示：

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>触发器 - Java 示例

以下 Java 函数使用 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中的 `@ServiceBusQueueTrigger` 注释来说明服务总线队列触发器的配置。 此函数获取放置在队列上的消息，然后将其添加到日志。

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

将消息添加到服务总线主题时，也可触发 Java 函数。 以下示例使用 `@ServiceBusTopicTrigger` 注释来说明触发器配置。

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>触发器 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个服务总线触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 此函数将读取[消息元数据](#trigger---message-metadata)并记录服务总线队列消息。 

下面是 function.json 文件中的绑定数据：

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

JavaScript 脚本代码如下所示：

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

### <a name="trigger---python-example"></a>触发器 - Python 示例

下面的示例演示如何通过触发器读取 ServiceBus 队列消息。

ServiceBus 绑定在 function.json 中定义，其中 type 设置为 `serviceBusTrigger`。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

_\_init_\_.py 中的代码将参数声明为 `func.ServiceBusMessage`，以允许你在函数中读取队列消息。

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>触发器 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，请使用以下属性来配置服务总线触发器：

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  该特性的构造函数采用队列或者主题和订阅的名称。 在 Azure Functions 版本 1.x 中，还可以指定连接的访问权限。 如果未指定访问权限，则默认为 `Manage`。 有关详细信息，请参阅[触发器 - 配置](#trigger---configuration)部分。

  下面是一个示例，演示了用于字符串参数的属性：

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  可以设置 `Connection` 属性来指定要使用的服务总线帐户，如以下示例中所示：

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  有关完整示例，请参阅[触发器 - C# 示例](#trigger---c-example)。

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  提供另一种方式来指定要使用的服务总线帐户。 构造函数采用包含服务总线连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

要使用的服务总线帐户按以下顺序确定：

* `ServiceBusTrigger` 特性的 `Connection` 属性。
* 作为 `ServiceBusTrigger` 特性应用到同一参数的 `ServiceBusAccount` 特性。
* 应用到函数的 `ServiceBusAccount` 特性。
* 应用到类的 `ServiceBusAccount` 特性。
* “AzureWebJobsServiceBus”应用设置。

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 function.json 文件和 `ServiceBusTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**type** | 不适用 | 必须设置为“serviceBusTrigger”。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为“in”。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 变量的名称，表示函数代码中的队列或主题消息。 设置为“$return”可引用函数返回值。 |
|**queueName**|**QueueName**|要监视的队列的名称。  仅在监视队列的情况下设置，不为主题设置。
|**topicName**|**TopicName**|要监视的主题的名称。 仅在监视主题的情况下设置，不为队列设置。|
|**subscriptionName**|**SubscriptionName**|要监视的订阅的名称。 仅在监视主题的情况下设置，不为队列设置。|
|**连接**|**Connection**|应用设置的名称，包含要用于此绑定的服务总线连接字符串。 如果应用设置名称以“AzureWebJobs”开头，则只能指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyServiceBus”，函数运行时将会查找名为“AzureWebJobsMyServiceBus”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为“AzureWebJobsServiceBus”的应用设置中的默认服务总线连接字符串。<br><br>若要获取连接字符串，请执行[获取管理凭据](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)中显示的步骤。 必须是服务总线命名空间的连接字符串，不限于特定的队列或主题。 |
|**accessRights**|**Access**|连接字符串的访问权限。 可用值为 `manage` 和 `listen`。 默认值是 `manage`，其指示 `connection` 具有“管理”权限。 如果使用不具有“管理”权限的连接字符串，请将 `accessRights` 设置为“listen”。 否则，Functions 运行时可能会在尝试执行需要管理权限的操作时失败。 在 Azure Functions 版本 2.x 中，此属性不可用，因为存储 SDK 不支持管理操作。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>触发器 - 用法

在 C# 和 C# 脚本中，可以为队列或主题消息使用以下参数类型：

* `string` - 如果消息是文本。
* `byte[]` - 适用于二进制数据。
* 自定义类型 - 如果消息包含 JSON，Azure Functions 会尝试反序列化 JSON 数据。
* `BrokeredMessage` - 使用 [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) 方法提供反序列化消息。

这些参数仅适用于 Azure Functions 版本 1.x；对于 2.x，请使用 [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) 而非 `BrokeredMessage`。

在 JavaScript 中通过 `context.bindings.<name from function.json>` 访问队列或主题消息。 服务总线消息作为字符串或 JSON 对象传递到函数中。

## <a name="trigger---poison-messages"></a>触发器 - 有害消息

无法在 Azure Functions 中控制或配置有害消息处理。 服务总线处理有害消息本身。

## <a name="trigger---peeklock-behavior"></a>触发器 - PeekLock 行为

Functions 运行时以 [PeekLock 模式](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)接收消息。 如果函数成功完成，则对此消息调用 `Complete`；如果函数失败，则调用 `Abandon`。 如果函数的运行时间长于 `PeekLock` 超时时间，则只要该函数正在运行，就会自动续订锁定。 

`maxAutoRenewDuration` 可在 *host.json* 中配置，它映射到 [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet)。 根据服务总线文档，此设置所允许的最长时间为 5 分钟，而你可以将 Functions 时间限制从默认值 5 分钟增加到 10 分钟。 对于服务总线函数，你不会那么做，因为会超出服务总线续订限制。

## <a name="trigger---message-metadata"></a>触发器 - 消息元数据

服务总线触发器提供了几个[元数据属性](./functions-bindings-expressions-patterns.md#trigger-metadata)。 这些属性可在其他绑定中用作绑定表达式的一部分，或者用作代码中的参数。 以下是 [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) 类的属性。

|属性|类型|描述|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|传递次数。|
|`DeadLetterSource`|`string`|死信源。|
|`ExpiresAtUtc`|`DateTime`|到期时间 (UTC)。|
|`EnqueuedTimeUtc`|`DateTime`|排队时间 (UTC)。|
|`MessageId`|`string`|服务总线可用于标识重复消息的用户定义值（如果启用）。|
|`ContentType`|`string`|发送方和接收方用于实现应用程序特定逻辑的内容类型标识符。|
|`ReplyTo`|`string`|对队列地址的回复。|
|`SequenceNumber`|`Int64`|服务总线分配给消息的唯一编号。|
|`To`|`string`|发送到地址。|
|`Label`|`string`|应用程序特定标签。|
|`CorrelationId`|`string`|相关 ID。|

> [!NOTE]
> 目前，与启用了会话的队列和订阅一起工作的服务总线触发器处于预览状态。 有关此触发器的任何进一步更新，请跟踪[此项](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458)。 

请参阅在本文的前面部分使用这些属性的[代码示例](#trigger---example)。

## <a name="trigger---hostjson-properties"></a>触发器 - host.json 属性

[host.json](functions-host-json.md#servicebus) 文件包含控制服务总线触发器行为的设置。

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "maxAutoRenewDuration": "00:05:00"
    }
}
```

|属性  |默认 | 描述 |
|---------|---------|---------|
|maxConcurrentCalls|16|消息泵应该对回调发起的最大并发调用数。 默认情况下，Functions 运行时同时处理多条消息。 若要指示运行时一次只处理单个队列或主题消息，请将 `maxConcurrentCalls` 设置为 1。 |
|prefetchCount|不适用|基础 MessageReceiver 将要使用的默认 PrefetchCount。|
|maxAutoRenewDuration|00:05:00|自动续订消息锁的最长持续时间。|

## <a name="output"></a>Output

使用 Azure 服务总线输出绑定发送队列或主题消息。

## <a name="output---example"></a>输出 - 示例

参阅语言特定的示例：

* [C#](#output---c-example)
* [C# 脚本 (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>输出 - C# 示例

以下示例演示发送服务总线队列消息的 [C# 函数](functions-dotnet-class-library.md)：

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>输出 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个服务总线输出绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数使用计时器触发器，每隔 15 秒发送一条队列消息。

下面是 function.json 文件中的绑定数据：

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

下面是可创建一条消息的 C# 脚本代码：

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

下面是可创建多条消息的 C# 脚本代码：

```cs
public static void Run(TimerInfo myTimer, ILogger log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>输出 - F# 示例

以下示例演示 *function.json* 文件中的一个服务总线输出绑定以及使用该绑定的 [F# 脚本函数](functions-reference-fsharp.md)。 该函数使用计时器触发器，每隔 15 秒发送一条队列消息。

下面是 function.json 文件中的绑定数据：

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

下面是可创建一条消息的 F# 脚本代码：

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>输出 - Java 示例

以下示例演示了一个 Java 函数，该函数在由 HTTP 请求触发时将消息发送到服务总线队列 `myqueue`。

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将写入服务总线队列的函数参数使用 `@QueueOutput` 注释。  参数类型应为 `OutputBinding<T>`，其中 T 是 POJO 的任何本机 Java 类型。

Java 函数也可将内容写入服务总线主题。 以下示例使用 `@ServiceBusTopicOutput` 注释来说明输出绑定的配置。 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

### <a name="output---javascript-example"></a>输出 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个服务总线输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数使用计时器触发器，每隔 15 秒发送一条队列消息。

下面是 function.json 文件中的绑定数据：

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

下面是可创建一条消息的 JavaScript 脚本代码：

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

下面是可创建多条消息的 JavaScript 脚本代码：

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>输出 - Python 示例

下面的示例演示如何使用 Python 写出到 ServiceBus 队列。

ServiceBue 绑定在 function.json 中定义，其中 type 设置为 `serviceBus`。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

在 _\_init_\_.py 中，可以通过将值传递给 `set` 方法将消息写出到队列。

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

## <a name="output---attributes"></a>输出 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)。

该特性的构造函数采用队列或者主题和订阅的名称。 也可指定连接的访问权限。 [输出 - 配置](#output---configuration)部分介绍了如何选择访问权限设置。 下面是一个示例，说明了应用于该函数的返回值的属性：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

可以设置 `Connection` 属性来指定要使用的服务总线帐户，如以下示例中所示：

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

有关完整示例，请参阅[输出 - C# 示例](#output---c-example)。

可以使用 `ServiceBusAccount` 特性在类、方法或参数级别指定要使用的服务总线帐户。  有关详细信息，请参阅[触发器 - 特性](#trigger---attributes)。

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 function.json 文件和 `ServiceBus` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**type** | 不适用 | 必须设置为“serviceBus”。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为“out”。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 不适用 | 变量的名称，表示函数代码中的队列或主题。 设置为“$return”可引用函数返回值。 |
|**queueName**|**QueueName**|队列名称。  仅在发送队列消息的情况下设置，不为主题设置。
|**topicName**|**TopicName**|要监视的主题的名称。 仅在发送主题消息的情况下设置，不为队列设置。|
|**连接**|**Connection**|应用设置的名称，包含要用于此绑定的服务总线连接字符串。 如果应用设置名称以“AzureWebJobs”开头，则只能指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyServiceBus”，函数运行时将会查找名为“AzureWebJobsMyServiceBus”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为“AzureWebJobsServiceBus”的应用设置中的默认服务总线连接字符串。<br><br>若要获取连接字符串，请执行[获取管理凭据](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)中显示的步骤。 必须是服务总线命名空间的连接字符串，不限于特定的队列或主题。|
|**accessRights**|**Access**|连接字符串的访问权限。 可用值为 `manage` 和 `listen`。 默认值是 `manage`，其指示 `connection` 具有“管理”权限。 如果使用不具有“管理”权限的连接字符串，请将 `accessRights` 设置为“listen”。 否则，Functions 运行时可能会在尝试执行需要管理权限的操作时失败。 在 Azure Functions 版本 2.x 中，此属性不可用，因为存储 SDK 不支持管理操作。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>输出 - 用法

在 Azure Functions 1.x 中，如果队列尚不存在并且 `accessRights` 设置为 `manage`，则运行时会创建队列。 在 Functions 版本 2.x 中，队列或主题必须已存在，如果指定了不存在的队列或主题，则函数将失败。 

在 C# 和 C# 脚本中，可以为输出绑定使用以下参数类型：

* `out T paramName` - `T` 可以是任何可 JSON 序列化的类型。 如果函数退出时参数值为 null，Functions 将创建具有 null 对象的消息。
* `out string` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `out byte[]` - 如果函数退出时参数值为 null，Functions 不创建消息。
* `out BrokeredMessage`-如果函数退出时参数值为 null，则函数不会创建消息（对于函数1.x）
* `out Message`-如果函数退出时参数值为 null，则函数不会创建消息（对于函数1.x）
* `ICollector<T>` 或 `IAsyncCollector<T>` - 用于创建多条消息。 调用 `Add` 方法时创建了一条消息。

使用C#函数时：

* 异步函数需要返回值`IAsyncCollector` ，而不`out`是参数。

* 若要访问会话 ID，请绑定到[`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message)类型并`sessionId`使用属性。

在 JavaScript 中通过 `context.bindings.<name from function.json>` 访问队列或主题。 可以将字符串、字节数组或 JavaScript 对象（反序列化为 JSON）分配给`context.binding.<name>`。

若要将消息以非C#语言发送到已启用会话的队列，请使用[AZURE 服务总线 SDK](https://docs.microsoft.com/azure/service-bus-messaging) ，而不是内置的输出绑定。

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 参考 |
|---|---|
| 服务总线 | [服务总线错误代码](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| 服务总线 | [服务总线限制](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 设置

本部分介绍版本 2.x 中可用于此绑定的全局配置设置。 下面的示例 host.json 文件仅包含此绑定的 2.x 版本设置。 有关版本 2.x 中的全局配置设置的详细信息，请参阅 [Azure Functions 版本 2.x 的 host.json 参考](functions-host-json.md)。

> [!NOTE]
> 有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            }
        }
    }
}
```

|属性  |默认 | 描述 |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|自动续订消息锁的最长持续时间。|
|autoComplete|真|触发器应立即标记为已完成（自动完成），还是等待调用完成的处理。|
|maxConcurrentCalls|16|消息泵应该对回调发起的最大并发调用数。 默认情况下，Functions 运行时同时处理多条消息。 若要指示运行时一次只处理单个队列或主题消息，请将 `maxConcurrentCalls` 设置为 1。 |
|prefetchCount|不适用|基础 MessageReceiver 将要使用的默认 PrefetchCount。|


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
