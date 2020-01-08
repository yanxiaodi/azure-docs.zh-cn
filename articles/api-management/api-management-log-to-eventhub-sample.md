---
title: 使用 Azure API 管理、事件中心和 Moesif 监视 API | Microsoft Docs
description: 通过连接 Azure API 管理、Azure 事件中心以及适用于 HTTP 日志记录与监视的 Moesif，演示 log-to-eventhub 策略的示例应用程序
services: api-management
documentationcenter: ''
author: darrelmiller
manager: erikre
editor: ''
ms.assetid: c528cf6f-5f16-4a06-beea-fa1207541a47
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2018
ms.author: apimpm
ms.openlocfilehash: c52a1942bda9881f8f782a227c81feaa4813722d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60656723"
---
# <a name="monitor-your-apis-with-azure-api-management-event-hubs-and-moesif"></a>使用 Azure API 管理、事件中心和 Moesif 监视 API
[API 管理服务](api-management-key-concepts.md)提供许多功能来增强发送到 HTTP API 的 HTTP 请求的处理。 但是，请求和响应都是暂时性存在的。 在请求发出后，将通过 API 管理服务流送到后端 API。 API 将处理该请求，然后，将响应返回给 API 使用者。 API 管理服务保留要在 Azure 门户仪表板中显示的有关 API 的一些重要统计信息，但除此之外不显示详细信息。

在 API 管理服务中使用 log-to-eventhub 策略，可将请求和响应的详细信息发送到 [Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)。 想要从发送到 API 的 HTTP 消息生成事件的原因包罗万象。 示例包括更新审核线索、使用情况分析、异常警报和第三方集成。

本文演示如何捕获整个 HTTP 请求和响应消息、将其发送到事件中心，然后将该消息中继到可提供 HTTP 日志记录和监视服务的第三方服务。

## <a name="why-send-from-api-management-service"></a>为何要从 API 管理服务发送？
可以编写能够插入到 HTTP API 框架中的 HTTP 中间件，以捕获 HTTP 请求和响应并将其馈送到日志记录和监视系统。 此方法的缺点是 HTTP 中间件必须集成到后端 API，并且必须与 API 的平台匹配。 如果有多个 API，每个 API 都必须部署中间件。 后端 API 无法升级往往是有原因的。

使用 Azure API 管理服务来与日志记录基础结构集成提供了一个集中的、平台独立的解决方案。 此外，还可以借助 Azure API 管理的[异地复制](api-management-howto-deploy-multi-region.md)功能进行缩放。

## <a name="why-send-to-an-azure-event-hub"></a>为何要发送到 Azure 事件中心？
一个合理的问题是，为何要创建特定于 Azure 事件中心的策略？ 在许多不同的场合下，我可能想要记录我的请求。 为何不能直接将请求发送到最终目标？  这只是一个选项。 但是，从 API 管理服务发出日志记录请求时，必须考虑日志记录消息对 API 性能造成的影响。 增加系统组件的可用实例或使用异地复制功能，可以处理逐渐增加的负载。 但是，如果日志记录基础结构在低负载状态下开始变慢，则短期的流量高峰可能导致请求延迟。

Azure 事件中心旨在引入大量数据，它能够处理的事件数目远高于大多数 API 所处理的 HTTP 请求数目。 在某种意义上，事件中心充当 API 管理服务与基础结构之间的高级缓冲区，可存储和处理消息。 这可以确保 API 性能不受日志记录基础结构的影响。

数据传递到事件中心后，会持久保存并等待事件中心使用者的处理。 事件中心不在乎其处理方式，只在乎如何确保成功传递消息。

事件中心可将事件流式传输到多个使用者组。 这样，事件便可由不同的系统处理。 此外，可为许多集成方案提供支持，而不会在 API 管理服务中处理 API 请求时造成额外的延迟，因为只需生成一个事件。

## <a name="a-policy-to-send-applicationhttp-messages"></a>用于发送 application/http 消息的策略
事件中心接受简单字符串形式的事件数据。 该字符串的内容由你确定。 要打包 HTTP 请求并将其发送到事件中心，需要使用请求或响应信息来格式化字符串。 在此类情况下，如果有可以重复使用的现有格式，则不需要编写自己的分析代码。 最初，我考虑使用 [HAR](http://www.softwareishard.com/blog/har-12-spec/) 来发送 HTTP 请求和响应。 但是，这种格式最适合用于存储 JSON 格式的一连串 HTTP 请求。 其中包含一些必需的元素，使得通过网络传递 HTTP 消息的方案增加了不必要的复杂性。

另一个做法是使用 HTTP 规范 [RFC 7230](https://tools.ietf.org/html/rfc7230) 中所述的 `application/http` 媒体类型。 此媒体类型使用的格式与用于通过网络实际发送 HTTP 消息的格式完全相同，但整个消息可以放在另一个 HTTP 请求的正文中。 在本例中，我们将使用该正文作为消息发送到事件中心。 [Microsoft ASP .NET Web API 2.2 客户端](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)库中有一个分析器可以分析此格式并将其转换为本机 `HttpRequestMessage` 和 `HttpResponseMessage` 对象，相当方便。

若要创建此消息，需要在 Azure API 管理中使用基于 C# 的[策略表达式](/azure/api-management/api-management-policy-expressions)。 下面是可将 HTTP 请求消息发送到 Azure 事件中心的策略。

```xml
<log-to-eventhub logger-id="conferencelogger" partition-id="0">
@{
   var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                               context.Request.Method,
                                               context.Request.Url.Path + context.Request.Url.QueryString);

   var body = context.Request.Body?.As<string>(true);
   if (body != null && body.Length > 1024)
   {
       body = body.Substring(0, 1024);
   }

   var headers = context.Request.Headers
                          .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                          .ToArray<string>();

   var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

   return "request:"   + context.Variables["message-id"] + "\n"
                       + requestLine + headerString + "\r\n" + body;
}
</log-to-eventhub>
```

### <a name="policy-declaration"></a>策略声明
此策略表达式有一些值得一提的特别之处。 log-to-eventhub 策略有一个名为 logger-id 的属性，该属性引用在 API 管理服务中创建的记录器名称。 [如何在 Azure API 管理中将事件记录到 Azure 事件中心](api-management-howto-log-event-hubs.md)文档中提供了有关如何在 API 管理服务中设置事件中心记录器的详细信息。 第二个属性是一个可选参数，指明事件中心要在哪个分区中存储消息。 事件中心使用分区实现可伸缩性，并且需要至少两个分区。 只保证一个分区内的消息依次传递。 如果未指明事件中心要在哪个分区中放置消息，它将使用循环算法来分配负载。 但是，这可能会导致一些消息不按顺序处理。

### <a name="partitions"></a>分区
为了确保消息依次传递给使用者并使用分区的负载分配功能，我已选择将 HTTP 请求消息发送到一个分区，将 HTTP 响应消息发送到另一个分区。 这可以确保负载平均分配，并且按顺序使用所有请求和所有响应。 响应有可能在相应请求之前使用，但这不成问题，因为我们有不同的机制能使请求与响应相互关联，并且我们知道请求始终出现在响应之前。

### <a name="http-payloads"></a>HTTP 有效负载
在构建 `requestLine` 之后，请查看是否应截断请求正文。 请求正文被截断成只有 1024 个字符。 可以增大此值，不过单个事件中心消息受限于 256 KB，因此有些 HTTP 消息正文可能无法放入单个消息。 执行日志记录和分析时，可以从 HTTP 请求行与标头派生大量信息。 此外，许多 API 请求只返回小型正文，因此相比于降低传输、处理和存储成本来保留所有正文内容，截断大型正文所造成的信息价值损失相当微小。 有关处理正文的最后一个注意事项是，需要将 `true` 传递给 `As<string>()` 方法，因为虽然我们可以读取正文内容，但也希望后端 API 能够读取正文。 将 true 传递给此方法后，正文会缓冲，以便进行第二次读取。 必须要注意 API 是否上传极大型文件或使用很长的轮询。 在这种情况下，最好完全避免读取正文。

### <a name="http-headers"></a>HTTP 标头
HTTP 标头可以转换为采用简单键/值对格式的消息格式。 我们已选择去除某些安全机密字段，以免不必要地泄漏凭据信息。 API 密钥和其他凭据不太可能用于分析。 如果想要分析用户及其使用的特定产品，可以从 `context` 对象获取这些信息，然后将其添加到消息。

### <a name="message-metadata"></a>消息元数据
创建要发送到事件中心的完整消息时，第一行实际上不是 `application/http` 消息的一部分。 第一行是附加的元数据，其中包括消息是请求消息还是响应消息，以及使响应与请求相互关联的消息 ID。 使用如下所示的另一个策略可以创建消息 ID：

```xml
<set-variable name="message-id" value="@(Guid.NewGuid())" />
```

应事先创建请求消息，将它存储在变量中，直到返回响应为止，然后将请求和响应作为单个消息发送。 但是，独立发送请求和响应并使用消息 ID 使两者相互关联，可以在消息大小方面获得更大的弹性，此外，能够使用多个分区并保留消息顺序，使请求更快出现在日志记录仪表板中。 在某些情况下，有效的响应也可能永远不会发送到事件中心，原因可能是 API 管理服务发生严重请求错误，但我们仍保留了该请求的记录。

用于发送响应 HTTP 消息的策略看起来与请求非常类似。完整的策略配置如下所示：

```xml
<policies>
  <inbound>
      <set-variable name="message-id" value="@(Guid.NewGuid())" />
      <log-to-eventhub logger-id="conferencelogger" partition-id="0">
      @{
          var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                      context.Request.Method,
                                                      context.Request.Url.Path + context.Request.Url.QueryString);

          var body = context.Request.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Request.Headers
                               .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                               .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                               .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "request:"   + context.Variables["message-id"] + "\n"
                              + requestLine + headerString + "\r\n" + body;
      }
  </log-to-eventhub>
  </inbound>
  <backend>
      <forward-request follow-redirects="true" />
  </backend>
  <outbound>
      <log-to-eventhub logger-id="conferencelogger" partition-id="1">
      @{
          var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                              context.Response.StatusCode,
                                              context.Response.StatusReason);

          var body = context.Response.Body?.As<string>(true);
          if (body != null && body.Length > 1024)
          {
              body = body.Substring(0, 1024);
          }

          var headers = context.Response.Headers
                                          .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                          .ToArray<string>();

          var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

          return "response:"  + context.Variables["message-id"] + "\n"
                              + statusLine + headerString + "\r\n" + body;
     }
  </log-to-eventhub>
  </outbound>
</policies>
```

`set-variable` 策略创建一个可供 `<inbound>` 节和 `<outbound>` 节中的 `log-to-eventhub` 策略访问的值。

## <a name="receiving-events-from-event-hubs"></a>从事件中心接收事件
使用 [AMQP 协议](https://www.amqp.org/)可从 Azure 事件中心接收事件。 Microsoft 服务总线团队提供了客户端库来方便使用事件。 支持两种不同的方法：一种是成为*直接使用者*，另一种是使用 `EventProcessorHost` 类。 在[事件中心编程指南](../event-hubs/event-hubs-programming-guide.md)中可以找到这两种方法的示例。 简而言之，两者的差别在于：`Direct Consumer` 提供给完全控制权，而 `EventProcessorHost` 可以自动完成一些繁琐的工作，但在如何处理这些事件上做出了假设。

### <a name="eventprocessorhost"></a>EventProcessorHost
为方便起见，本示例将使用 `EventProcessorHost`，但这不一定是此特定方案的最佳选择。 `EventProcessorHost` 努力确保用户无需担心特定事件处理器类中发生线程问题。 但是，在我们的方案中，只需将消息转换为另一种格式，并使用异步方法将它传递到另一个服务。 不需要更新共享状态，因此没有线程问题的风险。 在大多数情况下，`EventProcessorHost` 可能是最佳选择，当然也是更方便的选项。

### <a name="ieventprocessor"></a>IEventProcessor
使用 `EventProcessorHost` 时的核心概念是创建包含 `ProcessEventAsync` 方法的 `IEventProcessor` 接口的实现。 该方法的基本构成如下所示：

```csharp
async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
{

    foreach (EventData eventData in messages)
    {
        _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

        try
        {
            var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
            await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
        }
        catch (Exception ex)
        {
            _Logger.LogError(ex.Message);
        }
    }
    ... checkpointing code snipped ...
}
```

EventData 对象的列表将传递到此方法，我们需要迭代该列表。 每个方法的字节将分析成 HttpMessage 对象，该对象将传递到 IHttpMessageProcessor 的实例。

### <a name="httpmessage"></a>HttpMessage
`HttpMessage` 实例包含三个数据片段：

```csharp
public class HttpMessage
{
    public Guid MessageId { get; set; }
    public bool IsRequest { get; set; }
    public HttpRequestMessage HttpRequestMessage { get; set; }
    public HttpResponseMessage HttpResponseMessage { get; set; }

... parsing code snipped ...

}
```

`HttpMessage` 实例包含一个用于将 HTTP 请求连接到相应 HTTP 响应的 `MessageId` GUID，以及一个用于确定对象是否包含 HttpRequestMessage 和 HttpResponseMessage 实例的布尔值。 从 `System.Net.Http` 使用内置 HTTP 类，可以利用 `System.Net.Http.Formatting` 中包含的 `application/http` 分析代码。  

### <a name="ihttpmessageprocessor"></a>IHttpMessageProcessor
然后，`HttpMessage` 实例将转发到 `IHttpMessageProcessor` 的实现，这是为了分离从 Azure 事件中心接收和解释事件以及实际处理事件而创建的接口。

## <a name="forwarding-the-http-message"></a>转发 HTTP 消息
对于此示例，我认为将 HTTP 请求推送到 [Moesif API 分析](https://www.moesif.com)会很有趣。 Moesif 是专门用于 HTTP 分析和调试的基于云的服务。 该服务提供免费层，因此可以方便试用。它可以让我们实时查看流经 API 管理服务的 HTTP 请求。

`IHttpMessageProcessor` 实现如下所示：

```csharp
public class MoesifHttpMessageProcessor : IHttpMessageProcessor
{
    private readonly string RequestTimeName = "MoRequestTime";
    private MoesifApiClient _MoesifClient;
    private ILogger _Logger;
    private string _SessionTokenKey;
    private string _ApiVersion;
    public MoesifHttpMessageProcessor(ILogger logger)
    {
        var appId = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-APP-ID", EnvironmentVariableTarget.Process);
        _MoesifClient = new MoesifApiClient(appId);
        _SessionTokenKey = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-SESSION-TOKEN", EnvironmentVariableTarget.Process);
        _ApiVersion = Environment.GetEnvironmentVariable("APIMEVENTS-MOESIF-API-VERSION", EnvironmentVariableTarget.Process);
        _Logger = logger;
    }

    public async Task ProcessHttpMessage(HttpMessage message)
    {
        if (message.IsRequest)
        {
            message.HttpRequestMessage.Properties.Add(RequestTimeName, DateTime.UtcNow);
            return;
        }

        EventRequestModel moesifRequest = new EventRequestModel()
        {
            Time = (DateTime) message.HttpRequestMessage.Properties[RequestTimeName],
            Uri = message.HttpRequestMessage.RequestUri.OriginalString,
            Verb = message.HttpRequestMessage.Method.ToString(),
            Headers = ToHeaders(message.HttpRequestMessage.Headers),
            ApiVersion = _ApiVersion,
            IpAddress = null,
            Body = message.HttpRequestMessage.Content != null ? System.Convert.ToBase64String(await message.HttpRequestMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        EventResponseModel moesifResponse = new EventResponseModel()
        {
            Time = DateTime.UtcNow,
            Status = (int) message.HttpResponseMessage.StatusCode,
            IpAddress = Environment.MachineName,
            Headers = ToHeaders(message.HttpResponseMessage.Headers),
            Body = message.HttpResponseMessage.Content != null ? System.Convert.ToBase64String(await message.HttpResponseMessage.Content.ReadAsByteArrayAsync()) : null,
            TransferEncoding = "base64"
        };

        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApimMessageId", message.MessageId.ToString());

        EventModel moesifEvent = new EventModel()
        {
            Request = moesifRequest,
            Response = moesifResponse,
            SessionToken = _SessionTokenKey != null ? message.HttpRequestMessage.Headers.GetValues(_SessionTokenKey).FirstOrDefault() : null,
            Tags = null,
            UserId = null,
            Metadata = metadata
        };

        Dictionary<string, string> response = await _MoesifClient.Api.CreateEventAsync(moesifEvent);

        _Logger.LogDebug("Message forwarded to Moesif");
    }

    private static Dictionary<string, string> ToHeaders(HttpHeaders headers)
    {
        IEnumerable<KeyValuePair<string, IEnumerable<string>>> enumerable = headers.GetEnumerator().ToEnumerable();
        return enumerable.ToDictionary(p => p.Key, p => p.Value.GetEnumerator()
                                                         .ToEnumerable()
                                                         .ToList()
                                                         .Aggregate((i, j) => i + ", " + j));
    }
}
```

`MoesifHttpMessageProcessor` 利用可轻松将 HTTP 事件数据推送到其服务的[适用于 Moesif 的 C# API 库](https://www.moesif.com/docs/api?csharp#events)。 若要将 HTTP 数据发送到 Moesif 收集器 API，需要拥有帐户和应用程序 ID。可通过在 [Moesif 网站](https://www.moesif.com)上创建帐户，然后转到_右上方菜单_ -> “应用设置”来获取 Moesif 应用程序 ID  。

## <a name="complete-sample"></a>完整示例
GitHub 上提供了本示例的[源代码](https://github.com/dgilling/ApimEventProcessor)和测试。 需要准备好 [API 管理服务](get-started-create-service-instance.md)、[连接的事件中心](api-management-howto-log-event-hubs.md)和[存储帐户](../storage/common/storage-create-storage-account.md)才能自行运行本示例。   

本示例只是一个简单的控制台应用程序，可以侦听来自事件中心的事件，将其转换为 Moesif `EventRequestModel` 和 `EventResponseModel` 对象，然后将这些对象转发到 Moesif 收集器 API。

在下面的动画中，可以看到在开发人员门户对 API 发出的请求，显示正在接收、处理和转发消息的控制台应用程序，以及随后在事件流中显示的请求和响应。

![演示如何将请求转发到 Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## <a name="summary"></a>摘要
Azure API 管理服务提供了一个理想位置用于捕获 API 的双向 HTTP 流量。 Azure 事件中心是一个高度可缩放的、低成本的解决方案，可以捕获流量并将其馈送到辅助处理系统进行日志记录、监视和其他复杂分析。 只需编写几十行代码，就能轻松连接到 Moesif 等第三方流量监视系统。

## <a name="next-steps"></a>后续步骤
* 了解有关 Azure 事件中心的详细信息
  * [Azure 事件中心入门](../event-hubs/event-hubs-c-getstarted-send.md)
  * [使用 EventProcessorHost 接收消息](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [事件中心编程指南](../event-hubs/event-hubs-programming-guide.md)
* 了解有关 API 管理和事件中心集成的详细信息
  * [如何在 Azure API 管理中将事件记录到 Azure 事件中心](api-management-howto-log-event-hubs.md)
  * [记录器实体引用](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [log-to-eventhub 策略引用](/azure/api-management/api-management-advanced-policies#log-to-eventhub)
