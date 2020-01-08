---
title: 查询 Azure Monitor 日志来监视 Azure HDInsight 群集
description: 了解如何在 Azure Monitor 日志上运行查询，以监视在 HDInsight 群集中运行的作业。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/05/2018
ms.openlocfilehash: 51344ff7381b6392870b1fd0e331eed38a33915d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103520"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>查询 Azure Monitor 日志以监视 HDInsight 群集

了解有关如何使用 Azure Monitor 日志来监视 Azure HDInsight 群集的一些基本方案：

* [分析 HDInsight 群集指标](#analyze-hdinsight-cluster-metrics)
* [搜索特定的日志消息](#search-for-specific-log-messages)
* [创建事件警报](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先决条件

必须将 HDInsight 群集配置为使用 Azure Monitor 日志，并将特定于 HDInsight 群集的 Azure Monitor 日志监视解决方案添加到工作区。 有关说明，请参阅[将 Azure Monitor 日志与 HDInsight 群集配合使用](hdinsight-hadoop-oms-log-analytics-tutorial.md)。

## <a name="analyze-hdinsight-cluster-metrics"></a>分析 HDInsight 群集指标

了解如何查找 HDInsight 群集的特定指标。

1. 从 Azure 门户打开关联到 HDInsight 群集的 Log Analytics 工作区。
1. 选择“日志搜索”磁贴。
1. 在 "搜索" 框中键入以下查询，搜索配置为使用 Azure Monitor 日志的所有 HDInsight 群集的所有可用指标的所有指标，然后选择 "**运行**"。

        search *

    ![Apache Ambari 分析搜索所有指标](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "搜索所有指标")

    输出应如下所示：

    ![log analytics 搜索所有指标](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "搜索所有指标输出")

1. 在左窗格的“类型”下选择要深入了解的指标，然后选择“应用”。 以下屏幕快照显示已选定 `metrics_resourcemanager_queue_root_default_CL` 类型。

    > [!NOTE]  
    > 可能需要选择“[+]更多”按钮来查找所需指标。 此外，“应用”按钮位于列表底部，因此必须向下滚动才能看到它。

    请注意，文本框中的查询改为以下屏幕截图中突出显示的框中的内容：

    ![log analytics 搜索特定度量值](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "搜索特定指标")

1. 更深入了解此特定指标。 例如，现在可以使用以下查询，根据 10 分钟时间间隔中，按群集名称分类的所用资源的平均数来优化现有输出：

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)

1. 可以使用以下查询，基于 10 分钟时间范围内使用最大比例资源（以及 90% 和 95%）的时间优化结果，而不是基于所用资源的平均数进行优化：

        search in (metrics_resourcemanager_queue_root_default_CL) * | summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)

## <a name="search-for-specific-log-messages"></a>搜索特定的日志消息

了解如何在特定的时间窗口查找错误消息。 此处的步骤只是演示如何找到感兴趣的错误消息的一个示例。 可以使用任何可用属性来查找要查找的错误。

1. 从 Azure 门户打开关联到 HDInsight 群集的 Log Analytics 工作区。
2. 选择“日志搜索”磁贴。
3. 键入以下查询，搜索配置为使用 Azure Monitor 日志的所有 HDInsight 群集的所有错误消息，然后选择 "**运行**"。

         search "Error"

    应会看到一个输出，类似于以下输出：

    ![Azure 门户日志搜索错误](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "搜索所有错误输出")

4. 在左窗格的“类型”类别下，选择要深入了解的错误类型，然后选择“应用”。  请注意，结果已经过优化，只显示所选类型的错误。

5. 可通过使用左窗格中的可用选项来深入了解此特定错误。 例如：

    - 若要查看来自特定工作节点的错误消息，请执行以下操作：

        ![搜索特定错误 output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "搜索特定错误 output1")

    - 若要查看在特定时间发生的错误，请执行以下操作：

        ![搜索特定错误输出 2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "搜索特定错误输出 2")

6. 查看特定的错误。 可以选择“[+] 显示更多”来查看实际的错误消息。

    ![搜索特定错误 output3](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "搜索特定错误 output3")

## <a name="create-alerts-for-tracking-events"></a>创建用于跟踪事件的警报

创建警报的第一步是到达基于其触发警报的查询。 可以根据需要使用任何查询创建警报。

1. 从 Azure 门户打开关联到 HDInsight 群集的 Log Analytics 工作区。
2. 选择“日志搜索”磁贴。
3. 运行用于创建警报的以下查询，然后选择“运行”。

        metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0

    查询列出了 HDInsight 群集上运行失败的应用程序。

4. 选择页面顶部的“新建预警规则”。

    ![输入查询以创建警报 1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "输入查询以创建警报 1")

5. 在“创建规则”窗口中，输入用于创建警报的查询和其他详细信息，然后选择“创建预警规则”。

    ![输入查询以创建 alert2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "输入查询以创建 alert2")

若要编辑或删除现有警报，请执行以下操作：

1. 从 Azure 门户打开 Log Analytics 工作区。
2. 在左侧菜单中，选择“警报”。
3. 选择要编辑或删除的警报。
4. 可以使用以下选项：**保存**、**放弃**、**禁用**和**删除**。

    ![HDInsight Azure Monitor 日志警报删除编辑](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

有关详细信息，请参阅[使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/platform/alerts-metric.md)。

## <a name="see-also"></a>请参阅

* [使用 Azure Monitor 中的视图设计器创建自定义视图](../azure-monitor/platform/view-designer.md)
* [使用 Azure Monitor 创建、查看和管理指标警报](../azure-monitor/platform/alerts-metric.md)
