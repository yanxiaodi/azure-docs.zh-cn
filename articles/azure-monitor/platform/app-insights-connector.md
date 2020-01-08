---
title: 查看 Azure Application Insights 应用数据 | Microsoft Docs
description: 可以使用 Application Insights 连接器解决方案来诊断性能问题，以及了解用户在使用 Application Insights 监视的应用中执行的操作。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: magoedte
ms.openlocfilehash: 05f2f52da90f499f7ac16de179d9967b97579997
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68849187"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights 连接器管理解决方案（已弃用）

![Application Insights 符号](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> 在[跨资源查询](../../azure-monitor/log-query/cross-workspace-query.md)的支持下，不再需要 Application Insights 连接器管理解决方案。 该解决方案已被弃用并从 Azure 市场中删除，同时 Azure 商业云的 OMS 门户将于 2019 年 1 月 15 日正式弃用。 Azure 美国政府云将于 2019 年 3 月 30 日停用该门户。
>
>现有连接将继续运行到 2019 年 6 月 30 日为止。  随着 OMS 门户被弃用，将无法从门户中配置和删除现有的连接。 有关使用 PowerShell 删除现有连接的脚本，请参阅下面的[使用 PowerShell 删除连接器](#removing-the-connector-with-powershell)。
>
>有关查询多个应用程序的 Application Insights 日志数据的指导，请参阅[统一多个 Azure Monitor Application Insights 资源](../log-query/unify-app-resource-data.md)。 有关 OMS 门户弃用的详细信息，请参阅 [OMS 门户即将转移到 Azure](../../azure-monitor/platform/oms-portal-transition.md)。
>
> 

可以借助 Application Insights 连接器解决方案诊断性能问题，以及了解用户在使用 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 监视的应用中执行的操作。 Log Analytics 中提供了 Application Insights 中向开发人员显示的相同应用程序遥测数据视图。 但是，将 Application Insights 应用与 Log Analytics 集成时，将操作和应用程序数据放在一个位置可以增强应用程序的可见性。 使用相同的视图有助于与应用开发人员协作。 常见的视图可帮助减少检测和解决应用程序与平台问题的时间。

使用该解决方案时，可以：

- 在一个位置查看所有 Application Insights 应用，即使它们位于不同的 Azure 订阅中
- 将基础结构数据与应用程序数据相关联
- 在日志搜索中使用透视图可视化应用程序数据
- 在 Log Analytics 和 Azure 门户中通过 Log Analytics 数据透视 Application Insights 应用


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>连接的源

与其他大多数 Log Analytics 解决方案不同，代理不会收集 Application Insights 连接器的数据。 该解决方案使用的全部数据都直接来自于 Azure。

| 连接的源 | 支持 | 描述 |
| --- | --- | --- |
| [Windows 代理](../../azure-monitor/platform/agent-windows.md) | 否 | 解决方案不会从 Windows 代理收集信息。 |
| [Linux 代理](../../azure-monitor/learn/quick-collect-linux-computer.md) | 否 | 解决方案不会从 Linux 代理收集信息。 |
| [SCOM 管理组](../../azure-monitor/platform/om-agents.md) | 否 | 解决方案不会从连接的 SCOM 管理组中的代理收集信息。 |
| [Azure 存储帐户](collect-azure-metrics-logs.md) | 否 | 该解决方案不会从 Azure 存储收集信息。 |

## <a name="prerequisites"></a>系统必备

- 若要访问 Application Insights 连接器信息，必须拥有 Azure 订阅
- 必须至少配置了一个 Application Insights 资源。
- 必须是 Application Insights 资源的所有者或参与者。

## <a name="configuration"></a>配置

1. 从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview)或者使用[从解决方案库中添加 Log Analytics 解决方案](../../azure-monitor/insights/solutions.md)中所述的过程，启用 Azure Web 应用分析解决方案。
2. 浏览到 [Azure 门户](https://portal.azure.com)。 选择“所有服务”以打开 Application Insights。 然后，搜索 Application Insights。 
3. 在“订阅”下，选择包含 Application Insights 资源的订阅，然后在“名称”下选择一个或多个应用程序。
4. 单击“保存”。

大约 30 分钟后，数据将会可用，Application Insights 磁贴中会更新数据，如下图所示：

![Application Insights 磁贴](./media/app-insights-connector/app-insights-tile.png)

要记住的其他要点：

- 只能将 Application Insights 应用链接到一个 Log Analytics 工作区。
- 只能将[基本或企业 Application Insights 资源](https://azure.microsoft.com/pricing/details/application-insights)链接到 Log Analytics。 但是，可以使用 Log Analytics 的免费层。

## <a name="management-packs"></a>管理包

此解决方案不会在连接的管理组中安装任何管理包。

## <a name="use-the-solution"></a>使用解决方案

以下部分介绍了如何使用 Application Insights 仪表板中显示的边栏选项卡来查看应用中的数据并与其交互。

### <a name="view-application-insights-connector-information"></a>查看 Application Insights 连接器信息

单击“Application Insights”磁贴打开“Application Insights”仪表板查看以下边栏选项卡。

![Application insights 仪表板](./media/app-insights-connector/app-insights-dash01.png)

![Application insights 仪表板](./media/app-insights-connector/app-insights-dash02.png)

该仪表板包含下表中所示的边栏选项卡。 每个边栏选项卡按照指定范围和时间范围列出了匹配该边栏选项卡条件的最多 10 个项。 单击边栏选项卡底部的“查看全部”或单击边栏选项卡标题时，可运行返回所有记录的日志搜索。


| **列** | **说明** |
| --- | --- |
| 应用程序数 - 应用程序的数目 | 显示应用程序资源中的应用程序数目。 此外，还会列出应用程序名称，以及每个应用程序名称的应用程序记录数。 单击数字可以针对 <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> 运行日志搜索 <br><br>  单击应用程序名称可以针对该应用程序运行日志搜索，显示每台主机的应用程序记录、按遥测类型列出的记录，以及按类型列出的所有数据（基于前一天）。 |
| 数据量 – 发送数据的主机数 | 显示发送数据的计算机主机数。 此外，还列出计算机主机数以及每台主机的记录数。 单击数字可以针对 <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> 运行日志搜索 <br><br> 单击计算机名称可以针对该主机运行日志搜索，显示每台主机的应用程序记录、按遥测类型列出的记录，以及按类型列出的所有数据（基于前一天）。 |
| 可用性 – Web 测试结果 | 显示 Web 测试结果的圆环图，指示测试是通过还是失败。 单击该图表可以针对 <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> 运行日志搜索 <br><br> 结果显示所有测试的通过和失败结果数目。 它会显示最近一分钟产生了流量的所有 Web 应用。 单击应用程序名称可以查看显示已失败 Web 测试详细信息的日志搜索。 |
| 服务器请求数 – 每小时请求数 | 显示各个应用程序每小时服务器请求数的折线图。 将鼠标悬停在图表中的某个线条上可以查看在特定的时间点，接收请求数最多的 3 个应用程序。 此外，还显示在选定时间段内，接收请求的应用程序列表以及请求数目。 <br><br>单击图形可以针对 <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> 运行日志搜索，显示各个应用程序每小时的服务器请求数的更详细折线图。 <br><br> 单击列表中的某个应用程序可以针对 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> 运行日志搜索，显示请求列表、不同时间的请求数图表、请求持续时间以及请求响应代码列表。   |
| 失败 – 每小时的失败请求数 | 显示每小时失败的应用程序请求数的折线图。 将鼠标悬停在该图表上可以查看在特定的时间点请求失败次数最多的 3 个应用程序。 此外，还显示应用程序列表以及每个应用程序的失败请求数目。 单击该图表可以针对 <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> 运行日志搜索，显示失败应用程序请求数的更详细折线图。 <br><br>单击列表中的某个项可以针对 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> 运行日志搜索，显示失败的请求数、不同时间的失败请求数图表、请求持续时间以及失败请求的响应代码列表。 |
| 异常 - 每小时异常数 | 显示每小时异常数的折线图。 将鼠标悬停在该图表上可以查看在特定的时间点发生异常次数最多的 3 个应用程序。 此外，还显示应用程序列表以及每个应用程序的异常数目。 单击该图表可以针对 <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> 运行日志搜索，显示异常数的更详细折线图。 <br><br>单击列表中的某个项可以针对 <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> 运行日志搜索，显示异常数列表、不同时间的异常数图表、失败的请求数，以及异常类型列表。  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>使用日志搜索查看 Application Insights 透视图

单击仪表板中的任一项可以查看搜索中显示的 Application Insights 透视图。 透视图根据所选的遥测类型提供扩展的可视化效果。 因此，可视化内容会根据不同的遥测类型变化。

单击“应用程序”边栏选项卡中的任意位置可以查看“应用程序”透视图。

![Application Insights 中的“应用程序”透视图](./media/app-insights-connector/applications-blade-drill-search.png)

该透视图显示所选应用程序的概述。

“可用性”边栏选项卡显示一个不同的透视图，可在其中查看 Web 测试结果和相关的失败请求数。

![Application Insights 中的“可用性”透视图](./media/app-insights-connector/availability-blade-drill-search.png)

单击“服务器请求数”或“失败数”边栏选项卡中的任意位置时，透视图组件会发生变化，显示请求相关的可视化效果。

![Application Insights 中的“失败数”边栏选项卡](./media/app-insights-connector/server-requests-failures-drill-search.png)

单击“异常数”边栏选项卡中的任意位置可以查看根据异常数定制的可视化效果。

![Application Insights 中的“异常”边栏选项卡](./media/app-insights-connector/exceptions-blade-drill-search.png)

不管是否在“Application Insights 连接器”仪表板中单击了某项，在“搜索”页面本身中，返回 Application Insights 数据的任何查询都会显示 Application Insights 透视图。 例如，查看 Application Insights 数据时， **&#42;** 查询也会显示如下图所示的透视图选项卡：

![Application Insights](./media/app-insights-connector/app-insights-search.png)

透视图组件根据搜索查询进行更新。 这意味着，可以使用任何搜索字段来筛选结果，以便查看来自以下源的数据：

- 所有应用程序
- 单个选定的应用程序
- 一组应用程序

### <a name="pivot-to-an-app-in-the-azure-portal"></a>在 Azure 门户中透视应用

“Application Insights 连接器”边栏选项卡用来在你使用 Azure 门户时切换到选定的 Application Insights 应用。 可以使用该解决方案作为高级监视平台来帮助排查应用问题。 在任何连接的应用程序中发现潜在问题时，可以在 Log Analytics 搜索中深入到该问题，或者直接透视 Application Insights 应用。

若要透视，请单击每行末尾显示的省略号 ( **...** )，然后选择“在 Application Insights 中打开”。

>[!NOTE]
>Azure 门户中未提供“在 Application Insights 中打开”。

![在 Application Insights 中打开](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>采样更正数据

Application Insights 提供 *[采样更正](../../azure-monitor/app/sampling.md)* 来帮助减少遥测流量。 在 Application Insights 应用中启用采样时，可以减少 Application Insights 和 Log Analytics 中存储的条目数量。 尽管“Application Insights 连接器”页面和透视图中会保持数据一致性，但对于自定义查询，应手动更正采样的数据。

下面日志搜索查询中的采样更正示例：

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

“采样计数”字段出现在所有条目中，显示条目代表的数据点数目。 如果为 Application Insights 应用启用了采样，则“采样计数”大于 1。 若要统计应用程序生成的实际条目数，请对“采样计数”字段求和。

采样只会影响应用程序生成的条目总数。 不需要更正 **RequestDuration** 或 **AvailabilityDuration** 等指标字段的采样，因为这些字段显示被代表条目的平均值。

## <a name="input-data"></a>输入数据

解决方案从连接的 Application Insights 应用接收以下遥测类型的数据：

- 可用性
- Exceptions
- 请求
- 页面视图 - 要使工作区接收页面视图，必须将应用配置为收集该信息。 有关详细信息，请参阅 [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views)。
- 自定义事件 - 要使工作区接收自定义事件，必须将应用配置为收集该信息。 有关详细信息，请参阅 [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)。

数据可用后，Log Analytics 将从 Application Insights 接收该数据。

## <a name="output-data"></a>输出数据

将为每种输入数据创建 *ApplicationInsights* 类型的记录。 ApplicationInsights 记录具有以下部分中所述的属性：

### <a name="generic-fields"></a>泛型字段

| 属性 | 说明 |
| --- | --- |
| 类型 | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | 记录的时间 |
| ApplicationId | Application Insights 应用的检测密钥 |
| ApplicationName | Application Insights 应用的名称 |
| RoleInstance | 服务器主机的 ID |
| DeviceType | 客户端设备 |
| ScreenResolution |   |
| Continent | 请求的来源大洲 |
| Country | 请求的来源国家/地区 |
| 省/直辖市/自治区 | 请求的来源省、州或区域 |
| City | 请求的来源市或镇 |
| isSynthetic | 指示请求是由用户还是自动化方法创建的。 True = 自动方法或 false = 用户生成 |
| SamplingRate | SDK 生成的、发送到门户的遥测百分比。 范围为 0.0-100.0。 |
| SampledCount | 100/(SamplingRate)。 例如，4 =&gt; 25% |
| IsAuthenticated | 真或假 |
| OperationID | 具有相同操作 ID 的项在门户中显示为相关项。 通常为请求 ID |
| ParentOperationID | 父操作的 ID |
| OperationName |   |
| SessionId | 用于唯一标识创建请求所在的会话的 GUID |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>可用性特定的字段

| 属性 | 描述 |
| --- | --- |
| TelemetryType | 可用性 |
| AvailabilityTestName | Web 测试的名称 |
| AvailabilityRunLocation | http 请求的地理来源 |
| AvailabilityResult | 指示 Web 测试的成功结果 |
| AvailabilityMessage | 附加到 Web 测试的消息 |
| AvailabilityCount | 100/(采样率)。 例如，4 =&gt; 25% |
| DataSizeMetricValue | 1.0 或 0.0 |
| DataSizeMetricCount | 100/(采样率)。 例如，4 =&gt; 25% |
| AvailabilityDuration | Web 测试的持续时间，以毫秒为单位 |
| AvailabilityDurationCount | 100/(采样率)。 例如，4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Web 测试的唯一 GUID |
| AvailabilityTimestamp | 可用性测试的精确时间戳 |
| AvailabilityDurationMin | 对于采样的记录，此字段显示被代表数据点的最小 Web 测试持续时间（毫秒） |
| AvailabilityDurationMax | 对于采样的记录，此字段显示被代表数据点的最大 Web 测试持续时间（毫秒） |
| AvailabilityDurationStdDev | 对于采样的记录，此字段显示被代表数据点的所有 Web 测试持续时间（毫秒）之间的标准偏差 |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>异常特定的字段

| type | ApplicationInsights |
| --- | --- |
| TelemetryType | 异常 |
| ExceptionType | 异常的类型 |
| ExceptionMethod | 创建异常的方法 |
| ExceptionAssembly | 程序集包含框架和版本以及公钥令牌 |
| ExceptionGroup | 异常的类型 |
| ExceptionHandledAt | 指示处理异常的级别 |
| ExceptionCount | 100/(采样率)。 例如，4 =&gt; 25% |
| ExceptionMessage | 异常的消息 |
| ExceptionStack | 异常的完整堆栈 |
| ExceptionHasStack | 如果异常具有堆栈，则为 True |



### <a name="request-specific-fields"></a>请求特定的字段

| 属性 | 说明 |
| --- | --- |
| 类型 | ApplicationInsights |
| TelemetryType | 请求 |
| ResponseCode | 发送到客户端的 HTTP 响应 |
| RequestSuccess | 指示成功或失败。 True 或 False。 |
| RequestID | 用于唯一标识请求的 ID |
| RequestName | GET/POST + URL 基 |
| RequestDuration | 请求持续时间（秒） |
| URL | 请求的 URL，不包括主机 |
| 主机 | Web 服务器主机 |
| URLBase | 请求的完整 URL |
| ApplicationProtocol | 应用程序使用的协议类型 |
| RequestCount | 100/(采样率)。 例如，4 =&gt; 25% |
| RequestDurationCount | 100/(采样率)。 例如，4 =&gt; 25% |
| RequestDurationMin | 对于采样的记录，此字段显示被代表数据点的最小请求持续时间（毫秒）。 |
| RequestDurationMax | 对于采样的记录，此字段显示被代表数据点的最大请求持续时间（毫秒） |
| RequestDurationStdDev | 对于采样的记录，此字段显示被代表数据点的所有请求持续时间（毫秒）之间的标准偏差 |

## <a name="sample-log-searches"></a>示例日志搜索

此解决方案不会在仪表板上显示一组示例日志搜索。 但是，[查看 Application Insights 连接器信息](#view-application-insights-connector-information)部分中显示了示例日志搜索查询与说明。

## <a name="removing-the-connector-with-powershell"></a>使用 PowerShell 删除连接器
随着 OMS 门户被弃用，将无法从门户中配置和删除现有的连接。 可使用以下 PowerShell 脚本删除现有连接。 必须是工作区的所有者或贡献者以及 Application Insights 资源的读者才能执行此操作。

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

你可以使用以下可调用 REST API 调用的 PowerShell 脚本来检索应用程序列表。 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
此脚本需要持有者身份验证令牌才能对 Azure Active Directory 进行身份验证。 检索此令牌的一种方法是使用 [REST API 文档站点](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate)中的文章。 单击“试用”，然后登录 Azure 订阅。 可从“请求预览”复制持有者令牌，如下图所示。


![持有者令牌](media/app-insights-connector/bearer-token.png)


还可使用日志查询检索应用程序列表：

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>后续步骤

- 使用[日志搜索](../../azure-monitor/log-query/log-query-overview.md)可以查看 Application Insights 应用的详细信息。
