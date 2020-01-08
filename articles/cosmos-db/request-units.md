---
title: Azure Cosmos DB 中的请求单位和吞吐量
description: 了解如何指定和评估 Azure Cosmos DB 中的请求单位要求。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: a1143f912d894c1219de05b03a2338dc4e5bdc5f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467661"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB 中的请求单位数

使用 Azure Cosmos DB 时，需要支付预配的吞吐量和每小时消耗的存储的费用。 必须预配吞吐量才能确保始终为 Azure Cosmos 数据库提供足够的系统资源。 需要有足够的资源才能达到或超过 [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) 的要求。

Azure Cosmos DB 支持多种 API，例如 SQL、MongoDB、Cassandra、Gremlin 和表。 每个 API 具有自身的数据库操作集。 这些操作包括简单的点读取和写入，以及复杂的查询等等。 每个数据库操作根据其复杂性消耗系统资源。 

所有数据库操作的成本将由 Azure Cosmos DB 规范化，并以“请求单位”（缩写为 RU）表示。 可将每秒 RU 数视为吞吐量的货币。 每秒 RU 数是基于费率的货币。 它抽象化了执行 Azure Cosmos DB 支持的数据库操作所需的系统资源，例如 CPU、IOPS 和内存。 

读取 1 KB 项的成本为 1 个请求单位（1 个 RU）。 以类似方式为其他所有数据库操作分配 RU 成本。 不管使用哪个 API 来与 Azure Cosmos 容器和数据库操作交互，都始终以 RU 来计量成本。 无论数据库操作是写入、读取还是查询，都始终以 RU 来计量成本。

下图展示了 RU 的概要情况。

![数据库操作消耗请求单位](./media/request-units/request-units.png)

为了方便管理和规划容量，Azure Cosmos DB 可确保针对给定数据集执行的给定数据库操作的 RU 数是确定性的。 可以检查响应标头来跟踪任一数据库操作消耗的 RU 数。 了解[影响 RU 费用的因素](request-units.md#request-unit-considerations)以及应用程序吞吐量要求后，可以经济高效地运行应用程序。

可以按秒来预配应用程序的 RU 数，增量为每秒 100 RU。 若要缩放应用程序的预配吞吐量，随时可以增加或减少 RU 数。 可以以 100 RU 为增量或减量进行缩放。 可以编程方式或使用 Azure 门户进行更改。 你需要按小时付费。

可在两个不同的粒度级别预配吞吐量： 

* **容器**：有关详细信息，请参阅[对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* **数据库**：有关详细信息，请参阅[对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。

## <a name="request-unit-considerations"></a>请求单位注意事项

估算要预配的每秒 RU 数量时，请考虑以下因素：

* **项大小**：随着项的增大，读取或写入该项所要消耗的 RU 数也会增加。

* **项索引**：默认情况下会自动为每个项创建索引。 如果选择不为容器中的某些项创建索引，则消耗的 RU 数将会减少。

* **项属性计数**：假设所有属性采用默认索引，写入某个项所要消耗的 RU 数会随着项属性计数的增加而增加。

* **带索引的属性**：每个容器的索引策略都可确定默认情况下要进行索引的属性类别。 若要减少写入操作的 RU 消耗，请限制带索引的属性数目。

* **数据一致性**：在执行读取操作时，非常一致性和有限过期一致性级别消耗的 RU 数大约比其他宽松一致性级别要多两倍。

* **查询模式**：查询的复杂性会影响操作使用的 RU 数。 影响查询操作成本的因素： 
    
    - 查询结果数
    - 谓词数
    - 谓词性质
    - 用户定义的函数数目
    - 源数据的大小
    - 结果集的大小
    - 投影数

  Azure Cosmos DB 保证针对相同数据重复执行的相同查询所消耗的 RU 数相同。

* **脚本的使用**：与查询一样，存储过程和触发器也是根据所执行的操作的复杂性来消耗 RU。 开发应用程序时，请检查[请求费用标头](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query)，以更好地了解每个操作消耗的 RU 容量。

## <a name="next-steps"></a>后续步骤

* 详细了解如何[对 Azure Cosmos 容器和数据库预配吞吐量](set-throughput.md)。
* 详细了解[逻辑分区](partition-data.md)。
* 详细了解如何[全局缩放预配的吞吐量](scaling-throughput.md)。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
* 了解如何[查找操作所产生的请求单位费用](find-request-unit-charge.md)。
* 了解如何[在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)。
* 了解如何[优化 Azure Cosmos DB 中的读取和写入成本](optimize-cost-reads-writes.md)。
* 了解如何[优化 Azure Cosmos DB 中的查询成本](optimize-cost-queries.md)。
* 了解如何[使用指标监视吞吐量](use-metrics.md)。
