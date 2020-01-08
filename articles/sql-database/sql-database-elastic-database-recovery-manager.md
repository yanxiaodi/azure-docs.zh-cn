---
title: 使用恢复管理器解决分片映射问题 | Microsoft Docs
description: 使用 RecoveryManager 类解决分片映射问题
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: cbc4985f032c228db7a9ddf719390bbf2d0166b9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568697"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>使用 RecoveryManager 类解决分片映射问题

[RecoveryManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager)类为 ADO.NET 应用程序提供了在分片数据库环境中轻松检测和更正全局分片地图 (GSM) 与本地分片地图 (LSM) 之间的任何不一致的能力。

GSM 和 LSM 跟踪分片环境中每个数据库的映射。 有时，GSM 和 LSM 之间会发生中断。 在这种情况下，请使用 RecoveryManager 类来检测和修复中断。

RecoveryManager 类是[弹性数据库客户端库](sql-database-elastic-database-client-library.md)的一部分。

![分片映射][1]

有关术语定义，请参阅 [弹性数据库工具词汇表](sql-database-elastic-scale-glossary.md)。 若要了解如何使用 **ShardMapManager** 来管理分片解决方案中的数据，请参阅[分片映射管理](sql-database-elastic-scale-shard-map-management.md)。

## <a name="why-use-the-recovery-manager"></a>为何使用恢复管理器

在分片数据库环境中，每个数据库有一个租户，而每个服务器有多个数据库。 环境中也可能有多个服务器。 每个数据库映射在分片映射中，以便将调用路由到正确的服务器和数据库。 根据分片键跟踪数据库，将为每个分片分配一系列键值。 例如，分片键可能代表从“D”到“F”的客户名称。 所有分片（也称为数据库）及其映射范围的映射都包含在全局分片映射 (GSM) 中。 每个数据库还包含分片上所包含范围的映射，称为**本地分片映射 (LSM)** 。 当应用连接到分片时，会在应用中缓存映射以供快速检索。 LSM 用于验证缓存的数据。

GSM 和 LSM 可能因以下原因变得不同步：

1. 删除其范围被认为不再被使用的分片，或重命名分片。 删除分片导致**孤立的分片映射**。 类似地，重命名的数据库同样可能会造成孤立的分片映射。 根据更改的目的，可能需要删除分片或需要更新分片位置。 若要恢复已删除的数据库，请参阅[还原已删除的数据库](sql-database-recovery-using-backups.md)。
2. 发生异地故障转移事件。 要继续，必须有人更新服务器名称和应用程序中分片映射管理器的数据库名称，并更新分片映射中所有分片的分片映射详细信息。 如果存在异地故障转移，此类恢复逻辑应该在故障转移工作流中自动化。 自动化修复操作可为异地冗余的数据库启用顺畅的管理，并避免人工操作。 若要了解在出现数据中心服务中断时用于恢复数据库的选项，请参阅[业务连续性](sql-database-business-continuity.md)和[灾难恢复](sql-database-disaster-recovery.md)。
3. 分片或 ShardMapManager 数据库将还原到较早的时间点。 若要了解使用备份的时点恢复，请参阅[使用备份恢复](sql-database-recovery-using-backups.md)。

有关 Azure SQL 数据库弹性数据库工具、异地复制和还原的详细信息，请参阅以下内容：

* [概述：使用 SQL 数据库确保云业务连续性并进行数据库灾难恢复](sql-database-business-continuity.md)
* [弹性数据库工具入门](sql-database-elastic-scale-get-started.md)  
* [ShardMap 管理](sql-database-elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>从 ShardMapManager 检索 RecoveryManager

第一个步骤是创建 RecoveryManager 实例。 [GetRecoveryManager 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager)返回当前 [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) 实例的恢复管理器。 若要解决分片映射中的任何不一致性，必须先检索特定分片映射的 RecoveryManager。

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

在此示例中，RecoveryManager 已从 ShardMapManager 进行了初始化。 包含 ShardMap 的 ShardMapManager 也已进行了初始化。

由于此应用程序代码会自己处理分片映射，因此在工厂方法中使用的凭据（前面示例中的 smmConnectionString）应该是对连接字符串所引用的 GSM 数据库具有读写权限的凭据。 这些凭据通常与用于对数据相关路由建立连接的凭据不同。 有关详细信息，请参阅[在弹性数据库客户端中使用凭据](sql-database-elastic-scale-manage-credentials.md)。

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>删除分片后从 ShardMap 中删除分片

[DetachShard 方法](https://docs.microsoft.com/previous-versions/azure/dn842083(v=azure.100))可从分片映射中分离给定的分片，并删除与该分片关联的映射。  

* location 参数是分片位置，具体而言，包括要分离的分片的服务器名称和数据库名称。
* shardMapName 参数是分片映射名称。 仅当多个分片映射由同一分片映射管理器管理时才是必需的。 可选。

> [!IMPORTANT]
> 仅当确定所更新映射的范围为空时，才使用此方法。 上述方法不会在数据中检查要移动的范围，因此最好在代码中包含检查操作。

此示例从分片映射删除分片。

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

在删除分片前，分片映射反映了 GSM 中的分片位置。 由于已删除分片，假设这是特意的，而且分片键范围已不再使用。 如果不是这种情况，则可以执行时间点还原。 以从较早的时间点还原分片。 （在这种情况下，请查看以下部分了解如何检测分片的不一致性。）若要恢复，请参阅[时点恢复](sql-database-recovery-using-backups.md)。

由于假设数据库删除操作是有意而为的，最终的管理清理操作是删除分片映射管理器中分片的条目。 这可以防止应用程序无意中将信息写入到非预期的范围。

## <a name="to-detect-mapping-differences"></a>检测映射差异

[DetectMappingDifferences 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences)可选择并返回其中一个分片映射（本地或全局）做为真实源，并调解两个分片映射（GSM 和 LSM）上的映射。

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* location 指定服务器名称和数据库名称。
* *shardMapName* 参数是分片映射名称。 仅当多个分片映射由同一分片映射管理器管理时才是必需的。 可选。

## <a name="to-resolve-mapping-differences"></a>解决映射差异

[ResolveMappingDifferences 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences)可选择其中一个分片映射（本地或全局）做为真实源，并调解两个分片映射（GSM 和 LSM）上的映射。

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* RecoveryToken 参数枚举特定分片的 GSM 与 LSM 之间映射的差异。
* [MappingDifferenceResolution 枚举](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) 指示用于解决分片映射之间差异的方法。
* LSM 包含正确映射时，建议使用 **MappingDifferenceResolution.KeepShardMapping**，因此应该使用分片中的映射。 这通常是因为发生故障转移：分片现在驻留在新的服务器上。 由于必须先从 GSM 中删除分片（使用 RecoveryManager.DetachShard 方法），所以 GSM 上不再存在映射。 因此，必须使用 LSM 重新建立分片映射。

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>还原分片之后将分片附加到 ShardMap

[AttachShard 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard)可将给定的分片附加到分片映射。 然后，它将检测任何分片映射不一致性，并更新映射以匹配分片时间点还原的分片。 假设对数据库也进行了重命名以反映原始数据库名称（在还原分片之前），因为时间点还原默认为追加时间戳的新数据库。

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* *location* 参数是要附加的分片的服务器名称和数据库名称。
* shardMapName 参数是分片映射名称。 仅当多个分片映射由同一分片映射管理器管理时才是必需的。 可选。

此示例将分片添加到最近从较早时间点还原的分片映射。 由于已还原分片（也就是 LSM 中的分片映射），因此该分片可能与 GSM 中的分片条目不一致。 在此示例代码之外，分片已还原并重命名为数据库的原始名称。 由于它已还原，因此假设 LSM 中的映射为受信任的映射。

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>在分片异地故障转移（还原）之后更新分片位置

发生异地故障转移时，使辅助数据库可供写入访问，并成为新的主数据库。 服务器的名称（根据具体的配置，有时还包括数据库的名称）可能与原始主副本不同。 因此，必须修复 GSM 和 LSM 分片的映射条目。 同样地，如果数据库还原到不同的名称或位置，或到较早的时间点，可能会在分片映射中造成不一致。 分片映射管理器会将开放连接分发给正确的数据库。 这种分发基于分片映射中的数据以及作为应用程序请求目标的分片键值。 异地故障转移之后，必须使用准确的服务器名称、数据库名称和已恢复数据库的分片映射更新这些信息。

## <a name="best-practices"></a>最佳实践

异地故障转移和恢复是通常由应用程序的云管理员特意使用 Azure SQL 数据库业务连续性功能管理的操作。 规划业务连续性需要实施相应的流程、过程和措施，以确保业务运营能够持续而不中断。 应该在此工作流中使用 RecoveryManager 类随附的方法，以确保根据采取的恢复操作使 GSM 和 LSM 保持最新状态。 可以执行五个基本步骤来确保在故障转移事件后，GSM 和 LSM 能反映准确的信息。 可将执行这些步骤的应用程序代码集成到现有工具和工作流。

1. 从 ShardMapManager 检索 RecoveryManager。
2. 从分片映射中分离旧分片。
3. 将新分片附加到分片映射，包括新的分片位置。
4. 检测 GSM 和 LSM 之间的映射不一致情况。
5. 解决 GSM 和 LSM 之间的差异，信任 LSM。

此示例将执行以下步骤：

1. 从反映故障转移事件之前分片位置的分片映射中删除分片。
2. 将分片附加到反映新分片位置的分片映射（参数“Configuration.SecondaryServer”是新的服务器名称，但是相同的数据库名称）。
3. 通过检测每个分片的 GSM 与 LSM 之间的映射差异来检索恢复令牌。
4. 通过信任来自每个分片 LSM 的映射解决不一致情况。

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-database-recovery-manager/recovery-manager.png
