---
title: 监视群集性能 - Azure HDInsight
description: 如何监视 Azure HDInsight 中 Apache Hadoop 群集的运行状况和性能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 7a7544ef9fe5724d1f6c11918411a76461d908e5
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104396"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>监视 Azure HDInsight 中的群集性能

监视 HDInsight 群集的运行状况和性能对于维持最佳性能和资源利用率来说至关重要。 监视还可以帮助你检测并解决群集配置错误和用户代码问题。

以下各部分介绍了如何监视和优化群集上的负载、Apache Hadoop YARN 队列，以及检测存储限制问题。

## <a name="monitor-cluster-load"></a>监视群集负载

当群集上的负载均匀分布在所有节点中时，Hadoop 群集可以提供最佳性能。 这使得处理任务在运行时可以不受个体节点上的 RAM、CPU 或磁盘资源约束。

若要概括地查看群集的节点及其负载，请登录到 [Ambari Web UI](hdinsight-hadoop-manage-ambari.md)，然后选择“主机”选项卡。将按主机完全限定域名列出主机。 每个主机的运行状态由一个彩色运行状况指示器进行显示：

| 颜色 | 描述 |
| --- | --- |
| 红色 | 主机上至少有一个主组件已关闭。 悬停鼠标以查看列出受影响组件的工具提示。 |
| 橙色 | 主机上至少有一个辅助组件已关闭。 悬停鼠标以查看列出受影响组件的工具提示。 |
| 黄色 | Ambari 服务器已超过 3 分钟没有接收到来自主机的检测信号。 |
| 绿色 | 正常运行状态。 |

此外还将看到列，显示每个主机的内核数及 RAM 量、磁盘使用情况和平均负载。

![Apache Ambari 主机选项卡概述](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

选择任意主机名，详细了解在该主机上运行的组件及其指标。 查看 CPU 使用情况、负载、磁盘使用情况、内存使用情况、网络使用情况和进程数的可选时间线，了解这些指标。

![Apache Ambari 主机详细信息概述](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

有关设置警报和查看指标的详细信息，请参阅[使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)。

## <a name="yarn-queue-configuration"></a>YARN 队列配置

Hadoop 跨其分布式平台运行各种服务。 YARN (Yet Another Resource Negotiator) 协调这些服务并分配群集资源以确保任何负载都均匀地分布在群集中。

YARN 将 JobTracker、资源管理和作业计划/监视的两种责任划分为两个守护程序：一个全局资源管理器和一个每应用程序 ApplicationMaster (AM)。

资源管理器是一个纯计划程序，且仅仲裁所有竞争应用程序之间的可用资源。 资源管理器确保所有资源都处于使用状态，并针对各种常量（如 SLA、容量保障等）进行优化。 ApplicationMaster 处理来自于 ResourceManager 的资源，并与 NodeManager 一起执行和监视容器及其资源消耗。

如果多个租户共享一个大型群集，则产生针对群集资源的竞争。 CapacityScheduler 是一种可插入计划程序，通过对请求进行排队来协助资源共享。 CapacityScheduler 还支持分层队列，确保在允许其他应用程序的队列使用可用资源之前，在组织的子队列之间共享资源。

YARN 允许我们将资源分配给这些队列，并显示是否已分配所有可用资源。 若要查看有关队列的信息，请登录到 Ambari Web UI，然后从顶部菜单选择“YARN 队列管理器”。

![Apache Ambari YARN 队列管理器](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

YARN 队列管理器页的左侧显示队列的列表，以及分配给每个队列的容量百分比。

![YARN 队列管理器详细信息页](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

若要更加详细地查看队列，在 Ambari 仪表板中，从左侧列表选择“YARN”服务。 然后，在“快速链接”下拉菜单下，选择活动节点下的“资源管理器 UI”。

![资源管理器 UI 菜单链接](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

在资源管理器 UI 中，从左侧菜单中选择“计划程序”。 “应用程序队列”下将显示队列的列表。 此处可看到用于每个队列的容量、作业在队列之间的分布情况，以及作业是否受资源约束。

![Apache HAdoop 资源管理器 UI 菜单](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>存储限制

群集的性能瓶颈可能发生于存储级别。 出现此类瓶颈的最常见原因为阻止输入/输出 (IO) 操作，这发生在当正在运行的任务发送的 IO 超过了存储服务可以处理的 IO 数时。 这种阻止将创建等待处理完当前 IO 后再进行处理的 IO 请求队列。 发生此阻止的原因为存储限制，这并非物理限制，而是由存储服务通过服务级别协议 (SLA) 施加的限制。 此限制确保单个客户端或租户无法独占服务。 SLA 限制 Azure 存储的每秒 IO 数 (IOPS)。有关详细信息，请参阅 [Azure 存储可缩放性和性能目标](https://docs.microsoft.com/azure/storage/storage-scalability-targets)。

如果使用 Azure 存储，有关监视与存储相关问题（包括限制）的信息，请参阅[监视、诊断和排查 Microsoft Azure 存储问题](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting)。

如果群集的后备存储为 Azure Data Lake Storage (ADLS)，产生限制的原因很可能是因为带宽限制。 在这种情况下，可通过观察任务日志中的限制错误来确定限制。 对于 ADLS，请参阅这些文章中相应服务的限制部分：

* [Apache Hive on HDInsight 和 Azure Data Lake Storage 性能优化指南](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [MapReduce on HDInsight 和 Azure Data Lake Storage 性能优化指南](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Apache Storm on HDInsight 和 Azure Data Lake Storage 性能优化指南](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>后续步骤

请访问以下链接，了解有关故障排除和监视群集的详细信息：

* [分析 HDInsight 日志](hdinsight-debug-jobs.md)
* [使用 Apache Hadoop YARN 日志调试应用](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [在基于 Linux 的 HDInsight 上为 Apache Hadoop 服务启用堆转储](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
