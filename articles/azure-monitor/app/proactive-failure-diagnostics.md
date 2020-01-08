---
title: Application Insights 中的智能检测 - 失败异常 | Microsoft 文档
description: 将针对到 Web 应用的失败请求速率的异常变化向用户发出警报，并提供诊断分析。 无需进行配置。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.author: mbullwin
ms.openlocfilehash: 46944603fdf45a2a7a14641086959bf61b3f773e
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465884"
---
# <a name="smart-detection---failure-anomalies"></a>智能检测 - 失败异常
如果 Web 应用的失败请求速率出现异常上升，[Application Insights](../../azure-monitor/app/app-insights-overview.md) 会几乎实时地自动通知你。 它会对 HTTP 请求速率或报告为失败的依赖项调用的异常上升进行检测。 对于请求而言，失败的请求通常是响应代码为 400 或更高的请求。 为了帮助会审和诊断问题，通知中会提供失败及相关遥测的特征分析。 还提供指向 Application Insights 门户的链接，以供进一步诊断。 该功能不需要任何设置或配置，因为它使用机器学习算法来预测正常的失败率。

此功能适用于任何托管的 web 应用，在云中或自己的服务器上的生成请求或依赖项遥测-例如，如果必须调用的辅助角色[trackrequest （)](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)或[TrackDependency()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

在设置[适用于项目的 Application Insights](../../azure-monitor/app/app-insights-overview.md) 后，如果应用生成特定最低遥测量，在进行切换和发送警报前，智能检测失败异常将花费 24 小时来了解应用的正常行为。

下面是一个示例警报。

![围绕失败显示群集分析的示例智能检测警报](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> 默认情况下，会收到比该示例中更短的格式邮件。 但是可以[切换为这一详细格式](#configure-alerts)。
>
>

请注意，它会指示：

* 相较于正常应用行为的失败率。
* 受影响的用户数，因此你知道需要有多担心。
* 与失败关联的特征模式。 在此示例中，有特定的响应代码、请求名称（操作）和应用版本。 这会立即通知在代码中开始查找的位置。 其他可能性可能是特定的浏览器或客户端操作系统。
* 似乎与特征失败相关联的异常、日志跟踪和依赖项失败（数据库或其他外部组件）。
* 直接指向 Application Insights 中遥测的相关搜索的链接。

## <a name="failure-anomalies-v2"></a>失败异常 v2
现已可用失败异常警报规则的新版本。 这一新版本在新的 Azure 警报平台上运行，并覆盖现有版本引入了多种改进。

### <a name="whats-new-in-this-version"></a>什么是此版本中的新增功能？
- 更快的检测问题
- 更加丰富的操作-创建警报规则带有关联[操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)名为"Application Insights 智能检测"，其中包含电子邮件和 webhook 操作，并可进行扩展以触发其他操作时的警报激发。
- 更有针对性地通知-此警报规则从发送电子邮件通知现在默认情况下发送到与订阅的监视查阅者和监视参与者角色关联的用户。 提供了对此的详细信息[此处](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification)。
- 通过 ARM 模板-请参阅示例配置更容易[此处](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)。
- 常见警报架构支持的通知发送的此警报规则按照[常见警报架构](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)。
- 统一的电子邮件模板的电子邮件通知此警报规则具有一致的外观和感觉与其他警报类型。 进行此更改后，若要获取使用详细的诊断信息的失败异常警报的选项不再可用。

### <a name="how-do-i-get-the-new-version"></a>如何获取新版本？
- 失败异常警报规则的新版本现在预配新创建的 Application Insights 资源。
- 现有的 Application Insights 资源使用经典版本的失败异常警报规则将获取最新版本一次其托管的订阅作为的一部分迁移到新的警报平台[经典警报停用过程](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).

> [!NOTE]
> 失败异常警报规则的新版本仍然可以免费使用。 此外，电子邮件和 webhook 触发操作由关联"Application Insights 智能检测"操作组也是免费。
> 
> 

## <a name="benefits-of-smart-detection"></a>智能检测的优点
普通[指标警报](../../azure-monitor/app/alerts.md)会通知你可能存在问题。 但是，智能检测将开始诊断工作，并执行以往都需要你自行进行的大量分析。 结果将整齐地打包，以帮助你快速找到问题的根源。

## <a name="how-it-works"></a>工作原理
智能检测监视从应用收到的遥测数据，特别是失败率。 此规则计算 `Successful request` 属性为 False 的请求数，和 `Successful call` 属性为 False 的依赖项调用数。 对于请求而言，默认情况下，`Successful request == (resultCode < 400)`（除非已将自定义代码写入[筛选器](../../azure-monitor/app/api-filtering-sampling.md#filtering)或生成自己的 [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) 调用）。 

应用性能具有典型的行为模式。 某些请求或依赖项调用更容易出现失败，而且总体失败率可能会随着负载的增加而上升。 智能检测使用机器学习来查找这些异常。

由于遥测数据从 Web 应用提供给 Application Insights，因此智能检测会将当前行为与过去几天看到的模式进行比较。 如果通过与先前性能比较观察到失败率中有异常上升，将触发分析。

分析触发后，服务将对失败的请求执行群集分析，以尝试标识特征化失败的值的模式。 在上面的示例中，分析发现大多数失败都是关于特定结果代码、请求名称、服务器 URL 主机和角色实例。 相比之下，分析已发现客户端操作系统属性分布在多个值上，因此它未列出。

当使用这些遥测调用检测服务时，分析器查找与已标识群集中的请求关联的异常和依赖项失败，以及与这些请求关联的任何跟踪日志的示例。

生成的分析以警报形式发送给用户，除非已将它配置为不这样做。

与[手动设置的警报](../../azure-monitor/app/alerts.md)一样，可以检查警报状态并在 Application Insights 资源的“警报”边栏选项卡中配置它。 但与其他警报不同，无需设置或配置智能检测。 如果需要，可以禁用它或更改其目标电子邮件地址。

### <a name="alert-logic-details"></a>警报逻辑详细信息

警报是由我们的专有机器学习算法触发的，因此我们不能共享确切的实现细节。 话虽如此，但我们知道你有时需要详细了解基础逻辑的工作原理。 确定是否应当触发警报时评估的主要因素包括： 

* 对 20 分钟滚动时间窗口中的请求/依赖项的失败百分比进行分析。
* 将过去 20 分钟的失败百分比与过去 40 分钟和过去 7 天的失败率进行比较，并寻找超过标准偏差 x 倍的重大偏差。
* 对最小失败百分比使用自适应限制，该限制根据应用的请求/依赖项的数量而变化。

## <a name="configure-alerts"></a>配置警报
可以禁用智能检测、更改电子邮件收件人、创建 webhook，或者选择启用更详细的警报消息。

打开“警报”页。 包括失败异常以及已手动设置的任何警报，并可以查看其当前是否处于警报状态。

![在“概述”页上单击“警报”磁贴。 或者，在任何“指标”页上，单击“警报”按钮。](./media/proactive-failure-diagnostics/021.png)

单击警报以配置它。

![配置](./media/proactive-failure-diagnostics/032.png)

请注意，可以禁用智能检测，但不能删除它（或创建另一个）。

#### <a name="detailed-alerts"></a>详细的警报
如果选择“获取更详细的诊断”，电子邮件将包含更多诊断信息。 有时，可以仅通过电子邮件中的数据诊断问题。

存在更详细的警告消息可能包含敏感信息的轻微危险性，因为它包括异常和跟踪消息。 但是，只有代码允许敏感信息包含在这些消息中，才会发生这种情形。

## <a name="triaging-and-diagnosing-an-alert"></a>会审和诊断警报
警报指示已检测到失败请求中有异常上升。 应用或其环境很可能存在某些问题。

根据请求百分比和受影响用户数，可以确定问题的紧急程度。 在上面的示例中，将 22.5% 的失败率与 1% 的正常失败率比较，指示一些不好的事情正在进行。 另一方面，只有 11 位用户受到影响。 如果它是你的应用，能够评估情况的严重情况。

在许多情况下，能够从提供的请求名称、异常、依赖项失败和跟踪数据快速诊断问题。

存在其他一些提示。 例如，该示例中的依赖项失败率与异常率 (89.3%) 相同。 这表明异常直接由依赖项失败引发，让你对清楚地了解应从代码中的哪个位置开始查找。

若要进一步调查，每个部分中的链接可直接转到[搜索页](../../azure-monitor/app/diagnostic-search.md)，该页面已针对相关请求、异常、依赖项或跟踪进行筛选。 或者，可以打开 [Azure 门户](https://portal.azure.com)，导航到应用的 Application Insights 资源并打开“失败”边栏选项卡。

在此示例中，单击“查看依赖项失败详细信息”链接将打开 Application Insights 搜索边栏选项卡。 它显示具有根本原因示例的 SQL 语句：在必填字段中提供了 NULL，并且在保存操作期间未通过验证。

![诊断搜索](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>查看最近的警报

单击“智能检测”  以转到最近警报：

![警报摘要](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>区别是什么...
智能检测失败异常对其他类似但又不同的 Application Insight 功能进行补充。

* [指标警报](../../azure-monitor/app/alerts.md)由你设置，并且可监视各种指标，如 CPU 占用率、请求速率、页面加载时间等。 可以将它们用于发出警告，例如在需要添加更多资源时。 相比之下，智能检测失败异常涵盖小范围的关键指标（当前仅失败请求速率），设计成一旦 Web 应用的失败请求速率相较于 Web 应用的正常行为而言显著增加，便会以近实时方式通知你。

    智能检测自动调整其阈值以响应现行条件。

    智能检测将开始诊断工作。
* [智能检测性能异常](proactive-performance-diagnostics.md)还使用计算机智能发现指标中的异常模式，使得你无需执行任何配置。 但与智能检测失败异常不同，智能检测性能异常的目的是查找可能不能提供很好服务的使用情况复写体分段，例如通过特定类型浏览器上的特定页面。 将每日执行分析，如果找到任何结果，则很可能紧急程度远低于警报。 相比之下，会对传入的遥测数据连续执行失败异常分析，如果服务器失败率超出预期值，会在几分钟内通知你。

## <a name="if-you-receive-a-smart-detection-alert"></a>如果收到智能检测警报
*为什么会收到此警报？*

* 我们检测到与之前一段时间的正常基线相比，失败请求率中出现异常上升。 对失败和关联遥测进行分析后，我们认为存在问题并且你应当给予关注。

*通知是否表示肯定存在问题？*

* 我们尝试针对应用中断或降级发出警报，但只有可以完全了解语义以及对应用或用户的影响。

所以你们会查看我的数据？ 

* 否。 该服务完全是自动的。 只有你会收到通知。 数据是[私有](../../azure-monitor/app/data-retention-privacy.md)数据。

*是否需要订阅此警报？*

* 不。 发送请求遥测的每个应用程序都有智能检测警报规则。

*是否可以取消订阅或者获取已发送至同事的通知？*

* 是，在“警报”规则中，单击“智能检测”规则可配置它。 可以禁用警报，或更改警报的收件人。

*我丢失了电子邮件。在哪里可以找到门户中的通知？*

* 在活动日志中。 在 Azure 中，打开应用的 Application Insights 资源，并选择“活动日志”。

*一些警报关于已知问题，我不希望接收它们。*

* 我们对积压工作会有警报抑制。

## <a name="next-steps"></a>后续步骤
这些诊断工具可帮助检查应用中的遥测数据：

* [指标资源管理器](../../azure-monitor/app/metrics-explorer.md)
* [搜索资源管理器](../../azure-monitor/app/diagnostic-search.md)
* [分析 - 功能强大的查询语言](../../azure-monitor/log-query/get-started-portal.md)

智能检测是完全自动执行的。 但是或许你想要设置更多的警报？

* [手动配置的指标警报](../../azure-monitor/app/alerts.md)
* [可用性 Web 测试](../../azure-monitor/app/monitor-web-app-availability.md)
