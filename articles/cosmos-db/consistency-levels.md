---
title: Azure Cosmos DB 中的一致性级别
description: Azure Cosmos DB 提供五种一致性级别来帮助在最终一致性、可用性和延迟之间做出取舍。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 395b7bc31377fd771549a399032bad9d951ec804
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384918"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Azure Cosmos DB 中的一致性级别

依赖于复制实现高可用性和/或低延迟的分布式数据库在读取一致性与可用性、延迟和吞吐量之间进行基本权衡。 大多数商用分布式数据库都要求开发人员在两种极端一致性模型之间进行选择：非常一致和最终一致。 可线性化或强一致性模型是数据可编程性的黄金标准。 但其导致的延迟代价较高（稳定状态下）且会降低可用性（遇到故障时）。 另一方面，最终一致性可提供更高的可用性和性能，但会加大应用程序的编程难度。 

Azure Cosmos DB 通过某种选择范围来实现数据一致性，而不会走两种极端。 尽管非常一致性和最终一致性处于该范围的两个极端，但在一致性的整个范围中，还有很多一致性选项。 开发人员可以使用这些选项在高可用性和性能方面做出精确的选择和细致的取舍。 

借助 Azure Cosmos DB，开发人员可以在一致性范围内从五个明确定义的一致性模型中进行选择。 从最强到最弱，这些模型包括“非常”、“有限过期”、“会话”、“一致前缀”和“最终”一致性。 这些模型具有明确的定义且非常直观，可用于特定的真实场景。 每个模型[在可用性与性能方面各有利弊](consistency-levels-tradeoffs.md)，并有 SLA 作为保障。 下图以范围区间形式显示了不同的一致性级别。

![范围形式的一致性](./media/consistency-levels/five-consistency-levels.png)

一致性级别与区域无关，无论在哪个区域为读取和写入操作提供服务、与 Azure Cosmos 帐户关联的区域数量是多少，或者帐户是配置了单个还是多个写入区域，都可以保证所有操作获得这种一致性。

## <a name="scope-of-the-read-consistency"></a>读取一致性的范围

读取一致性适用于分区键范围或逻辑分区内的单个读取操作。 读取操作可能由远程客户端或存储过程发出。

## <a name="configure-the-default-consistency-level"></a>配置默认一致性级别

随时都可在 Azure Cosmos DB 帐户中配置默认的一致性级别。 在帐户中配置的默认一致性级别适用于该帐户下的所有 Azure Cosmos 数据库和容器。 针对某个容器或数据库发出的所有读取和查询默认使用指定的一致性级别。 有关详细信息，请参阅如何[配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)。

## <a name="guarantees-associated-with-consistency-levels"></a>与一致性级别关联的保证

Azure Cosmos DB 提供的综合 SLA 可保证 100% 的读取请求满足所选任何一致性级别的一致性保证。 如果满足与一致性级别关联的所有一致性保证，则读取请求满足一致性 SLA。 [Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub 存储库中提供了使用 TLA + 规范语言 Azure Cosmos DB 中五个一致性级别的精确定义。

下面描述了五个一致性级别的语义：

- **非常一致性**：强一致性提供可线性化保证。 可线性化是指并发处理请求。 保证读取操作返回项的最新提交版本。 客户端永远不会看到未提交或不完整的写入。 始终保证用户读取最新确认的写入。

- **有限过期性**：保证读取操作遵循一致性前缀保证。 读取操作可以滞后于写入操作最多 *"K"* 个项版本（即“更新”）或 *"T"* 时间间隔。 换言之，如果选择有限过期，则可以通过两种方式配置“过期”： 

  * 项的版本数 (*K*)
  * 读取操作可以滞后于写入操作的时间间隔 (*T*) 

  有限过期提供全局整体顺序，但在“过期窗口”中除外。 过期窗口内部和外部的区域中提供单调读取保证。 非常一致性的语义与有限过期提供的语义相同。 过期窗口等于零。 有限过期也称为“延时可线性化”。 当客户端在接受写入的区域中执行读取操作时，有限过期一致性提供的保证与非常一致性的保证相同。

- **会话一致性**：在单客户端会话中, 保证接受一致前缀 (假设单个 "写入器" 会话), 单调读取、单调写入、读写和读/写。 执行写入操作的会话之外的客户端将看到最终一致性。

- 一致前缀：返回的更新包含所有更新的一些前缀，不带间隔。 一致前缀一致性级别保证读取操作永远不会看到无序写入。

- **最终一致性**：不保证读取的顺序。 如果缺少任何进一步的写入，则副本最终会收敛。

## <a name="consistency-levels-explained-through-baseball"></a>借助棒球解释一致性级别

让我们以棒球比赛场景为例。 想象一系列表示棒球比赛得分的写入。 在 [Replicated data consistency through baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf)（借助棒球阐释复制数据一致性）一文中描述了逐局得分。 这场虚构的棒球比赛目前正处于第七局的中段。 这是第七局的比赛。 客队以 2 比 5 的比分落后，如下所示：

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **本垒打次数** |
| - | - | - | - | - | - | - | - | - | - | - |
| **客队** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **主队** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Azure Cosmos 容器保存客队和主队的本垒打总次数。 当比赛正在进行时，不同的读取保证可能会导致客户端读取不同的分数。 下表列出了使用每种（共五种）一致性保证读取客队和主队分数后可能返回的完整分数集。 首先列出客队的得分。 不同的可能返回值以逗号分隔。

| **一致性级别** | **比分（客队、主队）** |
| - | - |
| **非常** | 2-5 |
| **有限过期** | 最多一个已过期的回合比分：2-3、2-4、2-5 |
| **会话** | <ul><li>写入者：2-5</li><li> 写入者以外的任何人：0-0、0-1、0-2、0-3、0-4、0-5、1-0、1-1、1-2、1-3、1-4、1-5、2-0、2-1、2-2、2-3、2-4、2-5</li><li>读取 1-3 后：1-3、1-4、1-5、2-3、2-4、2-5</li> |
| **一致前缀** | 0-0、0-1、1-1、1-2、1-3、2-3、2-4、2-5 |
| **最终** | 0-0、0-1、0-2、0-3、0-4、0-5、1-0、1-1、1-2、1-3、1-4、1-5、2-0、2-1、2-2、2-3、2-4、2-5 |

## <a name="additional-reading"></a>其他阅读材料

若要详细了解一致性的概念，请阅读以下文章：

- [Azure Cosmos DB 提供的五个一致性级别的高级 TLA+ 规范](https://github.com/Azure/azure-cosmos-tla)
- [Doug Terry 借助棒球阐释复制数据一致性（视频）](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Doug Terry 借助棒球阐释复制数据一致性（白皮书）](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [弱一致性重复数据的会话保证](https://dl.acm.org/citation.cfm?id=383631)
- [现代分布式数据库系统设计中的一致性利弊：CAP 只是冰山一角](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)（实用部分仲裁的概率有限过期性 (PBS)）
- [最终一致性 - 再探](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>后续步骤

要详细了解 Azure Cosmos DB 中的一致性级别，请阅读以下文章：

* [为你的应用程序选择适当的一致性级别](consistency-levels-choosing.md)
* [跨 Azure Cosmos DB API 的一致性级别](consistency-levels-across-apis.md)
* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [配置默认一致性级别](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [替代默认一致性级别](how-to-manage-consistency.md#override-the-default-consistency-level)

