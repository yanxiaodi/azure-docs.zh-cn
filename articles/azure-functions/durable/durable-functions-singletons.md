---
title: Durable Functions 的单一实例 - Azure
description: 如何使用 Azure Functions 的 Durable Functions 扩展中的单一实例。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: ba35999d5a7193ba691b14005dc8271120ac2be7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933229"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Durable Functions 中的单一实例业务流程协调程序 (Azure Functions)

对于后台作业，通常需要确保一次只运行特定业务流程协调程序的一个实例。 这可以在 [Durable Functions](durable-functions-overview.md) 中通过在创建业务流程协调程序时为其分配特定的实例 ID 来实现。

## <a name="singleton-example"></a>单一实例示例

以下 C# 和 JavaScript 示例显示了一个 HTTP 触发器函数，该函数创建一个单一实例后台作业业务流程。 该代码可确保只存在一个具有指定实例 ID 的实例。

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

function.json 文件如下所示：
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

JavaScript 代码如下所示：
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

默认情况下，实例 ID 是随机生成的 GUID。 但在此示例中，实例 ID 通过 URL 在路由数据中传递。 该代码调用 [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) 或 `getStatus` (JavaScript) 检查具有指定 ID 的实例是否已在运行。 如果未运行，将使用该 ID 创建实例。

> [!NOTE]
> 在此示例中有潜在的争用条件。 如果 **HttpStartSingle** 的两个实例同时执行，则两个函数调用都将报告成功，但实际上只会启动一个业务流程实例。 根据你的要求，这可能会产生不良副作用。 因此，必须确保没有两个请求可以同时执行此触发器函数。

业务流程协调程序函数的实现细节实际上无关紧要。 它可以是一个启动并完成的常规业务流程协调程序函数，也可以是一个永远运行的业务流程协调程序函数（即[永久业务流程](durable-functions-eternal-orchestrations.md)）。 重点是一次只有一个实例在运行。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解业务流程的本机 HTTP 功能](durable-functions-http-features.md)
