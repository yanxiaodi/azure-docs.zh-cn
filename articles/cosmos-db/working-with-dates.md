---
title: 使用 Azure Cosmos DB 中的日期
description: 了解如何使用 Azure Cosmos DB 中的日期。
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 9676642e96d437965fef041930b8223241cadeaa
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349024"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 中的日期
Azure Cosmos DB 通过本机 [JSON](https://www.json.org) 数据模型提供架构灵活性和丰富的索引。 所有 Azure Cosmos DB 资源（包括数据库、容器、文档和存储过程）均作为 JSON 文档进行建模和存储。 为使代码可移植，JSON（及 Azure Cosmos DB）仅支持一小组基本类型：字符串、数字、布尔值、数组、对象和 Null。 但是，JSON 具有相当的灵活性，允许开发人员和框架使用这些基元并将其编写为对象或数组，以便表示更复杂的类型。 

除了基本类型，许多应用程序还需要 DateTime 类型来表示日期和时间戳。 本文介绍开发人员可如何使用 .NET SDK 在 Azure Cosmos DB 中存储、检索和查询日期。

## <a name="storing-datetimes"></a>存储 DateTime

Azure Cosmos DB 支持 JSON 类型，如字符串、数字、布尔值、null、数组和对象。 它不直接支持日期时间类型。 目前，Azure Cosmos DB 不支持日期的本地化。 因此，需要将 Datetime 存储为字符串。 Azure Cosmos DB `YYYY-MM-DDThh:mm:ss.sssZ`中的日期时间字符串的建议格式遵循 ISO 8601 UTC 标准。 建议将 Azure Cosmos DB 中的所有日期存储为 UTC。 将日期字符串转换为此格式将允许排序日期按字典顺序。 如果存储非 UTC 日期，则必须在客户端处理逻辑。 若要将本地日期时间转换为 UTC，则偏移量必须已知/存储为 JSON 中的属性，客户端可以使用 offset 计算 UTC 日期时间值。

由于以下原因，大多数应用程序可以使用 DateTime 的默认字符串表示形式：

* 字符串可以进行比较，而 DateTime 值的相对顺序在这些值转换为字符串时得以保留。 
* 此方法不需要进行 JSON 转换所需的任何自定义代码或属性。
* 在 JSON 中存储的日期人工可读。
* 这种方法可以利用 Azure Cosmos DB 的索引执行快速查询。

例如，以下代码片段使用 .NET SDK 将 `Order` 对象存储为文档，该对象包含两个 DateTime 属性 - `ShipDate` 和 `OrderDate`：

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

本文档存储在 Azure Cosmos DB 中，如下所示：

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

也可将 DateTime 存储为 Unix 时间戳，即存储为数字，用于表示自 1970 年 1 月 1 日以来已过去的秒数。 Azure Cosmos DB 的内部时间戳 (`_ts`) 属性遵循这种方法。 可以使用 [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) 类将 DateTime 序列化为数字。 

## <a name="indexing-datetimes-for-range-queries"></a>设置 DateTime 的索引，以便进行范围查询
通常可以使用 DateTime 值进行范围查询。 例如，如果需要查找昨天以来创建的所有订单，或者查找过去五分钟内发运的所有订单，则需进行范围查询。 若要有效地执行这些查询，必须针对字符串的范围索引配置集合。

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

如需详细了解如何配置索引策略，可参阅 [Azure Cosmos DB 索引策略](index-policy.md)。

## <a name="querying-datetimes-in-linq"></a>在 LINQ 中查询 Datetime
SQL .NET SDK 自动支持通过 LINQ 查询存储在 Azure Cosmos DB 中的数据。 例如，下面的代码片段显示一个 LINQ 查询，该查询筛选在过去三天中发运的订单。

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

可在[查询 Cosmos DB](how-to-sql-query.md) 中详细了解 Azure Cosmos DB 的 SQL 查询语言和 LINQ 提供程序。

本文探讨了如何在 Azure Cosmos DB 中存储、索引和查询 DateTime。

## <a name="next-steps"></a>后续步骤
* 下载并运行 [GitHub 上的代码示例](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* 了解 [SQL 查询](how-to-sql-query.md)的详细信息
* 深入了解 [Azure Cosmos DB 索引策略](index-policy.md)
