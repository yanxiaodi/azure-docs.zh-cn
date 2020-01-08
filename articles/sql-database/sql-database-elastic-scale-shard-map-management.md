---
title: 扩大 Azure SQL 数据库 | Microsoft Docs
description: 如何使用弹性数据库客户端库 ShardMapManager
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 3e7e2294938179da83fb5ad03db177c1142ad096
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568342"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>使用分片映射管理器扩大数据库

若要轻松地扩大 SQL Azure 上的数据库，请使用分片映射管理器。 分片映射管理器是一个特殊的数据库，它维护一个分片集中有关所有分片 （数据库）的全局映射信息。 元数据允许应用程序基于**分片键**值连接到正确的数据库。 此外，在集中的每个分片都包含跟踪本地分片数据的映射 （称为 **shardlet**）。

![分片映射管理](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

了解如何构建这些映射对于分片映射管理至关重要。 使用[弹性数据库客户端库](sql-database-elastic-database-client-library.md)中的 ShardMapManager 类（[Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)）来完成此操作。  

## <a name="shard-maps-and-shard-mappings"></a>分片映射

对每个分片而言必须选择要创建的分片映射类型。 选择取决于数据库架构：

1. 每个数据库一个租户  
2. 每个数据库多个租户（两种类型）：
   1. 列表映射
   2. 范围映射

对于单租户模型，创建**列表映射**分片映射。 单租户模型将每个租户分配一个数据库。 这是适用于 SaaS 开发人员的有效模式，因为它可以简化管理。

![列表映射][1]

多租户模型将数个租户分配给单个数据库（可跨多个数据库分布租户组）。 当希望每个租户具有较小数据需求时使用此模型。 在此模型中，使用范围映射将一系列用户分配到数据库。

![范围映射][2]

或可以使用列表映射来实现多租户数据库模型，以将多个租户分配给单个数据库。 例如，DB1 用于存储租户 ID 1 和 5 的相关信息，而 DB2 用于存储租户 7 和租户 10 的数据。

![单一数据库上的多个租户][3]

### <a name="supported-types-for-sharding-keys"></a>支持的分片键的类型

灵活扩展支持将以下类型用作分片键：

| .NET | Java |
| --- | --- |
| 整数 |整数 |
| long |long |
| guid |uuid |
| byte[]  |byte[] |
| DATETIME | timestamp |
| timespan | 持续时间|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>列表和范围分片映射

使用**各个分片键值的列表**或**分片键值的范围**可构造分片映射。

### <a name="list-shard-maps"></a>列表分片映射

**分片**包含 **shardlet**，shardlet 到分片的映射由分片映射维护。 **列表分片映射**是可标识 shardlet 的单独键值和可用作分片的数据库之间的关联项。  “列表映射”是可以映射到同一个数据库的显式且不同的键值。 例如，键值 1 映射到数据库 A，键值 3 和 6 都映射到数据库 B。

| Key | 分片位置 |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>范围分片映射

在**范围分片映射**中，键范围由 **[Low Value, High Value)** 对描述，其中 *Low Value* 是范围中的最小键，而 *High Value* 是第一个大于范围的值。

例如， **[0, 100)** 包括所有大于或等于 0 且小于 100 的整数。 请注意，多个范围可指向同一数据库，并且支持多个不连续的范围（例如，在下面的示例中，[100,200) 和 [400,600) 可同时指向数据库 C。）

| Key | 分片位置 |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

上面所示的每个表都是 **ShardMap** 对象的概念性示例。 每一行都是单个 **PointMapping**（适用于列表分片映射）对象或 **RangeMapping**（适用于范围分片映射）对象的简化示例。

## <a name="shard-map-manager"></a>分片映射管理器

在客户端库中，分片映射管理器是分片映射的集合。 由 **ShardMapManager** 实例管理的数据保存在以下三个位置中：

1. **全局分片映射 (GSM)** ：指定一个数据库以用作它的所有分片映射和映射的存储库。 将自动创建特殊的表和存储过程以管理信息。 这通常是小型数据库且可轻松进行访问，但不应用于满足应用程序的其他需求。 这些表位于名为 **__ShardManagement** 的特殊架构中。
2. **局部分片映射 (LSM)** ：修改指定为分片的每个数据库，以包含多个小表和特殊存储过程，其中包括特定于该分片的分片映射信息并对其进行管理。 对于 GSM 中的信息而言，该信息是冗余的，但应用程序通过该信息可验证缓存的分片映射信息，而无需将所有负载置于 GSM 上；应用程序可使用 LSM 确定缓存的映射是否仍然有效。 与每个分片上的 LSM 对应的表也位于架构 **__ShardManagement** 中。
3. **应用程序缓存**：每个用于访问 **ShardMapManager** 对象的应用程序实例都可维护其映射的本地内存中缓存。 它存储最近检索到的路由信息。

## <a name="constructing-a-shardmapmanager"></a>构造 ShardMapManager

ShardMapManager 对象是使用工厂（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)）模式构造的。 通过 ShardMapManagerFactory.GetSqlShardMapManager（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)）方法可获取具有 ConnectionString 形式的凭据（包括用于保存 GSM 的服务器名称和数据库名称），并返回 ShardMapManager 的实例。  

**请注意：** 在应用程序的初始化代码内，每个应用域只应实例化 **ShardMapManager** 一次。 在同一个应用域中创建 ShardMapManager 的其他实例将导致应用程序的内存增加且 CPU 使用率增加。 **ShardMapManager** 可包含任意数量的分片映射。 尽管对于许多应用程序而言，单个分片映射可能是足够的，但有时针对不同的架构或出于特定目的，需使用不同的数据库集，在这些情况下多个分片可能更合适。

在此代码中，应用程序尝试使用 TryGetSqlShardMapManager（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)）方法打开现有的 ShardMapManager。 如果数据库中尚不存在表示全局 ShardMapManager (GSM) 的对象，客户端库会使用 CreateSqlShardMapManager（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)）方法创建这些对象。

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

对于 .NET 版本，可以使用 PowerShell 来创建新的分片映射管理器。 [此处](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)提供了一个示例。

## <a name="get-a-rangeshardmap-or-listshardmap"></a>获取 RangeShardMap 或 ListShardMap

创建分片映射管理器以后，可以使用 TryGetRangeShardMap（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)）、TryGetListShardMap（[Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)）或 GetShardMap（[Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)）方法获取 RangeShardMap（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)）或 ListShardMap（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)）。

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>分片映射管理凭据

用于管理和操作分片映射的应用程序不同于那些使用分片映射路由连接的应用程序。

若要管理分片映射（添加或更改分片、分片映射等），必须使用**在 GSM 数据库和用作分片的每个数据库上都具有读/写权限的凭据**实例化 **ShardMapManager**。 在输入或更改分片映射信息时，这些凭据必须允许编写 GSM 和 LSM 中的表，以及在新分片上创建 LSM 表。  

请参阅[用于访问弹性数据库客户端库的凭据](sql-database-elastic-scale-manage-credentials.md)。

### <a name="only-metadata-affected"></a>仅元数据受影响

用于填充或更改 **ShardMapManager** 数据的方法不会更改存储在分片本身中的用户数据。 例如，类似于 **CreateShard**、**DeleteShard**、**UpdateMapping** 等的方法仅影响分片映射元数据， 而不会删除、添加或更改分片中所包含的用户数据。 但是，这些方法旨在与你执行的单独操作结合使用，以创建或删除实际数据库，或者将行从一个分片移动到另一个分片，以使分片环境恢复均衡。  （弹性数据库工具附带的**拆分-合并**工具将使用这些 API 并安排在分片之间移动实际数据。）请参阅[使用弹性数据库拆分 / 合并工具进行缩放](sql-database-elastic-scale-overview-split-and-merge.md)。

## <a name="data-dependent-routing"></a>数据依赖型路由

分片映射管理器主要由需要数据库连接的应用程序用来执行特定于应用的数据操作。 这些连接必须与正确的数据库关联。 这称为**数据相关的路由**。 对于这些应用程序，通过使用在 GSM 数据库上具有只读访问权限的凭据，实例化来自工厂的分片映射管理器对象。 以后，单独的连接请求将提供连接相应分片数据库时所需的凭据。

请注意，这些应用程序（使用具有只读权限的凭据打开的 ShardMapManager）无法对映射进行更改。 为了满足这些需求，请创建特定于管理的应用程序或 PowerShell 脚本，以提供如前所述的更高级别权限的凭据。 请参阅[用于访问弹性数据库客户端库的凭据](sql-database-elastic-scale-manage-credentials.md)。

有关详细信息，请参阅[数据依赖型路由](sql-database-elastic-scale-data-dependent-routing.md)。

## <a name="modifying-a-shard-map"></a>修改分片映射

可采用不同方式更改分片映射。 以下所有方法都可修改用于描述分片及其映射的元数据，但这些方法不以物理方式修改分片内的数据，也不创建或删除实际数据库。  下面所述的分片映射上的某些操作可能需要与以物理方式移动数据或添加和删除用作分片的数据库的管理操作进行协调。

这些方法作为构建基块一同工作，以便在分片的数据库环境中修改数据的总体分发情况。  

* 若要添加或删除分片：请使用 shardmap（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)）类的 CreateShard（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)）和 DeleteShard（[Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)）。
  
    若要执行这些操作，表示目标分片的服务器和数据库必须已经存在。 这些方法不会对数据库本身产生任何影响，仅对分片映射上的元数据产生影响。
* 若要创建或删除映射到分片的点或范围：请使用 RangeShardMapping（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)）类的 CreateRangeMapping（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping)、[.NET](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))）和 DeleteMapping（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)），以及 ListShardMap（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)）类的 CreatePointMapping（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)）。
  
    许多不同的点或范围可映射到相同的分片。 这些方法仅影响元数据，而不会影响已显示在分片中的任何数据。 如果为了与 **DeleteMapping** 操作保持一致而需要将数据从数据库中删除，需要单独执行这些操作，但需要结合使用这些方法。  
* 若要将现有的范围拆分为两个，或将相邻的范围合并为一个：请使用 SplitMapping（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping)[.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)）和 MergeMappings（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings)、[.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)）。  
  
    请注意，拆分和合并操作**不更改键值要映射到的分片**。 拆分操作可将现有的范围拆分为两个部分，但在映射到相同分片时同时保留这两个部分。 对在已映射到相同分片的两个相邻的范围进行合并操作，从而可将其合并到单个范围中。  要在分片之间移动点或范围本身，需要将 **UpdateMapping** 与移动的实际数据结合使用，才能进行协调。  当需要移动数据时，可以使用弹性数据库工具中随附的**拆分 / 合并**服务，以将分片映射的更改与数据移动相协调。
* 若要将单独的点或范围重新映射（或移动）到不同的分片：请使用 UpdateMapping（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)）。  
  
    由于可能需要将数据从一个分片移动到另一个分片，以便与 **UpdateMapping** 操作保持一致，因此需要单独执行此移动，但需要结合使用这些方法。

* 若要在联机和脱机状态下执行映射：请使用 MarkMappingOffline（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)）和 MarkMappingOnline（[Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)）来控制映射的联机状态。
  
    仅当映射处于“脱机”状态时才允许在分片映射上进行某些操作，其中包括 **UpdateMapping** 和 **DeleteMapping**。 当映射处于脱机状态时，基于该映射中所包含的键的数据依赖请求将返回一个错误。 此外，当范围首次处于脱机状态时，所有到受影响的分片的连接都会自动终止，以防止因范围的更改而导致查询出现不一致或不完整的结果。

映射是 .Net 中的不可变对象。  以上会更改映射的所有方法也会使代码中任何对映射的引用失效。 为了更轻松地执行操作序列来更改映射的状态，所有会更改映射的方法都将返回新的映射引用，以便能够链接操作。 例如，若要在 shardmap sm 中删除包含索引键 25 的现有映射，可以执行以下命令：

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>添加分片

对于已经存在的分片映射，应用程序通常仅需要添加新分片，以处理预期的新键或键范围数据。 例如，由租户 ID 分片的应用程序可能需要为新的租户设置新分片，或者在每个新的月份开始之前，每月分片的数据可能需要设置新分片。

如果新的键值范围还不是现有映射的组成部分且无需移动数据，则添加新分片以及将新的键或范围关联到该分片非常简单。 有关添加新分片的详细信息，请参阅[添加新分片](sql-database-elastic-scale-add-a-shard.md)。

但是，在需要移动数据的情况下，需要拆分/合并工具并结合使用必要的分片映射更新，才能安排在分片之间移动数据。 有关使用拆分合并工具的详细信息，请参阅[拆分/合并概述](sql-database-elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
