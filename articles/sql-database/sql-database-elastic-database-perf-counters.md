---
title: 创建性能计数器，以便跟踪分片映射管理器的性能
description: ShardMapManager 类和数据相关的路由的性能计数器
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: ae7666113bd3a4bdb595a8312fdb25007d4ed2c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568673"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>创建性能计数器，以便跟踪分片映射管理器的性能

性能计数器用于跟踪[数据相关路由](sql-database-elastic-scale-data-dependent-routing.md)操作的性能。 可在“弹性数据库: 分片管理”类别下的“性能监视器”中访问这些计数器。

可以捕获[分片映射管理器](sql-database-elastic-scale-shard-map-management.md)的性能，尤其是在使用[数据依赖型路由](sql-database-elastic-scale-data-dependent-routing.md)时。 计数器是使用 Microsoft.Azure.SqlDatabase.ElasticScale.Client 类的方法创建的。  


**有关最新版本：** 请转到 [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)。 另请参阅[升级应用以使用最新的弹性数据库客户端库](sql-database-elastic-scale-upgrade-client-library.md)。

## <a name="prerequisites"></a>系统必备

* 若要创建性能类别和计数器，用户必须属于托管应用程序的计算机上的本地**管理员**组。  
* 若要创建性能计数器实例和更新计数器，用户必须是**管理员**或**性能监视器用户组**的成员。

## <a name="create-performance-category-and-counters"></a>创建性能类别和计数器

若要创建计数器，请调用 [ShardMapManagementFactory 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)的 CreatePerformanceCategoryAndCounters 方法。 只有管理员才能执行该方法：

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

也可以使用[此 ](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283)PowerShell 脚本来执行该方法。
该方法将创建以下性能计数器：  

* **缓存的映射**：分片映射缓存的映射数。
* **DDR 操作数/秒**：分片映射的数据相关路由操作速率。 对 [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) 的调用导致成功连接到目标分片时，将更新此计数器。
* **映射查找缓存命中数/秒**：分片映射中映射的成功缓存查找操作速率。
* **映射查找缓存未命中数/秒**：分片映射中映射的失败缓存查找操作速率。
* **在缓存中添加或更新的映射数/秒**：在缓存中为分片映射添加或更新映射的速率。
* **从缓存中删除的映射数/秒**：从缓存中删除分片映射的映射速率。

性能计数器是按进程为每个缓存分片映射创建的。  

## <a name="notes"></a>说明

以下事件会触发性能计数器创建：  

* 在 ShardMapManager 包含任何分片映射的情况下，使用[预先加载](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)初始化 [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)。 这包括 [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) 和 [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) 方法。
* 成功查找分片映射（使用 [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx)、[GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) 或 [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)）。
* 使用 CreateShardMap() 成功创建分片映射。

对分片映射和映射执行的所有缓存操作会更新性能计数器。 使用 DeleteShardMap() 成功删除分片映射会导致删除性能计数器实例。  

## <a name="best-practices"></a>最佳实践

* 只应在创建 ShardMapManager 对象之前创建性能类别和计数器一次。 每次执行命令 CreatePerformanceCategoryAndCounters() 都会清除以前的计数器（丢失所有实例报告的数据），并创建新计数器。  
* 性能计数器实例是按进程创建的。 任何应用程序崩溃或从缓存中删除分片映射都会导致删除性能计数器实例。  

### <a name="see-also"></a>请参阅

[弹性数据库功能概述](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
