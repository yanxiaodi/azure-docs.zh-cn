---
title: 利用 Azure SQL 数据仓库按序聚集列存储索引进行性能优化 |Microsoft Docs
description: 使用有序聚集列存储索引以提高查询性能时应了解的建议和注意事项。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 74a1a2218020718a05c9d01de96ddf4fccb35eb4
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802568"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>具有有序聚集列存储索引的性能优化  

当用户查询 Azure SQL 数据仓库中的列存储表时，优化器会检查每个段中存储的最小值和最大值。  超出查询谓词范围的段不会从磁盘读取到内存。  如果要读取的段数和总大小较小，查询可以获得更快的性能。   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>已排序和未排序的聚集列存储索引 
默认情况下，对于不使用索引选项创建的每个 Azure 数据仓库表，内部组件（索引生成器）将在其上创建未排序的聚集列存储索引（CCI）。  每列中的数据将压缩为单独的 CCI 行组段。  对于每个段的值范围，都有元数据，因此，在执行查询期间，不会从磁盘中读取超出查询谓词范围的段。  CCI 提供最高级别的数据压缩，并减小段的大小以进行读取，以便查询可以更快地运行。 但是，因为在将数据压缩为段之前，索引生成器不会对数据进行排序，所以，可能会出现具有重叠值范围的段，从而导致查询读取磁盘中的更多段并花费更长时间才能完成。  

创建按序的 CCI 时，Azure SQL 数据仓库引擎会按顺序键对内存中的数据进行排序，然后索引生成器将其压缩到索引段。  使用已排序的数据时，段重叠会减少，使查询更有效地消除段，从而提高性能，因为要从磁盘读取的段数越小。  如果所有数据都可以在内存中同时进行排序，则可以避免进行分段重叠。  由于数据仓库表中的数据大小较大，因此不经常发生这种情况。  

若要检查列的段范围，请使用表名称和列名称运行此命令：

```sql
SELECT o.name, pnp.index_id, cls.row_count, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table_Name>' and cols.name = '<Column_Name>' 
ORDER BY o.name, pnp.distribution_id, cls.min_data_id

```

## <a name="data-loading-performance"></a>数据加载性能

将数据加载到有序的 CCI 表中的性能类似于将数据加载到已分区表中。  
由于数据排序，将数据加载到有序的 CCI 表可能需要更多的时间，而不是将数据加载到无序的 CCI 表中。  

下面是将数据加载到具有不同架构的表中的性能比较示例。
![Performance_comparison_data_loading @ no__t-1
 
## <a name="reduce-segment-overlapping"></a>减少分段重叠
下面是通过 CTAS 或使用数据的现有表在新表中创建有序的 CCI 时，进一步减少分段重叠的选项：

- 使用更大的资源类可在索引生成器将更多数据压缩为段之前，允许在内存中同时对这些数据进行排序。  在索引段中，无法更改数据的物理位置。  段或段内没有数据排序。  

- 使用较低的并行度（例如 DOP = 1）。  用于按序的 CCI 创建的每个线程都在数据子集上运行，并在本地对其进行排序。  不同线程排序的数据没有全局排序。  使用并行线程可以减少创建有序的 CCI 的时间，但会生成比使用单个线程更多的重叠段。 
- 在将数据加载到 Azure SQL 数据仓库表之前，按排序关键字预先对数据进行排序。

## <a name="create-ordered-cci-on-large-tables"></a>对大型表创建按序的 CCI
创建按序的 CCI 是一种脱机操作。  对于没有分区的表，用户在按序的 CCI 创建过程完成之前，不能访问数据。   对于已分区表，因为引擎按分区创建按序排序的 CCI 分区，所以用户仍可以访问未处理顺序的 CCI 的分区中的数据。   可以使用此选项最大限度地减少在大表上排序的 CCI 创建过程中的停机时间： 

1.  在目标大型表（称为表 A）上创建分区。
2.  创建具有与表 A 相同的表和分区架构的空顺序 CCI 表（称为表 B）。
3.  将一个分区从表 A 切换到表 B。
4.  在表 B 上运行 ALTER INDEX < Ordered_CCI_Index > REBUILD 来重建已切换的分区。  
5.  为表 A 中的每个分区重复步骤3和4。
6.  将所有分区从表 A 切换到表 B 并重新生成后，删除表 A 并将表 B 重命名为表 A。 

## <a name="examples"></a>示例

**A.检查有序列和顺序序号：**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B.若要更改列序号，请在订单列表中添加或删除列，或更改为按序的 CCI：**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>后续步骤
有关更多开发技巧，请参阅 [SQL 数据仓库开发概述](sql-data-warehouse-overview-develop.md)。
