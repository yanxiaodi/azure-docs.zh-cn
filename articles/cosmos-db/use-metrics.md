---
title: 使用 Azure Cosmos DB 中的指标进行监视和调试
description: 使用 Azure Cosmos DB 中的指标调试常见问题和监视数据库。
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 06/18/2019
ms.reviewer: sngun
ms.openlocfilehash: ef457fe8c21bc7e62f910a78913069df32bea1a3
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275690"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的指标进行监视和调试

Azure Cosmos DB 提供吞吐量、存储、一致性、可用性和延迟的指标。 在 Azure 门户提供了这些指标的聚合的视图。 此外可以查看从 Azure Monitor API 的 Azure Cosmos DB 指标。 若要了解有关如何查看从 Azure monitor 的指标，请参阅[获取从 Azure Monitor 指标](cosmos-db-azure-monitor-metrics.md)一文。 

本文介绍常见用例，以及如何使用 Azure Cosmos DB 指标来分析和调试这些问题。 指标每 5 分钟收集一次且保留 7 天。

## <a name="view-metrics-from-azure-portal"></a>从 Azure 门户查看指标

1. 登录到 [Azure 门户](https://portal.azure.com/)

1. 打开**指标**窗格。 默认情况下，指标窗格中显示的存储，编制索引，Azure Cosmos 帐户中的所有数据库的请求单位度量值。 您可以筛选这些指标每个数据库、 容器或一个区域。 此外可以筛选特定的时间粒度的度量值。 在单独的选项卡上提供吞吐量、 存储、 可用性、 延迟和一致性指标的详细信息。 

   ![在 Azure 门户中的 cosmos DB 性能指标](./media/use-metrics/performance-metrics.png)

以下度量值中有**指标**窗格： 

* **吞吐量指标**-此指标显示使用或失败 （429 响应代码） 请求数，因为已超过容器预配的吞吐量或存储容量。

* **存储度量值**-此指标显示数据和索引使用情况的大小。

* **可用性指标**-此指标显示成功的请求数的百分比通过每小时请求总数。 由 Azure Cosmos DB Sla 定义的成功率。

* **延迟指标**-此指标显示观察到在其中运行你的帐户的区域中的 Azure Cosmos db 的读取和写入延迟。 你可以跨区域异地复制帐户可视化延迟。 此度量值并不代表的端到端请求延迟。

* **一致性指标**-此指标显示方式最终是您选择的一致性模型的一致性。 对于多区域帐户，此指标还具有所选的区域之间显示复制延迟。

* **系统指标**-此指标显示了由主分区提供服务的元数据请求数。 它还有助于识别限制的请求。

以下部分介绍常见方案中，可以使用 Azure Cosmos DB 指标。 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>了解成功的请求数或导致错误的请求数

若要开始，请前往 [Azure 门户](https://portal.azure.com)并导航到“指标”边栏选项卡  。 在边栏选项卡，查找 * * 的请求数已超出容量每 1 分钟图表。 该图表显示按状态代码划分的总请求数（以分钟计）。 有关 HTTP 状态代码的详细信息，请参阅 [Azure Cosmos DB 的 HTTP 状态代码](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)。

最常见的错误状态代码为 429（速率限制）。 此错误意味着对 Azure Cosmos DB 的请求超过预配的吞吐量。 此问题最常见的解决方案是为给定集合[纵向扩展 RU](./set-throughput.md)。

![每分钟的请求数](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>确定跨分区的吞吐量分布

对任何可伸缩应用程序而言，均必须具有良好的分区键基数。 若要确定任何由分区细分为分区容器的吞吐量分布，请导航到 [Azure 门户](https://portal.azure.com)中的“指标”边栏选项卡  。 在“吞吐量”选项卡中，存储细目显示在“各物理分区占用的最大 RU 数/秒”图表中   。 下图显示一个示例介绍因最左侧的倾斜分区而产生的不良数据分布。

![单个分区在下午 3:05 的使用率很高](media/use-metrics/metrics-17.png)

吞吐量分布不均可能导致热分区，进而造成请求受阻和需要重新分区  。 若要深入了解如何在 Azure Cosmos DB 中进行分区，请参阅[在 Azure Cosmos DB 中进行分区和缩放](./partition-data.md)。

## <a name="determine-the-storage-distribution-across-partitions"></a>确定跨分区的存储分布

对任何可伸缩应用程序而言，均必须具有良好的分区基数。 若要确定任何按分区细分为分区容器的存储分布，请前往 [Azure 门户](https://portal.azure.com)中的“指标”边栏选项卡。 在“存储”选项卡中，存储细分显示在顶部分区键图表所占用的“数据 + 索引”存储中。 下图说明了数据存储的不良分布，如最左侧的倾斜分区所示。

![不良数据分布示例](media/use-metrics/metrics-07.png)

可单击图表上的分区，深入查看当前造成分布倾斜的分区键。

![分区键使分布倾斜](media/use-metrics/metrics-05.png)

确定导致分布倾斜的分区键之后，可能需使用进一步分布的分区键重新执行容器分区。 若要深入了解如何在 Azure Cosmos DB 中进行分区，请参阅[在 Azure Cosmos DB 中进行分区和缩放](./partition-data.md)。

## <a name="compare-data-size-against-index-size"></a>比较数据与索引的大小

在 Azure Cosmos DB 中，所用存储空间总量是指数据大小和索引大小的总和。 索引大小通常只是数据大小的一小部分。 在 [Azure 门户](https://portal.azure.com)的“指标”边栏选项卡，“存储”选项卡显示基于数据和索引的存储空间使用量详情。

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

若要节省索引空间，可调整[索引策略](index-policy.md)。

## <a name="debug-why-queries-are-running-slow"></a>调试查询运行缓慢的原因

在 SQL API SDK 中，Azure Cosmos DB 提供查询执行的统计信息。

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

QueryMetrics 提供执行各查询组件所用时长的详细信息  。 导致查询长时间运行的最常见根因是扫描，这意味着查询无法利用索引。 可通过设置更好的筛选条件来解决此问题。

## <a name="next-steps"></a>后续步骤

前文介绍了如何使用 Azure 门户中提供的指标来监视和调试问题。 以下文章进一步介绍了如何提高数据库性能：

* 若要了解有关如何查看从 Azure monitor 的指标，请参阅[获取从 Azure Monitor 指标](cosmos-db-azure-monitor-metrics.md)一文。 
* [执行 Azure Cosmos DB 缩放和性能测试](performance-testing.md)
* [Azure Cosmos DB 性能提示](performance-tips.md)
