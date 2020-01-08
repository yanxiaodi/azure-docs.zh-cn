---
title: Azure Monitor 中的日志警报进行故障排除 |Microsoft Docs
description: Azure 中日志警报规则的常见问题、错误和解决方法。
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 794f4ad5bba46af53280d35b55b762b9eef8e1a1
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675255"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>在 Azure Monitor 中排查日志警报问题  

本文介绍如何解决在 Azure Monitor 中设置日志警报时可能发生的常见问题， 并提供有关日志警报功能或配置的常见问题的解决方法。 

术语“日志警报”描述基于 [Azure Log Analytics 工作区](../learn/tutorial-viewdata.md)或 [Azure Application Insights](../../azure-monitor/app/analytics.md) 中的日志查询触发的规则。 在 [Azure Monitor 中的日志警报](../platform/alerts-unified-log.md)中详细了解功能、术语和类型。

> [!NOTE]
> 本文不考虑 Azure 门户中显示警报规则已触发以及不是通过关联的操作组执行通知的情况。 对于此类情况，请参阅[在 Azure 门户中创建和管理操作组](../platform/action-groups.md)中的详细信息。

## <a name="log-alert-didnt-fire"></a>日志警报未激发

下面 [Azure Monitor 中配置的日志警报规则](../platform/alerts-log.md)状态不按预期[显示为已激发](../platform/alerts-managing-alert-states.md)的部分常见原因。 

### <a name="data-ingestion-time-for-logs"></a>日志的数据引入时间

日志警报基于 [Log Analytics](../learn/tutorial-viewdata.md) 或 [Application Insights](../../azure-monitor/app/analytics.md) 定期运行查询。 由于 Azure Monitor 需要处理来自数千个客户以及全球各种源的若干 TB 的数据，因此，该服务很容易发生不同的时间延迟。 有关详细信息，请参阅 [Azure Monitor 日志中的数据引入时间](../platform/data-ingestion-time.md)。

如果系统发现所需的数据尚未引入，为了缓解延迟，它会等待一段时间，并重试警报查询多次。 为系统设置的等待时间呈指数级递增。 日志警报只会在数据可用后才会触发，因此，延迟可能是日志数据引入速度缓慢造成的。 

### <a name="incorrect-time-period-configured"></a>配置了错误的时间段

根据[日志警报的术语](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)一文中所述，配置中规定的时间段指定查询的时间范围。 查询仅返回在此时间范围内创建的记录。 

时间段限制为日志查询提取的数据以防止滥用，并规避日志查询中使用的任何时间命令（例如 **ago**）。 例如，如果时间段设置为 60 分钟，且在下午 1:15 运行查询，则在中午 12:15 和下午 1:15 之间创建的记录将用于日志查询。 如果日志查询使用类似于 **ago (1d)** 的时间命令，则查询仍只使用在中午 12:15 和下午 1:15 之间的创建数据，因为时间段设置为该间隔。

请检查配置中的时间段是否与查询匹配。 对于前面所述的示例，如果日志查询使用 **ago (1d)** （如绿色标记所示），则时间段应设置为 24 小时或 1440 分钟（如红色标记所示）。 此设置可确保查询按预期方式运行。

![时间段](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>设置“抑制警报”选项

根据[在 Azure 门户中创建日志警报规则](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)一文中的步骤 8 所述，日志警报提供一个“抑制警报”选项，用于在配置的一段时间内抑制触发和通知操作。 因此，你可能认为某个警报未激发， 但实际上它已激发，只不过是抑制了而已。  

![阻止警报](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>指标度量警报规则不正确

*指标度量日志警报*是日志警报的子类型，具有特殊的功能和受限的警报查询语法。 指标度量日志警报的规则要求查询输出是指标时序。 即，输出是包含等量大小的不同时间段以及相应聚合值的表。 

可以选择在该表中包含其他变量以及 **AggregatedValue**。 可以使用这些变量来为表排序。 

例如，假设指标度量日志警报的规则已配置为：

- 查询为 `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 时间段为 6 小时
- 阈值为 50
- 警报逻辑为三次连续违规
- 选择 **$table** 作为**聚合依据**

由于命令中包含 **summarize … by**，并提供了两个变量（**timestamp** 和 **$table**），系统将选择 **$table** 作为**聚合依据**。 系统会按 **$table** 字段将结果表排序，如以下屏幕截图所示。 然后查看每个表类型（例如 **availabilityResults**）的多个 **AggregatedValue**，以确定是否发生了三次或更多次的连续违规。

![包含多个值的指标度量查询执行](media/alert-log-troubleshoot/LogMMQuery.png)

由于**聚合依据**是基于 **$table** 定义的，因此数据已按 **$table** 列排序（如红框所示）。 然后我们进行分组并查看“聚合依据”字段的类型。 

例如，对于 **$table**，**availabilityResults** 的值将视为一个绘图/实体（如橙色框所示）。 在此绘图/实体值中，警报服务将检查三次连续违规（如绿框所示）。 违规时会对表值 **availabilityResults** 触发警报。 

同样，如果其他任何 **$table** 值发生三次连续违规，则会触发另一条警报通知。 警报服务自动按时间排序一个绘图/实体中的值（如橙色框所示）

现在，假设指标度量日志警报的规则已修改，且查询为 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`。 剩余的配置与前面相同，包括警报逻辑同样为三次连续违规。 在这种情况下，“聚合依据”选项默认为 **timestamp**。 在查询中只为 **summarize…by** 提供了一个值（即 **timestamp**）。 与前面的示例类似，在执行结束时，输出将如下所示。

   ![包含单个值的指标度量查询执行](media/alert-log-troubleshoot/LogMMtimestamp.png)

由于**聚合依据**是基于 **timestamp** 定义的，因此数据已按 **timestamp** 列排序（如红框所示）。 然后我们按 **timestamp** 进行分组。 例如，`2018-10-17T06:00:00Z` 的值将视为一个绘图/实体（如橙色框所示）。 在此绘图/实体值中，警报服务找不到连续违规（因为每个 **timestamp** 值只包含一个条目）。 因此永远不会触发警报。 在这种情况下，用户必须：

- 添加一个虚拟变量或现有变量（例如 **$table**），以使用“聚合依据”字段正确执行排序。
- 将警报规则重新配置为使用基于**违规总数**的警报逻辑。

## <a name="log-alert-fired-unnecessarily"></a>不必要地激发了日志警报

在 [Azure 警报](../platform/alerts-managing-alert-states.md)中查看 [Azure Monitor 中配置的日志警报规则](../platform/alerts-log.md)时，可能会意外触发该规则。 以下部分描述了某些常见原因。

### <a name="alert-triggered-by-partial-data"></a>部分数据触发了警报

Log Analytics 和 Application Insights 可能会发生引入和处理延迟。 在运行日志警报查询时，可能发现没有可用的数据，或者只有部分数据可用。 有关详细信息，请参阅 [Azure Monitor 中的日志数据引入时间](../platform/data-ingestion-time.md)。

根据警报规则的配置方式，如果在执行警报时日志中没有数据或者只有部分数据，则可能会错误地激发警报。 在这种情况下，我们建议你更改警报查询或配置。 

例如，如果日志警报规则配置为当分析查询的结果数小于 5 时触发，则当没有任何数据（零个记录）或只有部分结果（一个记录）时，警报将会触发。 但是，在发生数据引入延迟后，具有完整数据的同一查询可能会提供包含 10 个记录的结果。

### <a name="alert-query-output-is-misunderstood"></a>警报查询输出令人误解

你在分析查询中提供日志警报的逻辑。 分析查询可以使用各种大数据和数学函数。 警报服务使用指定时间段内的数据按指定的间隔运行查询。 警报服务根据警报类型对查询进行细微更改。 可以在“配置信号逻辑”屏幕上的“要执行的查询”部分查看此更改：

![要执行的查询](media/alert-log-troubleshoot/LogAlertPreview.png)

“要执行的查询”框显示日志警报服务运行的操作。 若要在创建警报之前了解警报查询输出的内容，可以通过 [Analytics 门户](../log-query/portals.md)或 [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/) 运行指定的查询及时间跨度。

## <a name="log-alert-was-disabled"></a>已禁用日志警报

以下部分列出了 Azure Monitor 禁用[日志警报规则](../platform/alerts-log.md)的一些原因。

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>在其中创建警报的资源不再存在

在 Azure Monitor 中创建的日志警报规则针对特定的资源，例如 Azure Log Analytics 工作区、Azure Application Insights 应用和 Azure 资源。 日志警报服务将针对指定的目标运行规则中提供的分析查询。 但是，在创建规则后，用户经常会在 Azure 中删除或移动日志警报规则的目标。 由于警报规则的目标不再有效，因此规则执行也就会失败。

在这种情况下，Azure Monitor 会禁用日志警报，并确保在该规则持续相当长一段时间（例如一周）无法运行时，不会产生不必要的费用。 可以通过 [Azure 活动日志](../../azure-resource-manager/resource-group-audit.md)查看 Azure Monitor 禁用日志警报的确切时间。 当 Azure Monitor 禁用日志警报规则时，会在 Azure 活动日志中添加一个事件。

Azure 活动日志中的以下示例事件适用于因持续失败而被禁用的警报规则。

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>日志警报中使用的查询无效

在 Azure Monitor 中创建为配置的一部分的每个日志警报规则必须指定警报服务要定期运行的分析查询。 在创建或更新规则时，分析查询可能使用了正确的语法。 但有时，在一段时间后，日志警报规则中提供的查询可能会出现语法问题，从而导致规则执行失败。 日志警报规则中提供的分析查询可能出现错误的一些常见原因包括：

- 该查询已写入到[跨多个资源的运行](../log-query/cross-workspace-query.md)。 一个或多个指定的资源不再存在。
- 配置的[指标度量类型日志警报](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules)具有不符合语法规范的警报查询
- 没有任何数据流向分析平台。 由于提供的查询没有数据，[查询执行出错](https://dev.loganalytics.io/documentation/Using-the-API/Errors)。
- [查询语言](https://docs.microsoft.com/azure/kusto/query/)的更改包含命令和函数的已修改格式。 因此，以前在警报规则中提供的查询不再有效。

[Azure 顾问](../../advisor/advisor-overview.md)会警告此类行为。 Azure 顾问会在“高可用性”类别下，针对特定的日志警报规则添加一条中度影响性的、说明为“请修复日志警报规则以确保执行监视”的建议。 如果在 Azure 顾问提供建议七天后仍未纠正日志警报规则中的警报查询，则 Azure Monitor 会禁用日志警报，并确保在该规则持续相当长一段时间（例如一周）无法运行时，不会产生不必要的费用。

可以通过查看 [Azure 活动日志](../../azure-resource-manager/resource-group-audit.md)中的事件，来了解 Azure Monitor 禁用日志警报规则的确切时间。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 中的日志警报](../platform/alerts-unified-log.md)。
- 详细了解 [Application Insights](../../azure-monitor/app/analytics.md)。
- 了解有关[日志查询](../log-query/log-query-overview.md)的详细信息。
