---
title: Azure SQL 数据仓库中的数据仓库单位（DWU、cDWU）| Microsoft Docs
description: 针对选择理想数目的数据仓库单位（DWU、cDWU）来优化价格和性能以及如何更改单位数提供了建议。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: 282fab70e3b6d1fcf81814b2dd599259e2396fb3
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036057"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>数据仓库单位 (DWU) 和计算数据仓库单位 (cDWU)

针对选择理想数目的数据仓库单位（DWU、cDWU）来优化价格和性能以及如何更改单位数提供了建议。

## <a name="what-are-data-warehouse-units"></a>什么是数据仓库单位？

Azure SQL 数据仓库 CPU、内存和 IO 将捆绑到称为数据仓库单位 (DWU) 的计算规模单位中。 DWU 表示抽象、规范化的计算资源和性能度量值。 更改服务级别会改变系统可用的 DWU 数量，从而调整系统的性能和成本。

若要提高性能，可以增加数据仓库单位的数量。 若要降低性能，可以减少数据仓库单位数。 存储和计算成本分别计费，因此更改数据仓库单位数不会影响存储成本。

数据仓库单位性能基于这些数据仓库负载指标：

- 标准数据仓库查询扫描大量行并执行复杂聚合的速度。 这是一种 I/O 和 CPU 密集型操作。
- 数据仓库从 Azure 存储 Blob 或 Azure Data Lake 引入数据的速度。 这是一种网络和 CPU 密集型操作。
- [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL 命令复制表的速度。 此操作涉及从存储读取数据、将数据分配到设备的节点上，以及重新将数据写入到存储。 这是一种 CPU、IO 和网络密集型操作。

增加 DWU：

- 以线性方式更改系统对扫描、聚合和 CTAS 语句的性能
- 增加 PolyBase 加载操作的读取器和编写器数量
- 增加并发查询和并发槽的最大数量。

## <a name="service-level-objective"></a>服务级别目标

服务级别目标 (SLO) 是确定数据仓库的成本和性能级别的可伸缩性设置。 第 2 代服务级别以计算数据仓库单位 (cDWU) 计量，例如 DW2000c。 第 1 代服务级别以 DWU 计量，例如 DW2000。
  > [!NOTE]
  > Azure SQL 数据仓库 Gen2 最近添加了额外的扩展功能，以支持低至 100 cDWU 的计算层。 当前在 Gen1 上需要较低计算层的现有数据仓库现可升级到当前可用区域中的 Gen2，无需额外成本。  如果你的区域尚不支持，仍可升级到支持的区域。 有关详细信息，请参阅[升级到 Gen2](upgrade-to-latest-generation.md)。

在 T-SQL 中，SERVICE_OBJECTIVE 设置确定了数据仓库的服务级别和性能层。

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>性能层和数据仓库单位

每个性能层用于其数据仓库单位测量的单位都略有不同。 当规模单位直接转换为计费时，这种差异会反映在发票上。

- 第 1 代数据仓库以数据仓库单位计量 (DWU)。
- 第 2 代数据仓库以计算数据仓库单位 (cDWU) 计量。

DWU 和 cDWU 都支持增加或减少计算，以及在无需使用数据仓库时暂停计算。 这些操作均可按需进行。 第 2 代还会在计算节点上使用基于本地磁盘的缓存以提高性能。 缩放或暂停系统时，缓存将失效，因此在达到最佳性能前，缓存需要预热一段时间。  

增加数据库单位时，将以线性方式增加计算资源。 第 2 代可提供最佳查询性能和最大规模。 Gen2 系统还最大限度利用了缓存。

### <a name="capacity-limits"></a>容量限制

每个 SQL Server（例如 myserver.database.windows.net）都有一个允许指定数据仓库单位数的[数据库事务单位 (DTU)](../sql-database/sql-database-what-is-a-dtu.md) 配额。 有关详细信息，请参阅[工作负荷管理容量限制](sql-data-warehouse-service-capacity-limits.md#workload-management)。

## <a name="how-many-data-warehouse-units-do-i-need"></a>我需要多少个数据仓库单位？

合适的数据仓库单位数很大程度上取决于工作负荷及已加载到系统的数据量。

查找最适合工作负荷的 DWU 的步骤：

1. 首先选择一个较小的 DWU。
2. 在测试数据加载到系统中时，监视应用程序性能，将所选 DWU 数目与观测到的性能变化进行比较。
3. 确认峰值活动周期的其他要求。 显示活动中重要峰值和谷值的工作负荷可能需要频繁缩放。

SQL 数据仓库是一个向外扩展系统，可预配大量计算和查询大量数据。 要查看其真正的缩放功能（尤其是针对较大的 DWU），建议在缩放的同时对数据集进行缩放，确保可向 CPU 提供足够的数据。 对于规模测试，建议至少使用 1 TB。

> [!NOTE]
>
> 此外，如果可以计算节点之间拆分工作，与多个并行化只会增加查询性能。 如果发现该缩放未更改性能，建议优化表设计和/或查询。 有关查询优化指南，请参阅[管理用户查询](sql-data-warehouse-overview-manage-user-queries.md)。

## <a name="permissions"></a>权限

更改数据仓库单位需要 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) 中所述的权限。

Azure 资源的内置角色（如 SQL DB 参与者和 SQL Server 参与者）可以更改 DWU 设置。

## <a name="view-current-dwu-settings"></a>查看当前的 DWU 设置

查看当前的 DWU 设置：

1. 在 Visual Studio 中打开“SQL Server 对象资源管理器”。
2. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。
3. 从 sys.database_service_objectives 动态管理视图中选择。 下面是一个示例：

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>更改数据仓库单位

### <a name="azure-portal"></a>Azure 门户

更改 DWU 或 cDWU：

1. 打开 [Azure 门户](https://portal.azure.com)，打开数据库，并单击“缩放”。

2. 在“缩放”下，向左或向右移动滑块，以更改 DWU 设置。

3. 单击“保存”。 此时会显示确认消息。 单击“是”以确认或“否”以取消。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要更改 DWU 或 cDWU，请使用 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell cmdlet。 以下示例将托管在服务器 MyServer 上的数据库 MySQLDW 的服务级别目标设置为 DW1000。

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

有关详细信息，请参阅[适用于 SQL 数据仓库的 PowerShell cmdlet](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

使用 T-SQL 可查看当前的 DWU 或 cDWU 设置、更改设置和检查进度。

更改 DWU 或 cDWU：

1. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。
2. 使用 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL 语句。 以下示例将数据库 MySQLDW 的服务级别目标设置为 DW1000。

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API

若要更改 DWU，请使用[创建或更新数据库 REST API](/rest/api/sql/databases/createorupdate)。 下面的示例将托管在服务器 MyServer 上的数据库 MySQLDW 的服务级别目标设置为 DW1000。 该服务器位于名为 ResourceGroup1 的 Azure 资源组中。

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

有关更多 REST API 示例，请参阅[适用于 SQL 数据仓库的 REST API](sql-data-warehouse-manage-compute-rest-api.md)。

## <a name="check-status-of-dwu-changes"></a>检查 DWU 更改的状态

DWU 更改可能需要几分钟才能完成。 如果要自动缩放，建议实现逻辑以确保某些操作在继续另一项操作前已完成。

检查通过不同终结点的数据库状态可允许你正确实现自动化。 门户在完成操作和数据库当前状态时提供通知，但不允许以编程方式检查状态。

不能使用 Azure 门户检查向外扩展操作的数据库状态。

检查 DWU 更改的状态：

1. 连接到与逻辑 SQL 数据库服务器关联的 master 数据库。
2. 提交以下查询以检查数据库状态。

```sql
SELECT    *
FROM      sys.databases
;
```

1. 提交以下查询以检查操作状态

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

此 DMV 返回针对 SQL 数据仓库的各种管理操作的相关信息，例如操作和操作状态（IN_PROGRESS 或 COMPLETED）。

## <a name="the-scaling-workflow"></a>缩放工作流

启动缩放操作时，系统首先终止所有打开的会话，回退所有打开的事务以确保状态一致。 对于缩放操作，缩放仅在此事务回退完成后才会发生。  

- 对于增加操作，系统会分离所有计算节点，预配额外计算节点，然后重新附加到存储层。
- 对于减少操作，系统会分离所有计算节点，然后仅将所需节点重新附加到存储层。

## <a name="next-steps"></a>后续步骤

若要了解有关如何管理性能的详细信息，请参阅[用于工作负荷管理的资源类](resource-classes-for-workload-management.md)和[内存和并发限制](memory-and-concurrency-limits.md)。
