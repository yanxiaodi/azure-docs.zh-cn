---
title: Azure HDInsight 中的群集容量规划
description: 确定 Azure HDInsight 群集容量和性能规划的关键问题。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: 64de4078fb529140859f1d4ff2e973fd081a5400
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70916563"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>HDInsight 群集的容量规划

在部署 HDInsight 群集之前，应确定所需的性能和规模，为所需的群集容量做好规划。 这种规划有助于优化可用性与成本。 部署之后，某些群集容量决策不可更改。 如果性能参数发生更改，可以拆除群集，然后重新创建，而不会丢失存储的数据。

容量规划期间要提出的重要问题包括：

* 应在哪个地理区域中部署群集？
* 需要多少存储？
* 应部署哪种群集类型？
* 群集节点应使用的虚拟机 (VM) 大小和类型是什么？
* 群集应包含多少个工作节点？

## <a name="choose-an-azure-region"></a>选择 Azure 区域

Azure 区域确定群集的物理预配位置。 为了将读写延迟最小化，群集应靠近数据所在的位置。

许多 Azure 区域提供 HDInsight。 若要查找最靠近的区域，请参阅“[产品上市区域](https://azure.microsoft.com/regions/services/)”中“分析”下面的“HDInsight”项。

## <a name="choose-storage-location-and-size"></a>选择存储位置和大小

### <a name="location-of-default-storage"></a>默认存储的位置

默认存储（Azure 存储帐户或 Azure Data Lake Storage）必须与群集位于同一位置。 所有位置都提供 Azure 存储。 Data Lake Storage Gen1 已在某些区域中提供 - 请参阅[可用的 Azure 产品(按区域)](https://azure.microsoft.com/regions/services/) 中“存储”下面列出的当前 Data Lake Storage 可用性。

### <a name="location-of-existing-data"></a>现有数据的位置

如果已有一个包含数据的存储帐户或 Data Lake Storage，并想要将此存储用作群集的默认存储，则必须在与此存储相同的位置部署群集。

### <a name="storage-size"></a>存储大小

部署 HDInsight 群集之后，可以附加更多 Azure 存储帐户，或访问其他 Data Lake Storage。 所有存储帐户必须与群集位于同一位置。 Data Lake Storage 可以位于不同的位置，不过，这可能会造成某种程度的数据读/写延迟。

Azure 存储具有某些[容量限制](../azure-subscription-service-limits.md#storage-limits)，而 Data Lake Storage Gen1 几乎不受限制。

群集可以访问不同存储帐户的组合。 典型示例包括：

* 当数据量可能会超过单个 Blob 存储容器的存储容量时。
* 当对 Blob 容器的访问速率可能会超过阈值，从而发生限制时。
* 想要将已上传到 Blob 容器的数据提供给群集使用时。
* 出于安全原因想要隔离存储的不同部分，或要简化管理时。

对于 48 节点群集，我们建议使用 4 到 8 个存储帐户。 尽管存储总量可能已足够，但每个存储帐户能够为计算节点提供额外的网络带宽。 如果有多个存储帐户，请为每个存储帐户使用不带前缀的随机名称。 使用随机名称的目的是降低出现存储瓶颈（限制）或所有帐户发生共模故障的可能性。 为提高性能，请对每个存储帐户仅使用一个容器。

## <a name="choose-a-cluster-type"></a>选择群集类型

群集类型确定 HDInsight 群集配置为运行的工作负荷，例如 [Apache Hadoop](https://hadoop.apache.org/)、[Apache Storm](https://storm.apache.org/)、[Apache Kafka](https://kafka.apache.org/) 或 [Apache Spark](https://spark.apache.org/)。 有关可用群集类型的详细说明，请参阅 [Azure HDInsight 简介](hdinsight-overview.md#cluster-types-in-hdinsight)。 每个群集类型具有一个特定的部署拓扑，该拓扑附带大小和节点数方面的要求。

## <a name="choose-the-vm-size-and-type"></a>选择 VM 大小和类型

每个群集类型具有一组节点类型，每个节点类型在 VM 大小和类型方面提供特定的选项。

若要确定应用程序的最佳群集大小，可以建立群集容量基准，并根据指示增加大小。 例如，可以使用模拟工作负荷或“canary 查询”。 使用模拟工作负荷时，可在不同大小的群集上运行预期的工作负荷，并逐渐增加大小，直到达到所需的性能。 可在其他生产查询之间定期插入 canary 查询，以显示群集是否有足够的资源。

VM 大小和类型由 CPU 处理能力、RAM 大小和网络延迟决定：

* CPU：VM 大小决定核心数。 核心越多，每个节点可实现的并行计算度就越大。 此外，某些 VM 类型的核心更快。

* RAM：VM 大小还决定 VM 中可用的 RAM 量。 对于在内存中存储而不是从磁盘读取待处理数据的工作负荷，请确保工作节点能够提供足够的内存来容纳这些数据。

* 网络：对于大多数群集类型，群集处理的数据并不在本地磁盘上，而是在 Data Lake Storage 或 Azure 存储之类的外部存储服务中。 考虑节点 VM 与存储服务之间的网络带宽和吞吐量。 通常，更大 VM 的可用网络带宽越高。 有关详细信息，请参阅 [VM 大小概述](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)。

## <a name="choose-the-cluster-scale"></a>选择群集规模

群集的规模由其 VM 节点数量决定。 所有群集类型都存在一些具有特定缩放的节点类型，以及一些支持横向扩展的节点类型。例如，某个群集可能恰好需要三个 [Apache ZooKeeper](https://zookeeper.apache.org/) 节点或两个头节点。 对于以分布方式执行数据处理的工作节点，可以通过添加更多的工作节点来享受横向扩展的好处。

根据群集类型，增加工作节点数目可以添加更多的计算容量（例如更多的核心），但同时也可能会增大整个群集为所处理数据的内存中存储提供支持所需的内存总量。 在 VM 大小和类型的选择上，适当的群集规模通常是使用模拟工作负荷或 canary 查询凭经验选择出来的。

可以扩展群集来满足峰值负载需求，然后在不再需要这些额外的节点时缩减群集。 自动[缩放功能](hdinsight-autoscale-clusters.md)可让你根据预先确定的指标和计时自动缩放群集。 有关手动缩放群集的详细信息，请参阅[缩放 HDInsight 群集](hdinsight-scaling-best-practices.md)。

### <a name="cluster-lifecycle"></a>群集生命周期

在群集的生存期内会产生费用。 如果只是需要在特定的时间启动并运行群集，可以使用 [Azure 数据工厂创建按需群集](hdinsight-hadoop-create-linux-clusters-adf.md)。 还可以创建 PowerShell 脚本用于预配和删除群集，然后使用 [Azure 自动化](https://azure.microsoft.com/services/automation/)计划这些脚本。

> [!NOTE]  
> 删除某个群集时，也会一并删除其默认 Hive 元存储。 若要保留元存储供下一次重新创建群集时使用，可以使用 Azure 数据库或 [Apache Oozie](https://oozie.apache.org/) 等外部元数据存储。
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>查明群集作业错误

有时，多节点群集上多个映射和化简组件的并行执行可能导致出错。 若要解决此问题，请尝试通过在单个辅助角色节点上运行并发多个作业来进行分布式测试，然后展开此方法以在包含多个节点的群集上并发运行多个作业。 若要在 Azure 中创建单节点 HDInsight 群集，请在门户中设置新群集时，使用 "*自定义（大小、设置、应用）* " 选项，并将值1用于 "**群集大小**" 部分中的 "*工作节点数*"。


## <a name="quotas"></a>配额

确定目标群集 VM 大小、规模和类型之后，请检查订阅的当前配额容量限制。 达到配额限制时，可能无法部署新群集，或通过添加更多工作节点来横向扩展现有群集。 唯一存在配额限制的是每个订阅的区域级别的 CPU 核心配额。 例如，订阅可能会在美国东部区域有 30 个核心的限制。 如果需要请求增加配额，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择页面左下方的“帮助 + 支持”。
1. 选择“新建支持请求”。
1. 在“新建支持请求”页面的“基本信息”选项卡下，选择以下选项：
   - “问题类型”：“服务和订阅限制(配额)”
   - “订阅”：想要修改的订阅
   - “配额类型”：**HDInsight**
    
     ![创建支持请求来增加 HDInsight 核心配额](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. 在完成时选择“下一步:解决方案 >>”。
1. 在“详细信息”页上，输入问题的说明，选择问题的严重性、首选联系方法和其他必需字段。
1. 在完成时选择“下一步:查看 + 创建 >>”。
1. 在“查看 + 创建”选项卡中，选择“创建”。

> [!NOTE]  
> 如果需要增加专用区域中的 HDInsight 核心配额，请[提交允许列表请求](https://aka.ms/canaryintwhitelist)。

可以[联系支持部门来请求提高配额](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

但是，存在一些固定的配额限制，例如，单个 Azure 订阅最多只能有 10,000 个核心。 有关这些限制的详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-subscription-service-limits)。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Hadoop、Spark、Kafka 等在 HDInsight 中设置群集](hdinsight-hadoop-provision-linux-clusters.md)：了解如何使用 Apache Hadoop、Spark、Kafka、交互式 Hive、HBase、ML Services 或 Storm 在 HDInsight 中设置和配置群集。
* [监视群集性能](hdinsight-key-scenarios-to-monitor.md)：了解要在 HDInsight 群集中监视的、可能会影响群集容量的关键情况。
