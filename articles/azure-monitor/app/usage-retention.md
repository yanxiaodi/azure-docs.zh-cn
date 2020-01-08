---
title: 使用 Azure Application Insights 为 Web 应用程序进行用户留存情况分析 | Microsoft docs
description: 有多少用户回归到应用？
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/03/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: bda79520dd86cc14161f6f22cd24feb2e35849ab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372598"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>使用 Azure Application Insights 为 Web 应用程序进行用户留存情况分析

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 中的“留存情况”功能可以帮助分析有多少用户回归到应用，以及他们以何频率执行特定的任务或达成目标。 例如，如果运行游戏网站，则可以会在输掉游戏后回归到网站的用户数与在获胜后回归的用户数进行比较。 此信息有助于改进用户体验和业务策略。

## <a name="get-started"></a>开始使用

如果在 Application Insights 门户的“留存情况”工具中没有看到数据，请[了解如何开始使用“使用情况”工具](usage-overview.md)。

## <a name="the-retention-tool"></a>留存情况工具

![留存情况工具](./media/usage-retention/retention.png)

1. 工具栏便于用户创建新的留存情况报表、打开现有留存情况报表、保存当前留存情况报表或将其另存为其他形式、还原已保存报表的更改、刷新报表数据、通过电子邮件或直接链接共享报表以及访问文档页。 
2. 默认情况下，留存情况显示执行了任意操作，然后在一段时间后返回并执行其他任意操作的所有用户。 可以选择事件的不同组合，将关注范围缩小为仅关注特定用户活动。
3. 添加一个或多个基于属性的筛选器。 例如，可以关注特定国家或地区中的用户。 在设置筛选器后单击“更新”。  
4. 整体留存情况图表显示选定时间段内用户留存情况的摘要。 
5. 网格根据 2 号图标处的查询生成器显示保留的用户数。 每行都表示在所显示的时间段内执行了任意事件的一群用户。 行中的每个单元格显示了该用户群中有多少人在之后的某个时间段内至少回归了一次。 某些用户可能在多个时间段内都回归过。 
6. 见解卡中显示了排名前 5 的启动事件和排名前 5 的返回事件，帮助用户更好地了解留存情况报表。 

![留存情况鼠标悬停](./media/usage-retention/hover.png)

用户可将鼠标悬停在留存情况工具的单元格上，以访问分析按钮和解释单元格含义的工具提示。 “分析”按钮可让用户使用预填充的查询来生成“分析”工具，以便从单元格生成用户。 

## <a name="use-business-events-to-track-retention"></a>使用业务事件跟踪留存情况

若要获得最有用的留存情况分析，请对表示重要业务活动的事件进行度量。 

例如，许多用户可能会打开应用中的页面，但是却不玩页面中显示的游戏。 因此，仅仅跟踪页面查看数将无法准确估计有多少人在玩过该游戏后又回来玩该游戏。 为了清晰了解回归玩家，应用应当在用户实际玩游戏时发送一个自定义事件。  

一种较好的做法是编写表示关键业务操作的自定义事件，并使用这些事件进行留存情况分析。 要捕获游戏结果，需要编写一行代码来向 Application Insights 发送一个自定义事件。 如果采用网页代码或 Node.JS 编写该事件，则它类似于以下内容：

```JavaScript
    appinsights.trackEvent("won game");
```

如果采用 ASP.NET 服务器代码，则如下所示：

```csharp
   telemetry.TrackEvent("won game");
```

[详细了解如何编写自定义事件](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)。


## <a name="next-steps"></a>后续步骤
- 若要启用使用体验，请首先发送[自定义事件](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)或[页面视图](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)。
- 如果已经发送自定义事件或页面视图，请浏览使用情况工具，了解用户如何使用服务。
    - [用户、会话、事件](usage-segmentation.md)
    - [漏斗图](usage-funnels.md)
    - [用户流](usage-flows.md)
    - [工作簿](../../azure-monitor/app/usage-workbooks.md)
    - [添加用户上下文](usage-send-user-context.md)


