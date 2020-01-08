---
title: 为 Azure SQL 数据仓库中的表编制索引 | Microsoft Azure
description: 在 Azure SQL 数据仓库中为表编制索引的建议和示例。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 4d51bd6906a8299a25fe50ca817b1a2b6082ab91
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479838"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>为 SQL 数据仓库中的表编制索引

在 Azure SQL 数据仓库中为表编制索引的建议和示例。

## <a name="index-types"></a>索引类型

SQL 数据仓库提供多种索引选项，包括[聚集列存储索引](/sql/relational-databases/indexes/columnstore-indexes-overview)、[聚集索引和非聚集索引](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described)，以及一个称作[堆](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes)的非索引选项。  

若要创建带有索引的表，请参阅 [CREATE TABLE（Azure SQL 数据仓库）](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)文档。

## <a name="clustered-columnstore-indexes"></a>聚集列存储索引

默认情况下，如果未在表中指定任何索引选项，则 SQL 数据仓库将创建聚集列存储索引。 聚集列存储表提供最高级别的数据压缩，以及最好的总体查询性能。  一般而言，聚集列存储表优于聚集索引或堆表，并且通常是大型表的最佳选择。  出于这些原因，在不确定如何编制表索引时，聚集列存储是最佳起点。  

若要创建聚集列存储表，只需在 WITH 子句中指定 CLUSTERED COLUMNSTORE INDEX，或省略 WITH 子句：

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

在某些情况下，聚集列存储可能不是很好的选择：

- 列存储表不支持 varchar(max)、nvarchar(max) 和 varbinary(max)。 可以考虑使用堆或聚集索引。
- 对瞬态数据使用列存储表可能会降低效率。 可以考虑使用堆，甚至临时表。
- 包含少于 6000 万行的小型表。 可以考虑使用堆表。

## <a name="heap-tables"></a>堆表

将数据暂时移入 SQL 数据仓库时，可能会发现使用堆表可让整个过程更快速。 这是因为堆的加载速度比索引表还要快，在某些情况下，可以从缓存执行后续读取。  如果加载数据只是在做运行更多转换之前的预备，将表载入堆表会远快于将数据载入聚集列存储表。 此外，将数据载入[临时表](sql-data-warehouse-tables-temporary.md)也比将表载入永久存储更快速。  

对于包含少于 6000 万行的小型查找表，堆表通常比较适合。  超过 6000 万行后，聚集列存储表开始达到最佳压缩性能。

若要创建堆表，只需在 WITH 子句中指定 HEAP：

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>聚集与非聚集索引

需要快速检索单个行时，聚集索引可能优于聚集列存储表。 对于需要单个或极少数行查找才能极速执行的查询，请考虑使用聚集索引或非聚集辅助索引。 使用聚集索引的缺点是只有在聚集索引列上使用高度可选筛选器的查询才可受益。 要改善其他列中的筛选器，可将非聚集索引添加到其他列。 但是，添加到表中的每个索引会增大空间和加载处理时间。

若要创建聚集索引表，只需在 WITH 子句中指定 CLUSTERED INDEX：

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

若要对表添加非聚集索引，请使用以下语法：

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>优化聚集列存储索引

聚集列存储表将数据组织成多个段。  拥有较高的段质量是在列存储表中实现最佳查询性能的关键。  压缩行组中的行数可以测量分段质量。  每个压缩的行组至少有 10 万行时的段质量最佳，而随着每个行组的行数趋于 1,048,576 行（这是行组可以包含的最大行数），性能会随之提升。

可以在系统上创建并使用以下视图来计算每个行组的平均行数，以及识别所有欠佳的聚集列存储索引。  此视图中的最后一列将生成为 SQL 语句，以用于重建索引。

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

现在已创建视图，请运行此查询来识别哪些表的行组中包含的行少于 10 万个。 当然，如果要寻求更理想的段质量，可以将 10 万这个阈值增大。

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

运行查询后就可以开始查看数据并分析结果。 下表解释了要在行组分析中查看的内容。

| 柱形图 | 如何使用此数据 |
| --- | --- |
| [table_partition_count] |如果表被分区，那么你可能希望看到更高的打开的行组的计数。 在理论上分布式架构中每个分区具有一个与之相关联的打开的行组。 请在分析中考虑这一点。 可以通过完全删除分区来优化已分区的小型表，因为这样可以改进压缩。 |
| [row_count_total] |表的行总数。 例如，可以使用此值来计算处于压缩状态的行的百分比。 |
| [row_count_per_distribution_MAX] |如果所有行均匀分布，那么此值为每个分布式架构的目标行数。 将此值与 compressed_rowgroup_count 进行比较。 |
| [COMPRESSED_rowgroup_rows] |表的列存储格式的行的总数。 |
| [COMPRESSED_rowgroup_rows_AVG] |如果平均行数明显小于行组的最大行数，则可以考虑使用 CTAS 或 ALTER INDEX REBUILD 重新压缩数据 |
| [COMPRESSED_rowgroup_count] |列存储格式的行组数。 如果该值相对于表的大小显得非常高，那么它表示列存储密度低。 |
| [COMPRESSED_rowgroup_rows_DELETED] |逻辑上被删除的列存储格式的行数。 如果该数值相对于表的大小来说是高的，请考虑重新创建分区或重新生成索引，因为这以物理方式删除它们。 |
| [COMPRESSED_rowgroup_rows_MIN] |将该值与 AVG 和 MAX 列一起使用，以便了解列存储格式的行组的值的范围。 如果该值稍微高出加载阈值（每分区对齐的分布区 102,400 行），则提示可在数据加载中进行优化 |
| [COMPRESSED_rowgroup_rows_MAX] |同上 |
| [OPEN_rowgroup_count] |打开的行组都是正常的。 理论上，每个表分布区 (60) 预期有一个 OPEN 状态的行组。 如果该值过大，则提示需要跨分区进行数据加载。 仔细检查分区策略，以确保该值合理。 |
| [OPEN_rowgroup_rows] |每个行组最多可以具有 1,048,576 行。 使用此值可以了解当前打开的行组的填满程度。 |
| [OPEN_rowgroup_rows_MIN] |打开的组表示数据正在以点滴方式加载到表，或者之前的加载超出了剩余行，溢出到此行组。 使用 MIN、MAX、AVG 列来查看 OPEN 行组中有多少数据。 对于小型表，此数据量可以为所有数据的 100%！ 在此情况下使用 ALTER INDEX REBUILD 语句将数据强制转换为列存储格式。 |
| [OPEN_rowgroup_rows_MAX] |同上 |
| [OPEN_rowgroup_rows_AVG] |同上 |
| [CLOSED_rowgroup_rows] |查看已关闭行组的行以进行健全性检查。 |
| [CLOSED_rowgroup_count] |如果没有看到几个行组，则已关闭的行组数应该较低。 可以将已关闭的行组转换为压缩行组，方法是使用 ALTER INDEX ...REORGANIZE 命令。 但是，通常并不需要使用此命令。 后台的“tuple mover”进程会自动将关闭的组转换为列存储行组。 |
| [CLOSED_rowgroup_rows_MIN] |已关闭的行组应具有非常高的填充率。 如果已关闭的行组的填充率较低，则需要进一步分析列存储。 |
| [CLOSED_rowgroup_rows_MAX] |同上 |
| [CLOSED_rowgroup_rows_AVG] |同上 |
| [Rebuild_Index_SQL] |用于重建表的列存储索引的 SQL |

## <a name="causes-of-poor-columnstore-index-quality"></a>列存储索引质量不佳的原因

如果已识别出段质量不佳的表，接下来可以找出根本原因。  下面是段质量不佳的其他一些常见原因：

1. 生成索引时内存有压力
2. 有大量的 DML 操作
3. 小型或渗透负载操作
4. 过多的分区

这些因素可能导致列存储索引在每个行组中的行远远少于最佳数量（100 万）。 它们还会造成行转到增量行组而不是压缩的行组。

### <a name="memory-pressure-when-index-was-built"></a>生成索引时内存有压力

每个压缩行组的行数，与行宽度以及可用于处理行组的内存量直接相关。  当行在内存不足的状态下写入列存储表时，列存储分段质量可能降低。  因此，最佳做法是尽可能让写入到列存储索引表的会话访问最多的内存。  因为内存与并发性之间有所取舍，正确的内存分配指导原则取决于表的每个行中的数据、已分配给系统的数据仓库，以及可以提供给将数据写入表的会话的并发访问槽位数。

### <a name="high-volume-of-dml-operations"></a>有大量的 DML 操作

更新和删除行的大量 DML 操作可能造成列存储低效。 当行组中的大多数行已修改时尤其如此。

- 从压缩的行组中删除某行只会以逻辑方式将该行标记为已删除。 该行仍会保留在压缩的行组中，直到重新生成分区或表为止。
- 插入某行会将该行添加到名为增量行组的内部行存储表中。 在增量行组已满且标记为已关闭之前，插入的行不会转换成列存储。 达到 1,048,576 个行的容量上限后，行组会关闭。
- 更新采用列存储格式的行将依次作为逻辑删除和插入进行处理。 插入的行可以存储在增量存储中。

超出每个分区对齐分布区的 102,400 行批量阈值的批次更新和插入操作将直接写入列存储格式。 但是，假设在平均分布的情况下，需要在单个操作中修改超过 6.144 百万行才发生这种情况。 如果给定分区对齐分布区的行数小于 102400, 则这些行将转为到增量存储, 并在插入或修改足够的行以关闭行组或重新生成索引之前停留在该处。

### <a name="small-or-trickle-load-operations"></a>小型或渗透负载操作

流入 SQL 数据仓库的小型负载有时也称为渗透负载。 它们通常代表系统引入的数据的接近恒定流。 但是，由于此流接近连续状态，因此行的容量并不特别大。 通常数据远低于直接加载到列存储格式所需的阈值。

在这些情况下，最好先将数据保存到 Azure Blob 存储中，并让它在加载之前累积。 此技术通常称为*微批处理*。

### <a name="too-many-partitions"></a>过多的分区

另一个考虑因素是分区对聚集列存储表的影响。  分区之前，SQL 数据仓库已将数据分散到 60 个数据库。  进一步分区会分割数据。  如果将分区，则要考虑的是**每个**分区必须至少有 100 万行，使用聚集列存储索引才有益。  如果将表分区为100个分区, 则表需要至少6000000000行才能受益于聚集列存储索引 (60 分布*100 分区*1000000 行)。 如果包含 100 个分区的表没有 60 亿行，请减少分区数目，或考虑改用堆表。

在表中加载一些数据后，请遵循以下步骤来识别并重建聚集列存储索引质量欠佳的表。

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>重建索引以提升段质量

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>步骤 1：识别或创建使用适当资源类的用户

立即提升段质量的快速方法是重建索引。  上述视图返回的 SQL 将返回可用于重建索引的 ALTER INDEX REBUILD 语句。 重建索引时，请确保将足够的内存分配给要重建索引的会话。  为此，请提高用户的资源类，该用户有权将此表中的索引重建为建议的最小值。

以下示例演示如何通过提高资源类向用户分配更多内存。 若要使用资源类，请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)。

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>步骤 2：使用更高的用户资源类重建聚集列存储索引

以步骤1中的用户身份登录 (例如 LoadUser), 该用户现在使用更高的资源类, 并执行 ALTER INDEX 语句。 请确保此用户对重建索引的表拥有 ALTER 权限。 这些示例演示如何重新生成整个列存储索引或如何重建单个分区。 对于大型表，一次重建一个分区的索引比较合适。

或者，可以[使用 CTAS](sql-data-warehouse-develop-ctas.md) 将表复制到新表，而不要重建索引。 哪种方法最合适？ 如果数据量很大，CTAS 的速度通常比 [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql) 要快。 对于少量的数据，ALTER INDEX 更容易使用，不需要换出表。

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

在 SQL 数据仓库中重建索引是一项脱机操作。  有关重建索引的详细信息，请参阅[列存储索引碎片整理](/sql/relational-databases/indexes/columnstore-indexes-defragmentation)中的“ALTER INDEX REBUILD”部分和 [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql)。

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>步骤 3：验证聚集列存储段质量是否已改善

重新运行识别出段质量不佳的表的查询，并验证段质量是否已改善。  如果段质量并未改善，原因可能是表中的行太宽。  请考虑在重建索引时使用较高的资源类或 DWU。

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>使用 CTAS 和分区切换重建索引

此示例使用 [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 语句和分区切换重建表分区。

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

有关使用 CTAS 重新创建分区的更多详细信息，请参阅[在 SQL 数据仓库中使用分区](sql-data-warehouse-tables-partition.md)。

## <a name="next-steps"></a>后续步骤

有关开发表的详细信息，请参阅[开发表](sql-data-warehouse-tables-overview.md)。
