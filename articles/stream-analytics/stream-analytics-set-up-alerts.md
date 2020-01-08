---
title: 为 Azure 流分析作业设置监视警报
description: 本文介绍如何使用 Azure 门户为 Azure 流分析作业设置监视和警报。
services: stream-analytics
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 0fd489d856a16953a5a450a347c9737fe440ad28
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621758"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>为 Azure 流分析作业设置警报

必须监视 Azure 流分析作业，以确保作业持续正常运行。 本文介绍如何针对要监视的常见方案设置警报。 

你可以从门户中，通过操作日志数据的指标定义规则，以及[以编程方式](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)。

## <a name="set-up-alerts-in-the-azure-portal"></a>在 Azure 门户中设置警报
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>当作业意外停止时发出警报

以下示例演示如何针对作业进入失败状态设置警报。 建议对所有作业设置此警报。

1. 在 Azure 门户中，打开要为其创建警报的流分析作业。

2. 在“作业”页上，导航到“监视”部分。    

3. 选择**指标**，然后**新的警报规则**。

   ![Azure 门户流分析警报设置](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. 流分析作业名称应会自动显示在“资源”下。  单击“添加条件”，然后选择“配置信号逻辑”下的“所有管理操作”。   

   ![选择流分析警报的信号名称](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. 在“配置信号逻辑”下，将“事件级别”更改为“所有”，将“状态”更改为“失败”。      将保留**事件发起者**为空，并选择**完成**。

   ![配置流分析警报的信号逻辑](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. 选择现有的操作组或创建新组。 本示例创建了名为 **TIDashboardGroupActions** 的新操作组，其中包含一个“电子邮件”操作，该操作可将电子邮件发送到具有“所有者”Azure 资源管理器角色的用户。  

   ![为 Azure 流分析作业设置警报](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. “资源”、“条件”和“操作组”都应该有对应的条目。    请注意，为了触发警报，需要满足所定义的条件。 例如，可以每 5 分钟检测一次某个指标在过去 15 分钟的平均值。

   ![创建流分析警报规则](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   在“警报详细信息”中添加**警报规则名称**、**说明**和**资源组**，然后单击“创建警报规则”创建流分析作业的规则。  

   ![创建流分析警报规则](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>要监视的方案

建议监视以下警报，以了解流分析作业的性能。 在过去 5 分钟时段内，应每隔一分钟评估这些指标。

|指标|条件|时间聚合|阈值|纠正措施|
|-|-|-|-|-|
|SU% 利用率|大于|最大值|80|有多个因素可以提高 SU% 利用率。 可以使用查询并行化进行缩放，或者增加流单元数。 有关详细信息，请参阅[利用 Azure 流分析中的查询并行化](stream-analytics-parallelization.md)。|
|运行时错误|大于|总计|0|检查活动或诊断日志，并对输入、查询或输出进行适当的更改。|
|水印延迟|大于|最大值|当此指标在过去 15 分钟的平均值大于延迟容限（以秒为单位）时。 如果未修改延迟容限，默认值将设置为 5 秒。|尝试增加 SU 数量或将查询并行化。 有关 SU 的详细信息，请参阅[了解和调整流单元](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job)。 有关并行化查询的详细信息，请参阅[利用 Azure 流分析中的查询并行化](stream-analytics-parallelization.md)。|
|输入反序列化错误|大于|总计|0|检查活动或诊断日志，并对输入进行适当的更改。 有关诊断日志的详细信息，请参阅[使用诊断日志对 Azure 流分析进行故障排除](stream-analytics-job-diagnostic-logs.md)|

## <a name="get-help"></a>获取帮助

有关在 Azure 门户中配置警报的详细信息，请参阅[接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)。  

如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-get-started.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

