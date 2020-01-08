---
title: 使用参考数据在 Azure 流分析中查找
description: 本文介绍如何使用参考数据在 Azure 流分析作业的查询设计中查找或关联数据。
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: ed50dfd7e3c423c1c26a7dc19ae60dcb319f1850
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621614"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>使用参考数据在流分析中查找

引用数据 （也称为查找表） 是有限的数据集的是静态或缓慢变化的本质上，用于执行查找或以增加你的数据的流。 例如，在 IoT 方案中，可以将关于传感器的元数据（不经常更改）存储在参考数据中，并将其与实时 IoT 数据流相联接。 Azure 流分析在内存中加载参考数据以实现低延迟流处理。 为了在 Azure 流分析作业中利用参考数据，通常会在查询中使用[参考数据联接](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics)。 

流分析支持将 Azure Blob 存储和 Azure SQL 数据库用作参考数据的存储层。 你还可以通过 Azure 数据工厂对参考数据进行转换并/或将其复制到 Blob 存储，以使用[任意数量的基于云的数据存储和本地数据存储](../data-factory/copy-activity-overview.md)。

## <a name="azure-blob-storage"></a>Azure Blob 存储

引用数据建模为 blob 序列（在输入配置中定义），这些 blob 按blob 名称中指定的日期/时间顺序升序排列。 它**仅**支持使用**大于**序列中最后一个 blob 指定的日期/时间的日期/时间添加到序列的末尾。

### <a name="configure-blob-reference-data"></a>配置 blob 参考数据

若要配置引用数据，首先需要创建一个属于“引用数据”  类型的输入。 下表介绍在根据说明创建引用数据输入时需要提供的每个属性：

|**属性名称**  |**说明**  |
|---------|---------|
|输入别名   | 一个友好名称会用于作业查询，以便引用此输入。   |
|存储帐户   | 存储 blob 的存储帐户的名称。 如果其与流分析作业所在订阅相同，则可从下拉菜单中进行选择。   |
|存储帐户密钥   | 与存储帐户关联的密钥。 如果存储帐户的订阅与流分析作业相同，则自动填充此密钥。   |
|存储容器   | 容器对存储在 Microsoft Azure Blob 服务中的 blob 进行逻辑分组。 将 blob 上传到 Blob 服务时，必须为该 blob 指定一个容器。   |
|路径模式   | 用于对指定容器中的 blob 进行定位的路径。 在路径中，可以选择指定一个或多个使用以下 2 个变量的实例：<BR>{date}、{time}<BR>示例 1：products/{date}/{time}/product-list.csv<BR>示例 2：products/{date}/product-list.csv<BR>示例 3：product-list.csv<BR><br> 如果指定路径中不存在 blob，流分析作业将无限期地等待 blob 变为可用状态。   |
|日期格式 [可选]   | 如果在指定的路径模式中使用了 {date}，则可从支持格式的下拉列表中选择组织 blob 所用的日期格式。<BR>例如：YYYY/MM/DD、MM/DD/YYYY，等等。   |
|时间格式 [可选]   | 如果在指定的路径模式中使用了 {time}，则可从支持格式的下拉列表中选择组织 blob 所用的时间格式。<BR>例如：HH、HH/mm、或 HH-mm。  |
|事件序列化格式   | 为确保查询按预计的方式运行，流分析需要了解你对传入数据流使用哪种序列化格式。 对于引用数据，所支持的格式是 CSV 和 JSON。  |
|编码   | 目前只支持 UTF-8 这种编码格式。  |

### <a name="static-reference-data"></a>静态参考数据

如果不希望参考数据发生变化，则可以通过在输入配置中指定静态路径来启用对静态参考数据的支持。 Azure 流分析从指定路径中获取 Blob。 不需要 {date} 和 {time} 替换令牌。 由于引用数据是在 Stream Analytics 中不可变的不建议覆盖静态引用数据 blob。

### <a name="generate-reference-data-on-a-schedule"></a>按计划生成参考数据

如果引用数据是缓慢变化的数据集，则使用 {date} 和 {time} 替换令牌在输入配置中指定路径模式即可实现对刷新引用数据的支持。 流分析根据此路径模式选取更新的引用数据定义。 例如，使用 `sample/{date}/{time}/products.csv` 模式时，日期格式为“YYYY-MM-DD”  ，时间格式为“HH-mm”  ，可指示流分析在 2015 年 4 月 16 日下午 5:30（UTC 时区）提取更新的 Blob `sample/2015-04-16/17-30/products.csv`。

Azure 流分析每间隔一分钟都会自动扫描刷新的参考数据 Blob。 如果具有时间戳 10:30:00 的 blob 上传具有短暂的延迟 (例如，10:30:30) 时，会注意到在引用此 blob 的 Stream Analytics 作业中短暂的延迟。 若要避免这种情况下，建议将 blob 上传早于目标的有效时间 (10： 在此示例中的 30:00) 以允许足够的时间 Stream Analytics 作业，以发现和加载在内存中并执行操作。 

> [!NOTE]
> 当前，流分析作业仅在计算机时间提前于 blob 名称中的编码时间时才查找 blob 刷新。 例如，该作业将尽可能查找 `sample/2015-04-16/17-30/products.csv`，但不会早于 2015 年 4 月 16 日下午 5:30（UTC 时区）。 它*永远不会*查找编码时间早于发现的上一个 blob 的 blob。
> 
> 例如，一旦作业找到 blob`sample/2015-04-16/17-30/products.csv`它将忽略编码日期早于 2015 年 4 月 16 日，下午 5:30 的任何文件因此如果晚到达`sample/2015-04-16/17-25/products.csv`获取创建的 blob 在同一容器中该作业将不使用它。
> 
> 同样，如果 `sample/2015-04-16/17-30/products.csv` 仅在 2015 年 4 月 16 日晚上 10:03 生成，但容器中没有更早日期的 blob，则该作业将从 2015 年 4 月 16 日晚上 10:03 起开始使用此文件，而在此之前使用以前的引用数据。
> 
> 这种情况的一个例外是，当作业需要按时重新处理数据时或第一次启动作业时。 开始时，作业查找的是在指定的作业开始时间之前生成的最新 blob。 这样做是为了确保在作业开始时存在**非空**引用数据集。 如果找不到引用数据集，该作业会显示以下诊断：`Initializing input without a valid reference data blob for UTC time <start time>`。

[Azure 数据工厂](https://azure.microsoft.com/documentation/services/data-factory/)可用来安排以下任务：创建流分析更新引用数据定义所需的已更新 blob。 数据工厂是一项基于云的数据集成服务，可对数据移动和转换进行安排并使其实现自动化。 数据工厂支持[连接到大量基于云和本地的数据存储](../data-factory/copy-activity-overview.md)以及按指定的定期计划轻松地移动数据。 有关如何将数据工厂管道设置为生成按预定义计划刷新的流分析引用数据的详细信息和分步指导，请查看此 [GitHub 示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs)。

### <a name="tips-on-refreshing-blob-reference-data"></a>有关刷新 Blob 参考数据的提示

1. 请勿覆盖参考数据 Blob，因为它们是不可变的。
2. 刷新参考数据的推荐方法是：
    * 使用路径模式中的 {date}/{time}
    * 使用作业输入中定义的相同容器和路径模式来添加新 Blob
    * 使用大于序列中最后一个 Blob 指定的日期/时间  。
3. 引用数据 blob 并不  按 blob 的“上次修改”时间排序，而是按 blob 名称中使用 {date} 和 {time} 替换项指定的日期和时间排序。
3. 为了避免必须列出大量 blob，请考虑删除不再对其进行处理的非常旧的 blob。 请注意，在某些情况下（如重新启动），ASA 可能需要重新处理一小部分 blob。

## <a name="azure-sql-database"></a>Azure SQL 数据库

Azure SQL 数据库参考数据由流分析作业进行检索并作为快照存储在内存中以用于处理。 参考数据的快照还存储在你在配置设置中指定的存储帐户中的一个容器中。 作业启动时自动创建容器。 如果作业已停止或进入失败状态，则在重新启动作业时将删除自动创建的容器。  

如果参考数据是缓慢变化的数据集，则需要定期刷新作业中使用的快照。 流分析允许你在配置 Azure SQL 数据库输入连接时设置刷新率。 流分析运行时将按刷新率指定的时间间隔查询 Azure SQL 数据库。 支持的最快刷新率是每分钟一次。 对于每次刷新，流分析都会在所提供的存储帐户中存储一个新快照。

流分析提供了两个用于查询 Azure SQL 数据库的选项。 快照查询是必需的，必须包括在每个作业中。 流分析根据刷新时间间隔定期运行快照查询，并将查询结果（快照）用作参考数据集。 快照查询在大多数情况下应该都是适用的，但如果在使用大型数据集和快速刷新率时出现性能问题，则可以使用增量查询选项。 返回参考数据集需要 60 秒以上的查询将导致超时。

使用增量查询选项时，流分析最初会运行快照查询来获取基线参考数据集。 之后，流分析会根据刷新时间间隔定期运行增量查询来检索增量更改。 这些增量更改不断适用于参考数据集，以使其保持更新。 使用增量查询有助于减少存储成本和网络 I/O 操作。

### <a name="configure-sql-database-reference"></a>配置 SQL 数据库参考

若要配置 SQL 数据库参考数据，首先需要创建**参考数据**输入。 下表介绍了在创建参考数据输入时需要提供的每个属性及其说明。 有关详细信息，请参阅[将 SQL 数据库中的参考数据用于 Azure 流分析作业](sql-reference-data.md)。

|**属性名称**|**说明**  |
|---------|---------|
|输入别名|一个友好名称会用于作业查询，以便引用此输入。|
|订阅|选择自己的订阅|
|数据库|包含参考数据的 Azure SQL 数据库。|
|用户名|与 Azure SQL 数据库关联的用户名。|
|密码|与 Azure SQL 数据库关联的密码。|
|定期刷新|此选项用来选择刷新率。 选择“开启”将允许你以 DD:HH:MM 格式指定刷新率。|
|快照查询|这是从 SQL 数据库中检索参考数据的默认查询选项。|
|增量查询|对于使用大型数据集和较短刷新率的高级方案，可选择此项来添加增量查询。|

## <a name="size-limitation"></a>大小限制

流分析支持**最大大小为 300 MB** 的参考数据。 只有简单的查询才能达到参考数据最大大小 300 MB 限制。 随着查询复杂性增加以包括有状态处理（如开窗聚合、临时联接接和临时分析函数），支持的参考数据最大大小将会减小。 如果 Azure 流分析无法加载参考数据并执行复杂操作，则作业将耗尽内存并失败。 在这种情况下，SU % 利用率指标将达到 100%。    

|**流单元数**  |**大约支持的最大大小（以 MB 为单位）**  |
|---------|---------|
|第   |50   |
|3   |150   |
|至少 6   |300   |

作业增加的流单元数量超过 6 不会增加参考数据支持的最大大小。

对压缩的支持不可用于参考数据。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
