---
title: Durable Functions 中的诊断 - Azure
description: 了解如何使用 Azure Functions 的 Durable Functions 扩展诊断问题。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: d2badee3eaa5a9af48e89adc1b59beacc1571792
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933503"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Azure Durable Functions 中的诊断

可以使用多个选项通过 [Durable Functions](durable-functions-overview.md) 诊断问题。 其中有些选项与适用于正则函数的选项相同，还有一些选项是 Durable Functions 特有的。

## <a name="application-insights"></a>Application Insights

使用 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 是在 Azure Functions 中执行诊断和监视的建议方法。 这同样适用于 Durable Functions。 有关如何在函数应用中利用 Application Insights 的概述，请参阅[监视 Azure Functions](../functions-monitoring.md)。

Azure Functions Durable 扩展还会发出跟踪事件，用于跟踪业务流程的端到端执行。 可在 Azure 门户中使用 [Application Insights Analytics](../../azure-monitor/app/analytics.md) 工具来查找和查询这些事件。

### <a name="tracking-data"></a>跟踪数据

业务流程实例的每个生命周期事件会导致将一个跟踪事件写入 Application Insights 中的**跟踪**集合。 此事件包含带有多个字段的 **customDimensions** 有效负载。  字段名称的前面都附有 `prop__`。

* **hubName**：运行业务流程的任务中心的名称。
* **appName**：函数应用的名称。 当有多个函数应用共享同一个 Application Insights 实例时，此字段非常有用。
* **slotName**：当前函数应用在其中运行的[部署槽位](../functions-deployment-slots.md)。 利用部署槽位控制业务流程的版本时，此字段非常有用。
* **functionName**：业务流程协调程序或活动函数的名称。
* **functionType**：函数的类型，例如 **Orchestrator** 或 **Activity**。
* **instanceId**：业务流程实例的唯一 ID。
* **state**：实例的生命周期执行状态。 有效值包括：
  * **Scheduled**：函数已计划执行，但尚未开始运行。
  * **Started**：函数已开始运行，但尚未进入等待或完成状态。
  * **Awaited**：业务流程协调程序已计划某项工作，正在等待该工作完成。
  * **Listening**：业务流程协调程序正在侦听外部事件通知。
  * **Completed**：函数已成功完成。
  * **Failed**：函数失败并出错。
* **reason**：与跟踪事件关联的其他数据。 例如，如果某个实例正在等待外部事件通知，则此字段指示该实例正在等待的事件的名称。 如果函数失败，此字段会包含错误详细信息。
* **isReplay**：指示跟踪事件是否用于重播执行的布尔值。
* **extensionVersion**：持久任务扩展的版本。 在报告扩展中可能存在的 bug 时，此字段是特别重要的数据。 如果长时间运行的实例在运行时发生更新，它可能会报告多个版本。
* **sequenceNumber**：事件的执行序列号。 与时间戳组合使用可以帮助按执行时间对事件进行排序。 *请注意，如果主机在实例正在运行时重新启动，则此数字将重置为零，因此始终先按时间戳然后按 sequenceNumber 排序很重要。*

可以在 `host.json` 文件的 `logger` (Functions 1.x) 或 `logging` (Functions 2.x) 节中配置对发出到 Application Insights 的数据的跟踪详细程度。

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

默认情况下，会发出所有非重播跟踪事件。 可通过将 `Host.Triggers.DurableTask` 设置为 `"Warning"` 或 `"Error"` 来减少数据量，在这种情况下，只会在发生异常情况时发出跟踪事件。

若要启用发出详细业务流程重播事件，可以在 `host.json` 文件中的 `durableTask` 下将 `LogReplayEvents` 设置为 `true`，如下所示：

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> 默认情况下，Azure Functions 运行时会对 Application Insights 遥测数据采样，以免过度频繁地发出数据。 如果在短时间内发生了许多的生命周期事件，此行为可能会导致跟踪信息丢失。 [Azure Functions 监视文章](../functions-monitoring.md#configure-sampling)介绍了如何配置此行为。

### <a name="single-instance-query"></a>单实例查询

以下查询显示 [Hello Sequence](durable-functions-sequence.md) 函数业务流程的单个实例的历史跟踪数据。 该查询是使用 [Application Insights 查询语言 (AIQL)](https://aka.ms/LogAnalyticsLanguageReference) 编写的。 它会筛选出重播执行，以便仅显示逻辑执行路径。 可以通过按 `timestamp` 和 `sequenceNumber` 排序来安排事件顺序，如以下查询中所示：

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

结果是显示业务流程执行路径的跟踪事件的列表，包括所有活动函数，按执行时间以升序排序。

![Application Insights 查询](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>实例摘要查询

以下查询显示在指定时间范围中运行的所有业务流程实例的状态。

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

结果是实例 ID 的列表及其当前运行时状态。

![Application Insights 查询](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>日志记录

直接从业务流程协调程序函数写入日志时，必须注意业务流程协调程序的重播行为。 例如，考虑以下业务流程协调程序函数：

### <a name="precompiled-c"></a>预编译 C#

```csharp
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="c-script"></a>C# 脚本

```csharp
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

生成的日志数据应如下所示：

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> 请记住，尽管日志声明要调用 F1、F2 和 F3，但实际上仅在首次遇到这些函数时才调用这些函数。 在重播期间发生的后续调用将被跳过，输出将重播到业务流程协调程序逻辑。

如果只想针对非重播执行记录日志，可以编写一个条件表达式，规定仅当 `IsReplaying` 为 `false` 时才记录日志。 沿用上面的示例，不过这一次要执行重播检查。

#### <a name="precompiled-c"></a>预编译 C#

```csharp
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

做出这种更改后，日志输出如下所示：

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>自定义状态

使用自定义业务流程状态，可以为业务流程协调程序函数设置自定义状态值。 此状态是通过 HTTP 状态查询 API 或 `DurableOrchestrationClient.GetStatusAsync` API 提供的。 自定义业务流程状态为业务流程协调程序函数实现了更丰富的监视。 例如，业务流程协调程序函数代码可以包括 `DurableOrchestrationContext.SetCustomStatus` 调用来更新长时间运行的操作的进度。 然后，客户端（例如网页或其他外部系统）可以定期查询 HTTP 状态查询 API 以获得更丰富的进度信息。 下面提供了使用 `DurableOrchestrationContext.SetCustomStatus` 的示例：

### <a name="precompiled-c"></a>预编译 C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript（仅限 Functions 2.x）

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

在业务流程正在运行时，外部客户端可以提取此自定义状态：

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

客户端将收到以下响应：

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> 自定义状态有效负载限制为 16 KB 的 UTF-16 JSON 文本，因为它需要能够容纳在 Azure 表存储列中。 如果需要更大的有效负载，可以使用外部存储。

## <a name="debugging"></a>调试

Azure Functions 支持直接调试函数代码，Durable Functions 承袭了这项支持，不管它是在 Azure 中还是在本地运行。 但是，调试时需注意几种行为：

* **重播**：当收到新输入时，Orchestrator 函数会定期[重播](durable-functions-orchestrations.md#reliability)。 这意味着，业务流程协调程序函数的单次逻辑执行可能导致多次命中同一断点，尤其是事先已在函数代码中设置了该断点时。
* **等待**：每当在`await`业务流程协调程序函数中遇到时，它都会向持久任务框架调度程序返回控制权。 如果这是首次遇到特定的 `await`，则关联的任务永远不可恢复。 由于任务永远不可恢复，实际上也就无法跳过等待（在 Visual Studio 中按 F10）。 仅当任务正在重播时，才能跳过。
* **消息超时**：Durable Functions 在内部使用队列消息来驱动 orchestrator、活动和实体函数的执行。 在多 VM 环境中，长时间中断调试可能会使另一个 VM 拾取消息，从而导致重复执行。 正则队列触发器函数也存在此行为，但必须在此上下文中指出，因为队列属于实现细节。
* **停止和启动**：持久函数中的消息在调试会话之间保持不变。 如果在执行持久函数时停止调试并终止本地主机进程，则该函数可能会在将来的调试会话中自动重新执行。 如果不需要，这可能会造成混淆。 在调试会话之间从[内部存储队列](durable-functions-perf-and-scale.md#internal-queue-triggers)中清除所有消息是一种避免此行为的方法。

> [!TIP]
> 在业务流程协调程序函数中设置断点时，如果只想在非重播执行时中断，则可以设置仅当`IsReplaying`为时`false`中断的条件断点。

## <a name="storage"></a>存储

默认情况下，Durable Functions 在 Azure 存储中存储状态。 这意味着，可以使用 [Microsoft Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)等工具检查业务流程的状态。

![Azure 存储资源管理器屏幕快照](./media/durable-functions-diagnostics/storage-explorer.png)

此工具非常适合用于调试，因为可以看到业务流程所处的确切状态。 此外，还可以检查队列中的消息，了解哪项工作处于挂起状态（或停滞在某种状态）。

> [!WARNING]
> 尽管可以在表存储中方便查看执行历史记录，但不要对此表有任何依赖。 它可能会随着 Durable Functions 扩展的演变而变化。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 中的监视](../functions-monitoring.md)
