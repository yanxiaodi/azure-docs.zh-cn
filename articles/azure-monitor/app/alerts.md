---
title: 在 Azure Application Insights 中设置警报 | Microsoft Docs
description: 响应速度变慢、发生异常以及 Web 应用中出现其他性能或用法变化时接收通知。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: eb8e98f66d000290ce7eb07d3d73e82fbc43514a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60692941"
---
# <a name="set-alerts-in-application-insights"></a>在 Application Insights 中设置警报
当 Web 应用中的性能或用法指标发生变化时，[Azure Application Insights][start] 可发出警报。 

Application Insights 在[各种平台][platforms]上监视实时应用，帮助诊断性能问题和了解使用模式。

有多种类型的警报：

* [**指标警报**](../../azure-monitor/platform/alerts-metric-overview.md)告诉您一个指标时超过某个阈值一段时间-例如响应时间、 异常计数、 CPU 使用率或页面视图。
* [**日志警报**](../../azure-monitor/platform/alerts-unified-log.md)用来描述警报，警报的信号基于自定义的 Kusto 查询。
* [**Web 测试**][availability]：当站点在 Internet 上不可用或响应缓慢时，它会向用户发送通知。 [了解详细信息][availability]。
* [**主动诊断**](../../azure-monitor/app/proactive-diagnostics.md)：由系统自动配置，通知出现了异常的性能模式。

## <a name="set-a-metric-alert"></a>设置指标警报
打开警报规则选项卡，然后使用添加按钮。

![在警报规则选项卡，选择添加警报。 将应用设置为要测量的资源，提供警报的名称，然后选择指标。](./media/alerts/01-set-metric.png)

* 在其他属性之前设置资源。 如果想要针对性能或用法指标设置警报，请**选择“(组件)”资源**。
* 为警报指定的名称必须在资源组中（不只是在应用程序中）唯一。
* 请仔细留意系统要求输入阈值所采用的单位。
* 如果选中“电子邮件所有者...”框，系统会通过电子邮件将警报发送到有权访问此资源组的每个人。 要增加收件人的人数，请将他们添加到[资源组或订阅](../../azure-monitor/app/resources-roles-access-control.md)（而不是资源）。
* 如果指定“其他电子邮件”，系统会将警报发送到这些个人或组（无论是否选中“电子邮件所有者...”框）。 
* 如果已设置响应警报的 Web 应用，请设置 [Webhook 地址](../../azure-monitor/platform/alerts-webhooks.md)。 当警报激活时，以及警报得到解决时，系统会调用此地址。 （但请注意，查询参数不会以 Webhook 属性的形式传递。）
* 您可以禁用或启用警报： 请参阅顶部的按钮。

*我看不到“添加警报”按钮。*

* 使用的是组织帐户？ 如果对此应用程序资源拥有所有者或参与者访问权限，则可以设置警报。 看看访问控制选项卡。[了解访问控制][roles]。

> [!NOTE]
> 在“警报”边栏选项卡中，可以看到已设置了一个警报：[主动诊断](../../azure-monitor/app/proactive-failure-diagnostics.md)。 自动警报可监视一个特定的指标：请求失败率。 除非决定禁用主动警报，否则无需针对请求失败率设置自己的警报。
> 
> 

## <a name="see-your-alerts"></a>查看警报
当警报在“非活动”和“活动”之间切换状态时，将收到电子邮件。 

在警报规则选项卡中显示每个警报的当前状态。

警报下拉列表中包含最近活动的摘要：

![警报下拉列表](./media/alerts/010-alert-drop.png)

状态更改历史记录在活动日志中提供：

![在概述选项卡上，单击设置审核日志](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>警报的工作原理
* 警报具有三种状态：“从未激活”、“已激活”和“已解决”。 “已激活”表示指定的条件在上次评估时为 true。
* 警报状态更改时，将生成通知。 （如果警报条件在创建警报时已为 true，则在条件变为 false 之前，可能收不到通知。）
* 如果已选中“电子邮件”框或提供了电子邮件地址，每条通知都会生成一封电子邮件。 也可以查看“通知”下拉列表。
* 每次出现指标都会评估警报，否则不会评估。
* 将评估聚合前一时间段的指标，然后将其与阈值进行比较，确定新状态。
* 选择的时间段指定了聚合指标的间隔。 它不影响评估警报的频率：评估频率取决于指标出现的频率。
* 如果在一段时间内特定指标的数据未出现，该间隙对警报评估以及指标资源管理器中的图表会产生不同的影响。 在指标资源管理器中，如果未看到数据的时间超过图表的采样间隔时间，图表会显示 0 值。 但是，基于相同指标的警报不会重新评估，并且警报的状态将保持不变。 
  
    当数据最终出现时，图表将跳回到非零值。 警报根据指定时间段的可用数据进行评估。 如果新数据点是该时间段内唯一可用的数据点，会根据该数据点聚合。
* 即使设置的时间段较长，警报也可能在警报与正常状态之间经常变动。 如果指标值徘徊在阈值附近，则可能发生这种情况。 阈值中没有滞后：过渡到警报状态时的值与过渡到正常状态时的值相同。

## <a name="what-are-good-alerts-to-set"></a>如何设置合理的警报？
这取决于应用程序。 一开始，最好不要设置太多指标。 花点时间查看应用运行时的指标图表，了解正常运行时的情况。 此做法可帮助找到改善应用性能的方式。 然后再设置警报，在指标偏离正常区域时接收通知。 

常用的警报包括：

* [浏览器指标][client]（尤其是浏览器**页面加载时间**）非常适合用于 web 应用程序。 如果页面包含许多脚本，则应留意浏览器异常  。 若要获取这些指标和警报，必须设置[网页监视][client]。
* **服务器响应时间**适合用于 Web 应用程序的服务器端。 还可以设置警报来注意此指标，确定高请求率是否不按比例变化：变化可能表示应用资源不足。 
* **服务器异常** - 若要查看这些异常，必须执行一些[附加设置](../../azure-monitor/app/asp-net-exceptions.md)。

请记住，[主动故障率诊断](../../azure-monitor/app/proactive-failure-diagnostics.md)会自动监视应用响应请求的速率并提供故障代码。

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>如何设置异常警报使用自定义日志搜索

在本部分中，我们将介绍如何设置基于查询的异常警报。 对于此示例，假设我们想警报时的失败的率过去 24 小时内均超过 10%。

1. 转到 Azure 门户中的 Application Insight 资源。
2. 在左侧，在单击上配置**警报**。

    ![在左侧配置单击警报](./media/alerts/1appinsightalert.png)

3. 在警报选项卡顶部选择**新的警报规则**。

     ![在警报选项卡顶部单击新的警报规则](./media/alerts/2createalert.png)

4. 所需的资源应自动选择。 若要设置条件，请单击**添加条件**。

    ![单击添加条件](./media/alerts/3addcondition.png)

5. 在配置信号逻辑选项卡中选择**自定义日志搜索**

    ![单击自定义日志搜索](./media/alerts/4customlogsearch.png)

6. 在自定义日志搜索选项卡，输入您在"搜索查询"框中的查询。 对于此示例中，我们将使用 Kusto 查询的下面。
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![在搜索查询框中键入查询](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > 此外可以将这些步骤应用于其他类型的基于查询的警报。 您可以了解有关 Kusto 查询语言从这[Kusto 入门 doc](https://docs.microsoft.com/azure/kusto/concepts/)或这[到 Kusto SQL 备忘单](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. 在"警报逻辑"，选择它是否为基础的结果或公制度量单位数。 然后，选择条件 （大于、 等于、 小于） 以及一个阈值。 要更改这些值，而您可能会注意到条件预览句子更改。 在此示例中，我们将使用"等于"。

    ![在警报逻辑下选择的基于提供的选项和条件，然后键入阈值](./media/alerts/6alertlogic.png)

8. 在"评估基于"，设置间隔和频率。 此处的段必须匹配的值，我们将为在上面的查询中时间段。 然后单击**完成**。

    ![在底部设置间隔和频率，然后单击完成](./media/alerts/7evaluate.png)

9. 我们现在看到我们创建包含每月估计成本的条件。 下面下["操作组"](../platform/action-groups.md)可以创建新的组，也可以选择一个现有。 如果你想，您可以自定义操作。

    ![单击在选择或创建操作组下的按钮](./media/alerts/8actiongroup.png)

10. 最后添加警报详细信息 （警报规则名称、 说明、 严重性）。 完成后，单击**创建警报规则**底部。

    ![在警报详细信息下键入警报规则名称、 编写的说明，然后选择严重级别](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>如何取消订阅经典警报的电子邮件通知

本部分适用于**经典可用性警报**，**经典的 Application Insights 指标警报**，并对其**经典失败异常警报**。

如果以下任一应用，收到这些经典警报的电子邮件通知：

* 警报规则设置中的通知电子邮件收件人字段中列出你的电子邮件地址。

* 将电子邮件通知发送到用户的订阅的某些角色选项已激活，并按住该特定的 Azure 订阅的相应角色。

![警报通知的屏幕截图](./media/alerts/alert-notification.png)

若要更好地控制您的安全和隐私，我们通常建议对经典警报中显式指定通知收件人**通知电子邮件收件人**字段。 此选项以通知所有用户持有的某些角色用于向后兼容性。

若要取消订阅警报规则生成的电子邮件通知，请删除从电子邮件地址**通知电子邮件收件人**字段。

如果未显式列出你的电子邮件地址，我们建议禁用此选项以自动通知某些角色的所有成员，而是列出所有用户电子邮件，需要在通知电子邮件中接收通知的警报规则收件人字段。

## <a name="who-receives-the-classic-alert-notifications"></a>谁会收到（经典）警报通知？

本节仅适用于经典警报，并将帮助优化警报通知以确保只有预期的接收人能收到通知。 若要了解有关之间的差异的详细信息[经典警报](../platform/alerts-classic.overview.md)和新警报体验，请参阅[警报概述文章](../platform/alerts-overview.md)。 若要控制在全新警报体验中的警报通知，请使用[操作组](../platform/action-groups.md)。

* 建议将经典警报通知用于特定接收人。

* 对于有关任何 Application Insights 指标（包括可用性指标）的警报，如果启用“批/组”复选框选项，则会发送给订阅中具有所有者、参与者或读者角色的用户  。 实际上，可以访问包含 Application Insights 资源在内的订阅的所有用户均会收到通知  。

> [!NOTE]
> 如果当前使用“批/组”复选框选项并禁用它，则无法还原更改  。

如果需要根据用户角色通知用户，请使用新的警报体验/近实时警报。 使用[操作组](../platform/action-groups.md)，可以为具有任何参与者/所有者/读者角色（未融合为单一选项）的用户配置电子邮件通知。

## <a name="automation"></a>自动化
* [使用 PowerShell 自动设置警报](../../azure-monitor/app/powershell-alerts.md)
* [使用 Webhook 自动响应警报](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>另请参阅
* [可用性 Web 测试](../../azure-monitor/app/monitor-web-app-availability.md)
* [自动设置警报](../../azure-monitor/app/powershell-alerts.md)
* [主动诊断](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

