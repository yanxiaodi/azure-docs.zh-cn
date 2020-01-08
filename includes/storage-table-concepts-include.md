---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 042aedf1a043cd89d74ff099554642d38a3c7dd3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172697"
---
## <a name="what-is-table-storage"></a>什么是表存储
Azure 表存储可存储大量结构化数据。 该服务是一个 NoSQL 数据存储，接受来自 Azure 云内部和外部的通过验证的呼叫。 Azure 表最适合存储结构化非关系型数据。 表存储的常见用途包括：

* 存储 TB 量级的结构化数据，能够为 Web 规模应用程序提供服务
* 存储无需复杂联接、外键或存储过程，并且可以对其进行非规范化以实现快速访问的数据集
* 使用聚集索引快速查询数据
* 使用 OData 协议和 LINQ 查询以及 WCF 数据服务 .NET 库访问数据

可以使用表存储来存储和查询大型结构化非关系型数据集，并且表会随着需求的增加而扩展。

## <a name="table-storage-concepts"></a>表存储概念
表存储包含以下组件：

![表存储组件图][Table1]

* **URL 格式：** Azure 表存储帐户使用此格式：`http://<storage account>.table.core.windows.net/<table>`

  Azure Cosmos DB 表 API 帐户使用此格式：`http://<storage account>.table.cosmosdb.azure.com/<table>`  

  可以直接使用此地址和 OData 协议来访问 Azure 表。 有关详细信息，请参阅 [OData.org][OData.org]。
* **帐户：** 对 Azure 存储进行的所有访问都要通过存储帐户完成。 有关存储帐户容量的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](../articles/storage/common/storage-scalability-targets.md) 。 

    对 Azure Cosmos DB 进行的所有访问都要通过表 API 帐户完成。 有关创建表 API 帐户的详细信息，请参阅[创建表 API 帐户](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account)。
* **Table**：表是实体的集合。 表不对实体强制实施架构，这意味着单个表可以包含具有不同属性集的实体。  
* **实体**：与数据库行类似，一个实体就是一组属性。 Azure 存储中的实体大小最大可以为 1MB。 Azure Cosmos DB 中的实体大小最大可以为 2MB。
* **属性**：属性是名称/值对。 每个实体最多可包含 252 个用于存储数据的属性。 每个实体还具有三个系统属性，分别指定分区键、行键和时间戳。 对具有相同分区键的实体的查询速度将更快，并且可以在原子操作中插入/更新这些实体。 一个实体的行键是它在一个分区内的唯一标识符。

有关命名表和属性的详细信息，请参阅 [了解表服务数据模型](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)。

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
