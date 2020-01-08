---
title: 在 Azure Cosmos DB 中优化多区域部署的成本
description: 本文说明如何在 Azure Cosmos DB 中管理多区域部署的成本。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rimman
ms.openlocfilehash: 233eab1fc49d7ce4cbb1e5b98b67eda9a64aa195
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667598"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中优化多区域成本

可随时对 Azure Cosmos 帐户添加或删除区域。 在与帐户关联的每个区域中会保留为各种 Azure Cosmos 数据库和容器配置的吞吐量。 如果每小时预配的吞吐量（即，跨所有数据库和容器为 Azure Cosmos 帐户配置的 RU/秒总和）为 `T`，并且与数据库帐户关联的 Azure 区域数是 `N`，则对于给定小时，Cosmos 帐户的总预配吞吐量等于：

1. `T x N RU/s`，如果使用单个写入区域配置 Azure Cosmos 帐户。 

1. `T x (N+1) RU/s`，如果使用能够处理写入的所有区域配置 Azure Cosmos 帐户。 

具有单个写入区域的预配吞吐量每 100 RU/秒的成本为 0.008 美元/小时，具有多个可写区域的预配吞吐量每 100 RU/秒的成本为 0.016 美元/小时。 若要了解详细信息，请参阅 Azure Cosmos DB [定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)。

## <a name="costs-for-multiple-write-regions"></a>多个写入区域的成本

在多主数据库系统中，可用于写入操作的净 RU 会增加 `N` 倍，其中 `N` 是写入区域数。 与单区域写入不同，每个区域都可写，应支持冲突解决。 编写器的工作负载量会增加。 从成本规划的角度来看, 若要`M`在全球范围内执行 RU/秒的写入, 则需要在`RUs`容器或数据库级别预配 M。 随后可以添加任意多个区域并将它们用于写入以在全球范围执行 `M` RU 写入。 

### <a name="example"></a>示例

请考虑在美国西部有一个容器，该容器这个月预配的吞吐量为 10K RU/s，且存储了 1 TB 数据。 假定添加了三个区域 - 美国东部、北欧和东亚，每个区域的存储和吞吐量相同，并且希望能够通过自己的全球分布式应用对所有四个区域中的容器进行写入。 一个月的总月度帐单（假定为 31 天）如下所示：

|**Item**|**使用情况（每月）**|**费率**|**每月成本**|
|----|----|----|----|
|美国西部容器的吞吐量帐单（多个写入区域） |10K RU/秒 * 24 * 31 |每小时每 100 RU/s 为 0.016 美元 |1,190.40 美元 |
|3 个其他区域（美国东部、北欧和东亚）的吞吐量帐单（多个写入区域） |(3 + 1) * 10K RU/秒 * 24 * 31 |每小时每 100 RU/s 为 0.016 美元 |4,761.60 美元 |
|美国西部容器的存储帐单 |1 TB (或 1024 GB) |0\.25 美元/GB |$256 |
|3 个其他区域（美国东部、北欧和东亚）的存储帐单 |3 * 1 TB (或 3072 GB) |0\.25 美元/GB |$768 |
|**总计**|||**$6976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>按每个区域提高吞吐量利用率

如果使用率效率低下（例如，一个或多个区域利用率不足或过度利用），则可以执行以下步骤以提高吞吐量利用率：  

1. 请确保首先优化写入区域中的预配吞吐量 (RU)，然后通过使用来自读取区域等的更改源充分使用读取区域中的 RU。 

2. 多写入区域读取和写入可以跨与 Azure Cosmos 帐户关联的所有区域进行缩放。 

3. 监视区域中的活动，你可以按需添加和删除区域以缩放读取和写入吞吐量。

## <a name="next-steps"></a>后续步骤

接下来，可通过以下文章详细了解 Azure Cosmos DB 中的成本优化：

* 详细了解[开发和测试优化](optimize-dev-test.md)
* 详细了解[了解 Azure Cosmos DB 帐单](understand-your-bill.md)
* 详细了解如何[优化吞吐量成本](optimize-cost-throughput.md)
* 详细了解如何[优化存储成本](optimize-cost-storage.md)
* 详细了解如何[优化读取和写入成本](optimize-cost-reads-writes.md)
* 详细了解如何[优化查询成本](optimize-cost-queries.md)

