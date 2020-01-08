---
title: 复制表的设计指南 - Azure SQL 数据仓库 | Microsoft Docs
description: 在 Azure SQL 数据仓库架构中设计复制表的建议。 
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: c622edc6c3a37b2bc71323cf0e2c155f7aec6e33
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479314"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>在 Azure SQL 数据仓库中使用复制表的设计指南
本文提供在 SQL 数据仓库架构中设计复制表的建议。 使用这些建议，可减少数据移动并降低查询复杂性，从而提高查询性能。

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>先决条件
本文假设读者熟悉 SQL 数据仓库中的数据分布和数据移动概念。  有关详细信息，请参阅[体系结构](massively-parallel-processing-mpp-architecture.md)一文。 

在设计表的过程中，尽可能多地了解数据以及数据查询方式。  例如，考虑以下问题：

- 表有多大？   
- 表的刷新频率是多少？   
- 数据仓库中有事实数据表和维度表吗？   

## <a name="what-is-a-replicated-table"></a>什么是复制表？
复制表是表的完整副本，可在每个计算节点上进行访问。 复制表以后，无需在执行联接或聚合前在计算节点中间传输数据。 因为表有多个副本，因此在压缩的表大小小于 2 GB 时，复制表效果最佳。  2 GB 不是硬性限制。  如果数据为静态数据，不会更改，则可复制更大的表。

下图显示了一个可在任意计算节点上访问的复制表。 在SQL 数据仓库中，复制表完整复制到每个计算节点上的分发数据库。 

![复制表](media/guidance-for-using-replicated-tables/replicated-table.png "复制表")  

复制的表比较适合星型架构中的维度表。 维度表通常联接到事实数据表，后者的分发不同于维度表。  通常情况下，维度的大小让存储并维护多个副本变得可行。 维度用于存储变化缓慢的描述性数据，例如客户名称和地址以及产品详细信息。 该数据的缓变本性使复制的表不会经历太多的维护。 

在以下情况下，考虑使用复制表：

- 磁盘上的表小于 2 GB，无论行数。 若要确定表大小，可以使用 [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) 命令：`DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`。 
- 在联接中使用表，否则需要数据移动。 连接未分布在同一列上的表（如将哈希分布式表连接到轮循机制表）时，需要进行数据移动才能完成此查询。  如果其中一个表较小，请考虑使用复制表。 在大多数情况下，我们建议使用复制表，而不是轮循表。 要查看查询计划中的数据移动操作，请使用 [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)。  BroadcastMoveOperation 是典型的数据移动操作，可通过使用复制的表来消除。  
 
在以下情况下，复制表可能无法实现最好的查询性能：

- 对表进行频繁的插入、更新和删除操作。 这些数据操作语言 (DML) 操作需要重新生成复制表。 经常重新生成可能会降低性能。
- 数据仓库缩放频繁。 缩放数据仓库会更改计算节点的数目, 这会导致重新生成复制的表。
- 表中包含大量列，但数据操作通常只访问少量列。 在这种情况下，与复制整个表相比，将表分发，然后对经常访问的列创建索引可能更为高效。 当查询需要进行数据移动时，SQL 数据仓库仅移动所请求列中的数据。 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>对简单查询谓词使用复制表
在选择分布或复制表之前，请先考虑计划对表运行的查询类型。 只要有可能，

- 对包含简单查询谓词（例如等式或不等式）的查询使用复制表。
- 对包含复杂查询谓词（例如 LIKE 或 NOT LIKE）的查询使用分布式表。

在工作分布在所有计算节点上时，CPU 密集型查询的效果最好。 例如，对于在表的每一行上运行计算的查询，针对分布式表进行查询比针对复制表进行查询效果更好。 由于复制表完整存储在每个计算节点上，因此对复制表的 CPU 密集型查询会针对每个计算节点上的整个表运行。 额外的计算可能会降低查询性能。

例如，此查询包含一个复制谓词。  当数据位于分布式表而非复制的表中时，它运行更快。 在此示例中，数据可以是循环分布式的。

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>将现有轮循表转换为复制表
如果已经具有循环表，如果它们满足本文中列出的条件，建议将其转换为复制的表。 复制表提高了轮循表的性能，因为前者不需要移动数据。  循环表的联接总是需要移动数据。 

下面的示例使用 [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 将 DimSalesTerritory 表更改为复制表。 无论 DimSalesTerritory 是哈希分布表还是轮询表，此示例都适用。

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>轮循表与复制表的查询性能示例 

复制表的联接不需要移动数据，因为整个表都存在于每个计算节点上。 如果维度表是轮循分布表，则联接会将维度表完整复制到每个计算节点上。 为了移动数据，查询计划包含一个名为 BroadcastMoveOperation 的操作。 此类数据移动操作会降低查询性能，可通过使用复制表避免此问题。 要查看查询计划步骤，请使用 [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) 系统目录视图。 

例如，在针对 AdventureWorks 架构的以下查询中，`FactInternetSales` 表是哈希分布表。 `DimDate` 和 `DimSalesTerritory` 表是较小的维度表。 此查询将返回 2004 财年北美地区的销售总额：

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
将 `DimDate` 和 `DimSalesTerritory` 重新创建为轮循表。 结果，查询显示了以下查询计划，其中包含多个广播移动操作： 
 
![轮循查询计划](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

将 `DimDate` 和 `DimSalesTerritory` 重新创建为复制表，然后重新运行查询。 生成的查询计划要短很多，而且不移动任何广播。

![复制查询计划](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>修改复制表的性能注意事项
SQL 数据仓库通过维护表的主版本来实现复制表。 它将主版本复制到每个计算节点上的某个分发数据库。 发生更改时，SQL 数据仓库会首先更新主表。 然后需要在每个计算节点上重新生成表。 重新生成复制表包括将表复制到每个计算节点，然后生成索引。  例如，DW400 上的复制表有 5 份数据副本。  每个计算节点上均存在主控副本和完整副本。  所有数据均存储在分发数据库中。 SQL 数据仓库使用此模型来支持更快的数据修改语句和灵活的缩放操作。 

执行以下操作后需要重新生成：
- 加载或修改数据
- 数据仓库缩放为不同级别
- 更新表定义

执行以下操作后不需要重新生成：
- 暂停操作
- 恢复操作

修改数据后不会立即重新生成。 而是在查询首次从表中进行选择时触发重新生成。  触发重新生成的查询将立即从表的主版本读取，同时将数据异步复制到每个计算节点。 在数据完成复制之前，后续查询将继续使用表的主版本。  如果强制执行其他重新生成操作的复制表发生任何活动，则数据复制将失效，并且下一个 select 语句将触发再次复制数据操作。 

### <a name="use-indexes-conservatively"></a>谨慎使用索引
标准索引适用于复制表。 SQL 数据仓库在重新生成的过程中重新生成每个复制表索引。 仅当重新生成索引带来的好处超出了相关成本时，才使用索引。  
 
### <a name="batch-data-loads"></a>批处理数据加载
将数据加载到复制表中时，请尝试通过批量加载最大限度减少重新生成。 在运行 Select 语句之前执行所有批量加载。

例如，此负载模式从 4 个源加载数据并调用 4 个重新生成。 

- 从源 1 进行加载。
- Select 语句触发重新生成 1。
- 从源 2 加载。
- Select 语句触发重新生成 2。
- 从源 3 加载。
- Select 语句触发重新生成 3。
- 从源 4 加载。
- Select 语句触发重新生成 4。

例如，此负载模式从 4 个源加载数据，但仅调用 1 个重新生成。

- 从源 1 加载。
- 从源 2 加载。
- 从源 3 加载。
- 从源 4 加载。
- Select 语句触发重新生成。


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>批量加载后重新生成复制表
为了确保查询执行时间一致，建议在批量加载后强制生成复制表。 否则，第一个查询仍将使用数据移动来完成查询。 

此查询使用 [sys.pdw_replicated_table_cache_state ](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV列出已修改但未重新生成的复制表。

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
若要触发重新生成，请在上一个输出中的每个表上运行以下语句。 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>后续步骤 
若要创建复制表，请使用以下语句之一：

- [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)（创建表（Azure SQL 数据仓库））
- [CREATE TABLE AS SELECT（Azure SQL 数据仓库）](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

有关分布式表的概述，请参阅[分布式表](sql-data-warehouse-tables-distribute.md)。



