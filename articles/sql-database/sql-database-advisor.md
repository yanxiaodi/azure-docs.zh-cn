---
title: 性能建议 - Azure SQL 数据库 | Microsoft Docs
description: Azure SQL 数据库提供有关 SQL 数据库的建议，以提升当前查询性能。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: 08def3ac2fd94f01586bc690d867c04758b8856b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569531"
---
# <a name="performance-recommendations-for-sql-database"></a>SQL 数据库性能建议

Azure SQL 数据库与应用程序一起自行学习和进行适应性调整。 它提供可最大限度提高 SQL 数据库性能的自定义建议。 SQL 数据库持续评估并分析 SQL 数据库的使用情况历史记录。 提供的建议以数据库唯一工作负载模式为依据，有助于提升性能。

> [!TIP]
> [自动调整](sql-database-automatic-tuning.md)是自动调整一些最常见的数据库性能问题的推荐方法。 [查询性能见解](sql-database-query-performance.md)是基本 Azure SQL 数据库性能监控需求的推荐方法。 [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) 是针对大规模高级数据库性能监控的推荐方法，具有内置智能功能，可自动执行性能故障排除。
>

## <a name="create-index-recommendations"></a>“创建索引”建议
SQL 数据库持续监视正在运行的查询，并发现可以提升性能的索引。 确信缺少特定索引后，便会新建“创建索引”建议。

 Azure SQL 数据库通过估计索引在一段时间内带来的性能提升，确信是否有必要创建索引。 根据估计的性能提升高低，将性能建议分为高、中或低三类。 

使用建议创建的索引始终都会标记为 auto_created。 可以通过查看 sys.indexes 视图，确定哪些是 auto_created 索引。 自动创建的索引不会阻止 ALTER/RENAME 命令。 

如果尝试删除包含自动创建的索引的列，那么命令会传递下去。 自动创建的索引会随列一起删除。 常规索引会阻止对已编制索引的列执行 ALTER/RENAME 命令。

应用“创建索引”建议后，Azure SQL 数据库便会比较查询性能与基线性能。 如果新索引提升了性能，则将建议标记为成功，并提供影响报表。 如果索引未提升性能，则会自动还原。 SQL 数据库使用此过程确保建议可提升数据库性能。

所有“创建索引”建议都有退让策略，即如果数据库或池的资源使用率很高，则不允许应用建议。 退让策略会考虑 CPU、数据 IO、日志 IO 和可用存储。 

如果 CPU、数据 IO 或日志 IO 在之前的 30 分钟内高于 80%，则推迟“创建索引”建议。 如果创建索引后可用存储低于 10%，则建议会进入错误状态。 如果几天后自动优化仍认为索引会很有益，此过程再次启动。 

此过程不断重复，直到没有足够的可用存储来创建索引或不再认为索引有益。

## <a name="drop-index-recommendations"></a>“删除索引”建议
除了检测缺少的索引外，SQL 数据库还会持续分析现有索引的性能。 Azure SQL 数据库会建议删除未使用的索引。 在以下两种情况下，建议删除索引：
* 索引是另一索引的副本（已编入索引且包含的列、分区架构和筛选器都相同）。
* 长时间（93 天）未使用索引。

也会验证已实现的“删除索引”建议。 如果性能得到提升，则会生成影响力报表。 如果性能下降，则会还原建议。


## <a name="parameterize-queries-recommendations"></a>参数化查询建议
当具有一个或多个正在持续被重新编译但都以相同的查询执行计划结束的查询时，就会出现*参数化查询*建议。 这种状态提供了一个应用强制参数化的机会。 而强制参数化允许缓存并在将来重复使用查询计划，从而改善性能和减少资源使用。 

对 SQL Server 发出的每个查询一开始需要进行编译，生成执行计划。 每个生成的计划添加到计划缓存中。 相同查询的后续执行可以重复使用该缓存中的此计划，而无需进一步编译。 

包含非参数化值的查询可能会导致性能开销，因为每次非参数化值有所不同时，需重新编译执行计划。 在许多情况下，使用不同参数值的相同查询生成相同的执行计划。 但这些计划仍然分别添加到计划缓存中。 

重新编译执行计划的过程会占用数据库资源、增加查询持续时间并使计划缓存溢出。 而这些事件导致系统从缓存中逐出计划。 可以通过对数据库设置强制参数化选项来更改此 SQL Server 行为。 

为了帮助估计此建议的影响，系统提供实际 CPU 使用率和预计 CPU 使用率（就像已应用建议一样）之间的比较。 此建议可帮助节省 CPU。 它还可帮助减少查询持续时间和计划缓存的开销，这意味着更多的计划可保留在缓存中并重复使用。 可以通过选择“应用”命令来快速轻松地应用此建议。 

应用此建议后，它会在几分钟之内对数据库启用强制参数化。 并启动监视进程（大约持续 24 小时）。 经过这段时间后，即可看到验证报告。 该报告显示应用此建议之前和之后的 24 小时内数据库的 CPU 使用率。 SQL 数据库顾问具有安全机制，在检测到性能衰退时可以自动还原已应用的建议。

## <a name="fix-schema-issues-recommendations-preview"></a>修复架构问题建议（预览）

> [!IMPORTANT]
> Microsoft 当前正在弃用“修复架构问题”建议。 建议使用[智能见解](sql-database-intelligent-insights.md)来监视数据库性能问题，包括“修复架构问题”建议以前涉及的架构问题。
> 

当 SQL 数据库服务发现 SQL 数据库上架构相关 SQL 错误的数量发生异常时，就会出现“修复架构问题”建议。 此建议通常在数据库在一个小时内遭遇到多个架构相关的错误（无效的列名、无效的对象名称等）时出现。

“架构问题”是 SQL Server 中的一类语法错误。 此类错误于 SQL 查询的定义与数据库架构的定义不一致时发生。 例如，目标表中可能缺少查询所需的某个列，反之亦然。 

当 Azure SQL 数据库服务发现 SQL 数据库上架构相关 SQL 错误的数量发生异常时，就会出现“修复架构问题”建议。 下表显示与架构问题相关的错误：

| SQL 错误代码 | 消息 |
| --- | --- |
| 201 |过程或函数“ *”需要参数“* ”，但未提供该参数。 |
| 207 |列名称“*”无效。 |
| 208 |对象名“*”无效。 |
| 213 |列名或所提供值的数目与表定义不匹配。 |
| 2812 |找不到存储过程“*”。 |
| 8144 |为过程或函数 * 指定了过多的参数。 |

## <a name="custom-applications"></a>自定义应用程序

开发人员可以考虑使用针对 Azure SQL 数据库的性能建议来开发自定义应用程序。 可以通过 [Get-AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API 访问门户中列出的针对数据库的所有建议。

## <a name="next-steps"></a>后续步骤
监视建议并继续应用它们以优化性能。 数据库工作负荷是动态的，并且不断地更改。 SQL 数据库顾问继续监视和提供可能提高数据库性能的建议。 

* 有关数据库索引和查询执行计划自动优化的详细信息，请参阅 [Azure SQL 数据库自动优化](sql-database-automatic-tuning.md)。
* 有关借助自动诊断和性能问题的根本原因分析自动监视数据库性能的详细信息，请参阅 [Azure SQL 智能见解](sql-database-intelligent-insights.md)。
*  有关如何在 Azure 门户中使用性能建议的详细信息，请参阅 [Azure 门户中的性能建议](sql-database-advisor-portal.md)。
* 若要了解和查看排名靠前的查询的性能影响，请参阅[查询性能见解](sql-database-query-performance.md)。


