---
title: Azure Cosmos DB 的常见用例和方案
description: 了解 Azure Cosmos DB 最常见的五个用例：用户生成的内容、事件日志记录、目录数据、用户首选项数据和物联网 (IoT)。
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: e22b426b2172c169f9343569fffac57f370afbee
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219882"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Azure Cosmos DB 常见用例
本文概述了 Azure Cosmos DB 的几个常见用例。  本文中的建议可以作为使用 Cosmos DB 开发应用程序的一个起点。   

阅读本文之后，能够回答以下问题： 

* Azure Cosmos DB 的常见用例有哪些？
* 对零售应用程序使用 Azure Cosmos DB 有什么好处？
* 将 Azure Cosmos DB 用作物联网 (IoT) 系统的数据存储空间有什么好处？
* 对 Web 和移动应用程序使用 Azure Cosmos DB 有什么好处？

## <a name="introduction"></a>介绍
[Azure Cosmos DB](../cosmos-db/introduction.md) 由 Microsoft 提供，是全球分布的数据库服务。 使用该服务，客户可在任意数量的地理区域间弹性而独立地缩放吞吐量和存储空间。 Azure Cosmos DB 是一项全球分布式数据库服务，它在当前市场中率先提供了综合[服务级别协议](https://azure.microsoft.com/support/legal/sla/cosmos-db/)，包含吞吐量、延迟、可用性和一致性。 

Azure Cosmos DB 是一种全球分步的多模型数据库，广泛用于应用程序和用例。 它对于需要低延迟毫秒级响应时间或需要全局快速缩放的任何[无服务器的](https://azure.com/serverless)应用程序都是一个不错的选择。 它以可扩展的方式本机支持多种数据模型（键值、文档、图形和纵栏式）以及许多用于数据访问的 API，包括 [Azure Cosmos DB 的 API for MongoDB](mongodb-introduction.md)、[SQL API](documentdb-introduction.md)、[Gremlin API](graph-introduction.md) 和[表 API](table-introduction.md)。 

下面介绍了 Azure Cosmos DB 的几个属性，这些属性使 Cosmos DB 非常适用于想要实现全球分布的高性能应用程序。

* Azure Cosmos DB 采用本机方式对数据进行分区，以实现高可用性和可伸缩性。 Azure Cosmos DB 为所有单区域帐户和具有松散一致性的所有多区域帐户提供对可用性、吞吐量和一致性的 99.99% 保证，为所有多区域数据库帐户提供 99.999% 的读取可用性。
* Azure Cosmos DB 采用由 SSD 提供支持的存储，具有低延迟毫秒级响应时间。
* Azure Cosmos DB 支持最终、一致前缀、会话和受限停滞等一致性级别，从而实现充分的灵活性和较高的性价比。 Azure Cosmos DB 在级别一致性方面的灵活性超过任何其他数据库服务。 
* Azure Cosmos DB 提供了灵活的数据友好型定价模式，独立测量存储和吞吐量。
* 通过 Azure Cosmos DB 保留的吞吐量模型，可考虑读取/写入数量而不考虑基础硬件的 CPU/内存/IOP。
* Azure Cosmos DB 的设计允许扩展到每天约数万亿个请求的大规模请求量。

这些特性对于需要低响应时间和需要处理大量读取和写入操作的 Web、移动、游戏和 IoT 应用程序尤为有益。

## <a name="iot-and-telematics"></a>IoT 和 Telematic
IoT 用例在引入、处理和存储数据方法方面通常具有相同的模式。  首先，这些系统需要引入各处设备传感器中的激增数据。 其次，这些系统可以处理和分析流式传输数据，以获得实时见解。 然后将数据存档到闲置存储以进行批量分析。 Microsoft Azure 提供可应用于 IoT 用例的多种服务，包括 Azure Cosmos DB、Azure 事件中心、Azure 流分析、Azure 通知中心、Azure 机器学习、Azure HDInsight 和 Power BI。 

![Azure Cosmos DB IoT 参考体系结构](./media/use-cases/iot.png)

由于 Azure 事件中心可以低延迟引入高吞吐量数据，因此它可以引入激增数据。 可以将需要进行实时分析的引入数据传输至 Azure 流分析，以进行实时分析。 可将数据加载到 Azure Cosmos DB 中进行即席查询。 将数据加载到 Azure Cosmos DB 后，即可对其进行查询。 此外，可以在更改源中读取新数据以及对现有数据的更改。 更改源是持久性的、仅限追加的日志，可按顺序存储对 Cosmos 容器的更改。 Azure Cosmos DB 中的所有数据或者仅仅对数据所做的更改可用作实时分析中的参考数据。 此外，通过将 Azure Cosmos DB 数据连接到 HDInsight 进行 Pig、Hive 或 Map/Reduce 作业，可进一步优化和处理数据。  经过优化的数据随后被加载回 Azure Cosmos DB 用于报告。   

有关使用 Azure Cosmos DB、EventHubs 和 Storm 的 IoT 解决方案示例，请参阅 [GitHub 上的 hdinsight-storm-examples 存储库](https://github.com/hdinsight/hdinsight-storm-examples/)。

有关 Azure IoT 产品/服务的详细信息，请参阅 [创建物联网](https://www.microsoft.com/en-us/internet-of-things)。 

## <a name="retail-and-marketing"></a>零售和营销
Azure Cosmos DB 广泛用于 Microsoft 旗下运行 Windows 应用商店和 XBox Live 的电子商务平台。 它还在零售业中用于存储目录数据，以及在订单处理管道中用于事件寻源。

目录数据使用方案涉及对人员、地点和产品等实体的一些属性进行存储和查询。 目录数据的部分示例包括用户帐户、产品目录、IoT 设备注册表和物料系统清单。 此数据的属性可能会随时间的推移而发生变化和更改以满足应用程序要求。

以汽车部件供应商的产品目录为例。 除所有部件共有的常见属性外，每个部件可能都具有其自己的属性。 而且，某个特定部件的属性还可能会在发布新型号之后的第二年发生变化。 Azure Cosmos DB 支持灵活的架构和分层数据，因此非常适用于存储产品目录数据。

![Azure Cosmos DB 零售目录参考体系结构](./media/use-cases/product-catalog.png)

Azure Cosmos DB 通常用于事件寻源，可使用其[更改源](change-feed.md)功能为事件驱动的体系结构提供动力。 更改源使下游微服务能够可靠地以增量方式读取针对 Azure Cosmos DB 所做的插入和更新（例如订单事件）。 利用此功能可以提供一个持久性的事件存储作为状态不断变化的事件的消息中转站，并驱动许多微服务之间的订单处理工作流（可实现为[无服务器 Azure Functions](https://azure.com/serverless)）。

![Azure Cosmos DB 订单管道参考体系结构](./media/use-cases/event-sourcing.png)

此外，Azure Cosmos DB 中存储的数据可与 HDInsight 集成，通过 Apache Spark 作业进行大数据分析。 有关 Azure Cosmos DB 的 Spark 连接器的详细信息，请参阅[使用 Cosmos DB 和 HDInsight 运行 Spark 作业](spark-connector.md)。

## <a name="gaming"></a>游戏
数据库层是游戏应用程序的关键组件。 新式游戏可在移动/控制台客户端执行图形处理，但需依赖云传输自定义的个性化内容，例如游戏中的统计数据、社交媒体集成以及高分排行榜。 游戏通常需要单毫秒的读取和写入延迟，从而带来具有吸引力的游戏体验。 新游戏启动和功能更新期间，游戏数据库需要具备很快的速度，并且要能够处理请求速率中的大量峰值。

Azure Cosmos DB 用于 Next Games 推出的 [The Walking Dead:No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/)（行尸走肉：无人地带）和 [Halo 5:Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/)（光环 5：守护者）等游戏。 对于游戏开发人员，Azure Cosmos DB 具备以下优势：

* Azure Cosmos DB 允许弹性提高或降低性能。 此优势使游戏可以通过执行单个 API 调用同时处理从几十到数百万不等的游戏配置文件和统计数据的更新。
* Azure Cosmos DB 支持毫秒读取和写入，有助于避免在玩游戏期间出现任何延迟。
* Azure Cosmos DB 的自动索引可对多个不同属性进行实时筛选，例如通过内部玩家 ID 或其 GameCenter、Facebook、Google ID 或基于玩家公会成员身份的查询对玩家进行定位。 如果不构建复杂的索引或分区基础结构，就不可能实现这种操作。
* 采用灵活的架构，就更容易实现游戏内聊天消息、玩家公会成员身份、已完成的挑战、高分排行榜以及社交图谱等社交功能。
* Azure Cosmos DB 作为托管的平台即服务 (PaaS) 只需最少的设置和管理工作即可实现快速迭代并缩短上市时间。

![Azure Cosmos DB 游戏参考体系结构](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Web 和移动应用程序
Azure Cosmos DB 通常用于 Web 和移动应用程序内部，非常适用于社交互动建模、与第三方服务集成和积累丰富的个性化体验。 可以使用 Cosmos DB SDK 构建使用常用 [Xamarin 框架](mobile-apps-with-xamarin.md)的丰富 iOS 和 Android 应用程序。  

### <a name="social-applications"></a>社交应用程序
Azure Cosmos DB 的一个常见用例就是存储和查询 Web、移动和社交媒体应用程序的用户生成内容 (UGC)。 UGC 的部分示例包括聊天会话、推文、博客文章、评级和评论。 通常情况下，社交媒体应用程序中的 UGC 混合了不受刚性结构约束的自由形式文本、属性、标记和关系。 可将聊天、评论和帖子等内容存储在 Cosmos DB 中，无需转换或复杂对象关系映射层。  可以轻易添加或修改数据属性以满足开发人员遍历应用程序代码的要求，从而促进快速开发。  

与第三方社交网络集成的应用程序必须响应这些网络中不断更改的架构。 由于 Cosmos DB 中的数据默认自动编制索引，因此可以随时查询数据。 因此，这些应用程序可以根据其各自的需求灵活地检索投影。

许多社交应用程序在全球范围内运行并表现出不可预测的使用模式。 因此，当应用程序层能根据用量需求缩放时，缩放数据存储的灵活性至关重要。  可通过在 Cosmos DB 帐户之下添加额外的数据分区来进行扩大。  也可在多个区域中创建额外的 Cosmos DB 帐户。 有关 Cosmos DB 服务区域可使用性的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/#services)。

![Azure Cosmos DB Web 应用参考体系结构](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>个性化
如今，现代应用程序都具有复杂的视图和体验。 它们通常采用动态设计，以迎合对用户首选项或情绪应对和品牌塑造的需求。 因此，应用程序需要能够有效地检索个性化设置，以快速呈现 UI 元素和体验。 

JSON 是一种用于呈现 UI 布局数据的有效格式，它受 Cosmos DB 支持，不仅轻量而且可被 JavaScript 轻松理解。 Cosmos DB 提供可调的一致性级别，可以实现快速读取和低延迟写入。 因此，在 Cosmos DB 中将包括个性化设置的 UI 布局数据存储为 JSON 文档是获取网络数据的一种有效方法。

![Azure Cosmos DB Web 应用参考体系结构](./media/use-cases/personalization.png)

## <a name="next-steps"></a>后续步骤

* 可以根据[快速入门](create-sql-api-dotnet.md)开始使用 Azure Cosmos DB，其中逐步讲解了如何创建帐户并开始使用 Cosmos DB。

* 如果你想要了解有关使用 Azure Cosmos DB 的客户的详细信息，请参阅[客户案例研究](https://azure.microsoft.com/en-us/case-studies/?service=cosmos-db)页面。
