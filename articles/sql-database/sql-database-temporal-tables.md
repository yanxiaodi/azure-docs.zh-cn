---
title: Azure SQL 数据库中的临时表入门 | Microsoft 文档
description: 了解如何开始使用 Azure SQL 数据库中的临时表。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 06/26/2019
ms.openlocfilehash: 39c19661a71a8b466aa6ff25be9e895189dfbfb3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566372"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Azure SQL 数据库中的临时表入门

临时表是 Azure SQL 数据库中新的可编程功能，可用于跟踪和分析数据更改的完整历史记录，而无需编写自定义代码。 临时表保存与时间上下文密切相关的数据，因此，只有特定时段内的存储事实才会解译为有效。 临时表的这种属性可让用户执行基于时间的有效分析，并从数据演变中获得见解。

## <a name="temporal-scenario"></a>临时表方案

本文演示了在应用程序方案中使用临时表的步骤。 假设你想要从头开始跟踪开发中的新网站上的用户活动，或跟踪要使用用户活动分析扩展的现有网站上的用户活动。 在这个简化的示例中，我们假设一段时间内浏览过的网页数是需要在托管于 Azure SQL 数据库上的网站数据库中捕获和监视的指标。 用户活动历史分析的目标是获取有关重新设计网站的意见，并为访客提供更好的体验。

此场景的数据库模型非常简单 - 用户活动指标以一个整数字段 **PageVisited** 表示，并与用户配置文件中的基本信息一起捕获。 此外，对于基于时间的分析，需要为每个用户保留一系列的行，其中每行代表特定时间段内特定用户访问过的网页数。

![架构](./media/sql-database-temporal-tables/AzureTemporal1.png)

幸运的是，无需在应用上花费过多的精力就能维护此活动信息。 可以使用临时表将过程自动化：使你在网站设计过程中保有完全的弹性并节省更多的时间，从而将重心放在数据分析本身。 唯一要做的事就是确保将 **WebSiteInfo** 表配置为[版本由系统控制的临时表](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0)。 下面描述了在此方案中使用临时表的确切步骤。

## <a name="step-1-configure-tables-as-temporal"></a>步骤 1：将表配置为时态表
根据是要开始新的开发工作，还是升级现有的应用程序，可以创建时态表，或者通过添加临时属性来修改现有表。 在一般情况下，方案可能混用了这两个选项。 使用 [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS)、[SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) 或其他任何 Transact-SQL 开发工具执行以下操作。

> [!IMPORTANT]
> 建议始终使用最新版本的 Management Studio 以保持与 Microsoft Azure 和 SQL 数据库的更新同步。 [更新 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)。
> 
> 

### <a name="create-new-table"></a>创建新表
在 SSMS 对象资源管理器中使用上下文菜单项“新建版本由系统控制的表”打开包含时态表模板脚本的查询编辑器，并使用“指定模板参数的值”(Ctrl+Shift+M) 来填充模板：

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

在 SSDT 中将新项添加到数据库项目时，请选择“时态表(版本由系统控制)”模板。 此时会打开表设计器，让你轻松指定表布局：

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

也可以通过直接指定 Transact-SQL 语句来创建时态表，如以下示例中所示。 请注意，每个时态表的必需元素为 PERIOD 定义以及可引用将存储历史行版本的另一个用户表的 SYSTEM_VERSIONING 子句：

```
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

创建版本由系统控制的时态表时，会自动创建随附默认配置的历史记录表。 默认历史记录表包含期限列（结束、开始）上启用页压缩的聚集 B 树目录索引。 此配置非常适合使用时态表的大部分方案，特别是用于[数据审核](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0)。 

在此特定案例中，我们的目标是针对一段较长的数据历史记录以及较大的数据集，执行基于时间的趋势分析，因此历史记录表的存储选择为聚集列存储索引。 聚集列存储为分析查询提供极佳的压缩和性能。 时态表允许灵活且完全独立地在当前表和时态表中配置索引。 

> [!NOTE]
> 高级层、标准层、S3 及更高版本中提供列存储索引。
>

以下脚本演示如何将历史记录表的默认索引更改为聚集列存储：

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

临时表在对象资源管理器中以特定图标表示以便于识别，其历史记录表显示为子节点。

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>将现有表更改为临时表
让我们探讨替代方案，其中 WebsiteUserInfo 表已存在，但不是针对保留更改历史记录而设计的。 在此情况下，只需扩展现有表，使其成为临时表，如以下示例中所示：

```
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

## <a name="step-2-run-your-workload-regularly"></a>步骤 2：定期运行工作负荷
临时表的主要优点是不需要以任何方式更改或调整网站就可以执行更改跟踪。 创建临时表后，每对数据进行修改时，会自动保存以前的行版本。 

若要对此特定方案使用自动更改跟踪, 只需在每次用户结束网站上的会话时更新列**PagesVisited** :

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

请务必注意，更新查询不需要知道实际操作进行的时间，也不需要知道如何保留历史数据以供将来分析使用。 Azure SQL 数据库会自动处理这两个方面。 下图演示了如何在每次更新时生成历史记录数据。

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>步骤 3：执行历史数据分析
现在，当启用版本由系统控制的临时表时，只需一个查询就能执行历史数据分析。 本文将提供一些解决常见分析方案的示例 - 若要了解所有详细信息，请浏览随 [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) 子句一起引入的各种选项。

若要查看按访问网页次数排序的前 10 个用户，请运行以下查询：

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

可以轻松修改此查询，以分析一天前、一个月前或所需的任何过去时间点的站点访问记录。

若要执行前一天的基本统计分析，请使用以下示例：

```
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

若要搜索特定用户在某个时间段的活动，请使用 CONTAINED IN 子句：

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

图形可视化对于临时查询特别方便，因为可以轻松、直观地显示趋势和使用模式：

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>不断演变的表架构
通常，开发应用时需要更改临时表架构。 为此，只需运行正则 ALTER TABLE 语句，Azure SQL 数据库就能正确传播历史记录表的更改。 以下脚本演示如何添加要跟踪的其他属性：

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

同样，可以在工作负荷处于活动状态时更改列定义：

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

最后，可以删除不再需要的列。

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

或者，在已连接到数据库（联机模式）或正在开发数据库项目（脱机模式）时，使用最新的 [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) 来更改时态表架构。

## <a name="controlling-retention-of-historical-data"></a>控制历史数据的保留期
使用版本由系统控制的时态表时，历史记录表可能比常规表更容易增大数据库大小。 大型以及不断增长的历史记录表可能会成为一个问题，这不单单体现在存储成本的增加上，而且还会降低临时查询的性能。 因此，针对管理历史记录表中的数据制定一个数据保留策略，是规划和管理每个时态表的生命周期的一个重要方面。 使用 Azure SQL 数据库，可以通过以下方法来管理时态表中的历史数据：

* [表分区](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [自定义清理脚本](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>后续步骤

- 有关临时表的详细信息, 请参阅签出[临时表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)。
- 访问第 9 频道收听[客户实施临时表的真实成功案例](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions)，观看[临时表现场演示](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016)。

