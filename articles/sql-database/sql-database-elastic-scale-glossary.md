---
title: 弹性数据库工具词汇表 | Microsoft 文档
description: 弹性数据库工具所用术语的解释
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 3ed0cc9dce312cb9736b3e32ba46d2cb1cca3ef8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568451"
---
# <a name="elastic-database-tools-glossary"></a>弹性数据库工具词汇表

Azure SQL 数据库中的[弹性数据库工具](sql-database-elastic-scale-introduction.md)功能的术语定义如下。 这些工具用于管理[分片映射](sql-database-elastic-scale-shard-map-management.md)，包括[客户端库](sql-database-elastic-database-client-library.md)、[拆分/合并工具](sql-database-elastic-scale-overview-split-and-merge.md)、[弹性池](sql-database-elastic-pool.md)和[查询](sql-database-elastic-query-overview.md)。 

[使用弹性数据库工具添加分片](sql-database-elastic-scale-add-a-shard.md)和[使用 RecoveryManager 类解决分片映射问题](sql-database-elastic-database-recovery-manager.md)中用到了这些术语。

![弹性缩放术语][1]

**数据库**：Azure SQL 数据库。 

**依赖于数据的路由**：使应用程序能够连接到给定了特定分片键的分片的功能。 请参阅[依赖于数据的路由](sql-database-elastic-scale-data-dependent-routing.md)。 与 **[多分片查询](sql-database-elastic-scale-multishard-querying.md)** 进行比较。

**全局分片映射**：分片集内分片键及其各自分片之间的映射。 全局分片映射存储在**分片映射管理器**中。 与**局部分片映射**进行比较。

**列表分片映射**：在其中单独映射分片键的分片映射。 与**范围分片映射**进行比较。   

**局部分片映射**：局部分片映射（存储在分片上）包含驻留在该分片上的 shardlet 的映射。

**多分片查询**：能够针对多个分片发出查询；使用 UNION ALL 语义（也称为“扇出查询”）返回结果集。 与**依赖于数据的路由**进行比较。

**多租户**和**单租户**：下面显示的是单租户数据库和多租户数据库：

![单租户数据库和多租户数据库](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

下面显示的是**分片**单租户数据库和多租户数据库。 

![单租户数据库和多租户数据库](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**范围分片映射**：分片分发策略在其中基于多个连续值范围的分片映射。 

**引用表**：未进行分片，但在分片间进行复制的表。 例如，可以在引用表中存储邮政编码。 

**分片**：用于存储分片数据集中的数据的 Azure SQL 数据库。 

**分片弹性**：执行横向缩放和纵向缩放的能力。

**分片表**：已进行分片的表，即在基于其分片键值的分片中分发其数据。 

**分片键**：确定如何在分片上分发数据的列值。 值类型可以是下列其中一项：**int**、**bigint**、**varbinary** 或 **uniqueidentifier**。 

**分片集**：属于分片映射管理器中相同分片映射的分片集合。  

**Shardlet**：与分片上分片键的单个值相关联的所有数据。 当重新分发分片表时，shardlet 是可能的数据移动的最小单元。 

**分片映射**：分片键及其各自分片之间的映射集。

**分片映射管理器**：包含分片映射、分片位置和一个或多个分片集的映射的管理对象和数据存储。

![映射][2]

## <a name="verbs"></a>动词
**横向缩放**：通过将分片添加到分片映射或删除分片，向外（或向内）扩展分片集合的行为。

![横向缩放与纵向缩放][3]

**合并**：将 shardlet 从两个分片移动到一个分片，并且相应地更新分片映射的行为。

**Shardlet 移动**：将单个 shardlet 移动到不同分片的行为。 

**分片**：基于分片键对多个数据库上结构相同的数据进行水平分区的行为。

**拆分**：将几个 shardlet 从一个分片移动到另一个（通常是新的）分片的行为。 由用户提供的作为拆分点的分片键。

**纵向缩放**：向上（或向下）缩放单个分片的计算大小的行为。 例如，将分片从标准版更改为高级版（这会导致需要更多的计算资源）。 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

