---
title: Azure SQL 数据库弹性查询概述 | Microsoft Docs
description: 使用弹性查询可运行跨多个数据库的 Transact-SQL 查询。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 07/01/2019
ms.openlocfilehash: 313e8af0e42f5108a22261a475b5340208adb7bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568559"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Azure SQL 数据库弹性查询概述（预览版）

使用弹性查询功能（处于预览状态）可以跨 Azure SQL 数据库中的多个数据库运行 Transact-SQL 查询。 它允许执行跨数据库查询以访问远程表，以及连接 Microsoft 和第三方工具（Excel、Power BI、Tableau 等）以跨多个数据库的数据层进行查询。 使用此功能，可以将查询扩大到 SQL 数据库中的较大数据层，并直观显示商业智能 (BI) 报表中的结果。

## <a name="why-use-elastic-queries"></a>为什么要使用弹性查询？

### <a name="azure-sql-database"></a>Azure SQL 数据库

跨 Azure SQL 数据库完全在 T-SQL 查询。 这允许对远程数据库进行只读查询，并为当前的本地 SQL Server 客户提供将使用由三部分和四部分组成的名称或链接服务器的应用程序迁移到 SQL DB 的选项。

### <a name="available-on-standard-tier"></a>在标准层上可用

“标准”和“高级”服务层级均支持弹性查询。 有关较低服务层级的性能限制，请参阅下面有关预览版限制的部分。

### <a name="push-parameters-to-remote-databases"></a>将参数推送到远程数据库

弹性查询现在可以将 SQL 参数推送到远程数据库执行。

### <a name="stored-procedure-execution"></a>存储过程执行

执行远程存储的过程调用或使用远程运行 [sp\_ 执行\_远程](https://msdn.microsoft.com/library/mt703714)。

### <a name="flexibility"></a>灵活性

使用弹性查询的外部表可以引用具有不同架构或表名的远程表。

## <a name="elastic-query-scenarios"></a>弹性查询方案

目标是便于实现多个数据库参与生成单个总体结果中的行的查询方案。 查询可以由用户或应用程序直接编写，也可以通过与数据库连接的工具间接编写。 这在使用商业 BI 或数据集成工具或任何不能更改的应用程序创建报表时特别有用。 使用弹性查询，可以利用 Excel、Power BI、Tableau 或 Cognos 等工具中熟悉的 SQL Server 连接体验跨多个数据库进行查询。
弹性查询可让你通过 SQL Server Management Studio 或 Visual Studio 发出的查询轻松访问整个数据库集合，并便于实现从实体框架或其他 ORM 环境跨数据库查询。 图 1 显示了这样一个方案：其中使用[弹性数据库客户端库](sql-database-elastic-database-client-library.md)的现有云应用程序在扩大的数据层的基础上构建，并且弹性查询用于跨数据库报告。

**图 1** 在扩大的数据层上使用的弹性查询

![在扩大的数据层上使用的弹性查询][1]

弹性查询的客户方案的特征包括以下拓扑：

* **垂直分区 - 跨数据库查询**（拓扑 1）：数据在数据层中的多个数据库之间垂直分区。 通常，不同的表集驻留在不同的数据库上。 这意味着不同数据库上的架构是不同的。 例如，清单的所有表都位于一个数据库上，而与会计相关的所有表都位于第二个数据库上。 采用此拓扑的常见使用案例需要使用一个查询跨多个数据库中的表进行查询或编译报表。
* **水平分区 - 分片**（拓扑 2）：将数据进行水平分区以将行分布到扩大的数据层上。 使用此方法时，所有参与数据库中的架构是相同的。 此方法也称为“分片”。 可以使用 (1) 弹性数据库客户端库或 (2) 自我分片来执行和管理分片。 可使用弹性查询跨多个分片查询或编译报表。

> [!NOTE]
> 弹性查询最适用于可以在外部源端执行大多数处理（筛选、聚合）的报告方案。 它不适用于从远程数据库传输大量数据的 ETL 操作。 对于使用更复杂查询的大量报表工作负荷或数据仓库方案，还可以考虑使用 [Azure SQL 数据仓库](https://azure.microsoft.com/services/sql-data-warehouse/)。
>  

## <a name="vertical-partitioning---cross-database-queries"></a>垂直分区 - 跨数据库查询

若要开始编写代码，请参阅[跨数据库查询（垂直分区）入门](sql-database-elastic-query-getting-started-vertical.md)。

弹性查询可用于使位于 SQL 数据库中的数据供其他 SQL 数据库使用。 这允许从一个数据库的查询引用任何其他远程 SQL 数据库中的表。 第一步是定义每个远程数据库的外部数据源。 外部数据源在要从中获取对远程数据库中的表的访问权限的本地数据库中定义。 在远程数据库上不必进行任何更改。 对于不同数据库具有不同架构的典型垂直分区方案，可以使用弹性查询来实现常见使用案例（例如，访问引用数据和跨数据库查询）。

> [!IMPORTANT]
> 必须拥有 ALTER ANY EXTERNAL DATA SOURCE 权限。 此权限包含在 ALTER DATABASE 权限中。 引用基础数据源需要 ALTER ANY EXTERNAL DATA SOURCE 权限。
>

**引用数据**：拓扑用于引用数据管理。 在下图中，包含引用数据的两个表（T1 和 T2）保存在专用数据库中。 使用弹性查询，现在可以从其他数据库远程访问表 T1 和 T2，如图中所示。 如果引用表较小或对引用表的远程查询包含选择性谓词，请使用拓扑 1。

**图 2** 垂直分区 - 使用弹性查询来查询引用数据

![垂直分区 - 使用弹性查询来查询引用数据][3]

**跨数据库查询**：弹性查询支持需要跨多个 SQL 数据库进行查询的用例。 图 3 显示了四个不同的数据库：CRM、库存、人力资源和产品。 在其中一个数据库中执行的查询还需要访问一个或所有其他数据库。 使用弹性查询时，可以通过在这四个数据库的每个数据库上运行几个简单的 DDL 语句来为此案例配置数据库。 经过此一次性配置后，对远程表的访问就像从 T-SQL 查询或 BI 工具引用本地表一样简单。 如果远程查询不返回大型结果，建议使用此方法。

**图 3** 垂直分区 - 使用弹性查询来跨多个数据库查询

![垂直分区 - 使用弹性查询来跨多个数据库查询][4]

通过以下步骤，为垂直分区方案（需要访问具有相同架构的远程 SQL 数据库中的表）配置弹性数据库查询：

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* **RDBMS** 类型的 [CREATE/DROP 外部数据源](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource
* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) mytable

运行 DDL 语句后，可以访问远程表“mytable”就像它是本地表一样。 Azure SQL 数据库会自动打开与远程数据库的连接，处理远程数据库上的请求并返回结果。

## <a name="horizontal-partitioning---sharding"></a>水平分区 - 分片

使用弹性查询对分片（即，水平分区）的数据层执行报表任务需要[弹性数据库分片映射](sql-database-elastic-scale-shard-map-management.md)来表示数据层的数据库。 通常情况下，仅在此方案中使用单个分片映射，并且具有弹性查询功能的专用数据库（头节点）将作为报表查询的入口点。 只有此专用数据库需要访问分片映射。 图 4 说明了此拓扑及其使用弹性查询数据库和分片映射的配置。 数据层中的数据库可以是任何 Azure SQL 数据库版本。 有关弹性数据库客户端库和创建分片映射的详细信息，请参阅[分片映射管理](sql-database-elastic-scale-shard-map-management.md)。

**图 4** 水平分区 - 使用弹性查询实现分片数据层上的报告

![水平分区 - 使用弹性查询实现分片数据层上的报告][5]

> [!NOTE]
> 弹性查询数据库（头节点）可以是单独的数据库，也可以是承载分片映射的相同数据库。
> 无论选择何种配置，请确保该数据库的服务层级和计算大小足够高，以便处理预期的登录/查询请求数。

通过以下步骤，为水平分区方案（需要访问通常位于多个远程 SQL 数据库上的一组表）配置弹性数据库查询：

* [CREATE MASTER KEY](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* 使用弹性数据库客户端库创建表示数据层的[分片映射](sql-database-elastic-scale-shard-map-management.md)。
* **SHARD_MAP_MANAGER** 类型的 [CREATE/DROP 外部数据源](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource
* [CREATE/DROP EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) mytable

执行这些步骤后，便可以访问水平分区的表“mytable”就像它是本地表一样。 Azure SQL 数据库会自动打开与远程数据库（在其中表以物理方式存储）的多个并行连接，处理远程数据库上的请求并返回结果。
有关水平分区方案所需的步骤的详细信息，可以在[水平分区的弹性查询](sql-database-elastic-query-horizontal-partitioning.md)中找到。

若要开始编写代码，请参阅[弹性查询入门 - 水平分区（分片）](sql-database-elastic-query-getting-started.md)。

## <a name="t-sql-querying"></a>T-SQL 查询

定义外部数据源和外部表后，便可以使用常规 SQL Server 连接字符串连接到在其中定义了外部表的数据库。 然后可以通过该连接对外部表运行 T-SQL 语句，但具有下述限制。 可以在[水平分区](sql-database-elastic-query-horizontal-partitioning.md)和[垂直分区](sql-database-elastic-query-vertical-partitioning.md)的文档主题中找到 T-SQL 查询的详细信息和示例。

## <a name="connectivity-for-tools"></a>工具的连接

可以使用常规 SQL Server 连接字符串将应用程序和 BI 或数据集成工具连接到具有外部表的数据库。 请确保支持将 SQL Server 用作工具的数据源。 连接后，可以引用弹性查询数据库和该数据库中的外部表，就像你会对使用工具连接的任何其他 SQL Server 数据库所做的一样。

> [!IMPORTANT]
> 当前不支持使用 Azure Active Directory 通过弹性查询进行身份验证。

## <a name="cost"></a>开销

弹性查询包含在 Azure SQL 数据库的数据库成本中。 请注意，支持远程数据库与弹性查询终结点在不同的数据中心的拓扑，但通常按 [Azure 费率](https://azure.microsoft.com/pricing/details/data-transfers/)对远程数据库的数据流出量进行收费。

## <a name="preview-limitations"></a>预览版限制

* 在“标准”服务层级上运行第一个弹性查询可能需要长达几分钟时间。 此时间是加载弹性查询功能所必需的；使用更高的服务层级和计算大小可提高加载性能。
* 尚不支持从 SSMS 或 SSDT 对外部数据源或外部表进行脚本编写。
* SQL 数据库的导入/导出尚不支持外部数据源和外部表。 如果需要使用导入/导出，请在导出之前删除这些对象，并在导入后重新创建它们。
* 弹性查询当前仅支持对外部表进行只读访问。 但是，可以对在其中定义了外部表的数据库使用完整的 T-SQL 功能。 这可能对以下操作很有用：例如，使用（例如）SELECT <column_list> INTO <local_table>持久保存临时结果，或在引用外部表的弹性查询数据库中定义存储过程。
* 除了 nvarchar (max), LOB 类型 (包括空间类型) 在外部表定义中不受支持。 一种解决方法是，可以在远程数据库上创建将 LOB 类型强制转换为 nvarchar(max) 的视图，通过该视图（而不是基表）定义外部表，然后在查询中将其强制转换回原始 LOB 类型。
* 结果集中的 nvarchar(max) 数据类型列禁用弹性查询实现中使用的高级批处理技术，并且在大量非聚合数据正在作为查询的结果进行传输的非规范用例中可能会影响一个数量级甚至两个数量级的查询性能。
* 当前不支持外部表上的列统计信息。 支持表统计信息，但需要手动创建。
* 弹性查询仅适用于 Azure SQL 数据库。 不能将其用于在 VM 中查询本地 SQL Server 或 SQL Server。

## <a name="feedback"></a>反馈

请在下面的 MSDN 论坛或 Stack Overflow 上与我们共享有关你使用弹性查询的体验的反馈。 我们对有关该服务的所有类型反馈（缺陷、未完善处、功能差距）感兴趣。

## <a name="next-steps"></a>后续步骤

* 有关垂直分区的教程，请参阅[跨数据库查询（垂直分区）入门](sql-database-elastic-query-getting-started-vertical.md)。
* 有关垂直分区数据的语法和示例查询，请参阅[查询垂直分区数据](sql-database-elastic-query-vertical-partitioning.md)
* 有关水平分区（分片）的教程，请参阅[弹性查询入门 - 水平分区（分片）](sql-database-elastic-query-getting-started.md)。
* 有关水平分区数据的语法和示例查询，请参阅[查询水平分区数据](sql-database-elastic-query-horizontal-partitioning.md)
* 请参阅 [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714)，了解在单个远程 Azure SQL 数据库或在水平分区方案中用作分片的一组数据库中执行 Transact-SQL 语句的存储过程。

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
