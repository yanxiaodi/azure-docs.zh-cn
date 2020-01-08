---
title: 如何在 Azure Application Insights 中执行... | Microsoft Docs
description: 有关 Application Insights 的常见问题解答。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/04/2017
ms.author: mbullwin
ms.openlocfilehash: 9f80edf18a531d6c2850658ddef9c7007edb350f
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795511"
---
# <a name="how-do-i--in-application-insights"></a>如何在 Application Insights 中执行...？
## <a name="get-an-email-when-"></a>... 时收到电子邮件
### <a name="email-if-my-site-goes-down"></a>站点关闭时发送电子邮件
设置[可用性 Web 测试](../../azure-monitor/app/monitor-web-app-availability.md)。

### <a name="email-if-my-site-is-overloaded"></a>站点过载时发送电子邮件
针对“服务器响应时间”设置[警报](../../azure-monitor/app/alerts.md)。  介于 1 和 2 秒之间的阈值应可解决问题。

![](./media/how-do-i/030-server.png)

应用还可能通过返回失败代码来表明资源紧张的迹象。 针对“失败的请求”设置警报。 

如果想要针对“服务器异常”设置警报，可能需要执行[其他一些设置](../../azure-monitor/app/asp-net-exceptions.md)才能看到数据。 

### <a name="email-on-exceptions"></a>发生异常时发送电子邮件
1. [设置异常监视](../../azure-monitor/app/asp-net-exceptions.md)
2. 针对异常计数指标[设置警报](../../azure-monitor/app/alerts.md)

### <a name="email-on-an-event-in-my-app"></a>应用中发生某个事件时发送电子邮件
假设希望在发生特定的事件时收到电子邮件。 Application Insights 不直接提供此功能，但可以[在指标超过某个阈值时发送警报](../../azure-monitor/app/alerts.md)。

可以针对[自定义指标](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)设置警报，但不能针对自定义事件设置警报。 编写一些代码，以便在发生事件时增大指标：

    telemetry.TrackMetric("Alarm", 10);

或：

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

由于警报有两种状态，因此需要设置一个较小值，以便将警报视为已结束：

    telemetry.TrackMetric("Alarm", 0.5);

在[指标资源管理器](../../azure-monitor/app/metrics-explorer.md)中创建一个图表来查看警报：

![](./media/how-do-i/010-alarm.png)

现在，将警报设置为在指标超过中间值有一段时间时激发：

![](./media/how-do-i/020-threshold.png)

将平均期限设置为最小值。

当指标高于和低于阈值时，将收到电子邮件。

考虑的要点：

* 警报有两种状态（“警报”和“正常”）。 仅当收到指标时才评估状态。
* 仅当状态更改时才发送电子邮件。 这是为何要同时发送高值和低值指标的原因。
* 若要评估警报，可采用收到的值除以前一期限来计算平均值。 由于每次收到指标时都进行计算，因此发送电子邮件的频率比设置的期限还要高。
* 由于状态为“警报”和“正常”时都会发送电子邮件，因此可能要以两个状态的条件来仔细考虑单次发生的事件。 例如，不要采用“作业已完成”事件，而是采用“作业正在进行”条件，这样就可以在作业开始和结束时收到电子邮件。

### <a name="set-up-alerts-automatically"></a>自动设置警报
[使用 PowerShell 创建新警报](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>使用 PowerShell 管理 Application Insights
* [创建新资源](../../azure-monitor/app/powershell-script-create-resource.md)
* [创建新警报](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>不同版本中的单独遥测

* 应用中的多个角色：使用一个 Application Insights 资源，对 [cloud_Rolename](../../azure-monitor/app/app-map.md) 进行筛选。
* 开发、测试和发布版本相互独立：使用不同的 Application Insights 资源。 从 web.config 中选取检测密钥。[了解详细信息](../../azure-monitor/app/separate-resources.md)
* 报告生成版本：使用遥测初始值设定项添加属性。 [了解详细信息](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>监视后端服务器和桌面应用
[使用 Windows Server SDK 模块](../../azure-monitor/app/windows-desktop.md)。

## <a name="visualize-data"></a>可视化数据
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>包含来自多个应用的指标的仪表板
* 在 [指标资源管理器](../../azure-monitor/app/metrics-explorer.md) 中，自定义图表并将它保存到收藏夹。 将图表固定到 Azure 仪表板。

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>包含来自其他源和 Application Insights 的数据的仪表板
* [将遥测数据导出到 Power BI](../../azure-monitor/app/export-power-bi.md )。

或

* 使用 SharePoint 作为仪表板，在 SharePoint Web 部件中显示数据。 [使用连续导出和数据流分析导出到 SQL](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md)。  使用 PowerView 检查数据库，并创建适用于 PowerView 的 SharePoint Web 部件。

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>筛选出匿名用户或经过身份验证的用户
如果用户要登录，可以设置[经过身份验证的用户 ID](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)。（不会自动设置此 ID。）

然后，可以：

* 按特定的用户 ID 搜索

![](./media/how-do-i/110-search.png)

* 筛选匿名用户或经过身份验证的用户的指标

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>修改属性名称或值
创建[筛选器](../../azure-monitor/app/api-filtering-sampling.md#filtering)。 这样，便可以先修改或筛选遥测数据，然后将它从应用程序发送到 Application Insights。

## <a name="list-specific-users-and-their-usage"></a>列出特定的用户及其用法
如果只想[搜索特定的用户](#search-specific-users)，可以设置[经过身份验证的用户 ID](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)。

如果想要查看用户列表以及他们查看过哪些页面或登录频率等相关数据，可以使用两个选项：

* [设置经过身份验证的用户 ID](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)，[导出到数据库](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md)，并使用适当的工具分析用户数据。
* 如果只有少量的用户，可以发送自定义事件或指标，并在其中使用所需的数据作为指标值或事件名称，将用户 ID 设置为属性。 若要分析页面视图，可以替换标准的 JavaScript trackPageView 调用。 要分析服务器端遥测数据，可以使用遥测初始值设定项，将用户 ID 添加到所有服务器遥测数据。 然后可以筛选和细分指标，并按用户 ID 执行搜索。

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>减少从应用到 Application Insights 的流量
* 在 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) 中，禁用所有不需要用到的模块，例如性能计数器收集器。
* 使用 SDK 中的[采样和筛选](../../azure-monitor/app/api-filtering-sampling.md)功能。
* 在网页中，限制针对每个页面视图报告的 Ajax 调用次数。 在 `instrumentationKey:...` 后面的脚本片段中插入：`,maxAjaxCallsPerView:3`（或适当的数字）。
* 如果使用的是 [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)，请在发送结果之前，先计算指标值批次的聚合。 TrackMetric() 的一个重载可实现此目的。

详细了解[定价和配额](../../azure-monitor/app/pricing.md)。

## <a name="disable-telemetry"></a>禁用遥测
**动态停止和启动**从服务器收集与传输遥测数据：

### <a name="aspnet-classic-applications"></a>ASP.NET 经典应用程序

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>其他应用程序
不建议使用`TelemetryConfiguration.Active`控制台或 ASP.NET Core 应用程序上的单一实例。
如果您创建`TelemetryConfiguration`自己的实例设置`DisableTelemetry`到`true`。

对于 ASP.NET Core 应用程序可以访问`TelemetryConfiguration`实例使用[ASP.NET Core 依赖关系注入](/aspnet/core/fundamentals/dependency-injection/)。 请查找中的更多详细信息[ASP.NET Core 应用程序的 ApplicationInsights](../../azure-monitor/app/asp-net-core.md)一文。

## <a name="disable-selected-standard-collectors"></a>禁用选定的标准收集器
您可以禁用 （例如，性能计数器、 HTTP 请求或依赖项） 的标准收集器

* **ASP.NET 应用程序**-删除或注释掉中的相关行[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **ASP.NET Core 应用程序**-请按照中的遥测模块配置选项[ApplicationInsights ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>查看系统性能计数器
可以在指标资源管理器中显示的指标信息是一组系统性能计数器。 有一个标题为“服务器”的预定义边栏选项卡显示了其中的多个计数器。 

![打开 Application Insights 资源并单击“服务器”](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>如果看不到任何性能计数器数据
* 在自己的计算机或 VM 上设置一个 **IIS 服务器**。 [安装状态监视器](../../azure-monitor/app/monitor-performance-live-website-now.md)。
* **Azure 网站** - 目前不支持性能计数器。 可以获取多个指标作为 Azure 网站控制台的标准组成部分。
* **Unix 服务器** - [安装 collectd](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>显示更多性能计数器
* 首先[添加一个新图表](../../azure-monitor/app/metrics-explorer.md)，然后查看计数器是否出现在提供的基本集内。
* 如果没有，请[将计数器添加到性能计数器模块收集的集内](../../azure-monitor/app/performance-counters.md)。
