---
title: 如何管理 Azure Redis 缓存 | Microsoft Docs
description: 了解如何执行管理任务，如重启 Azure Redis 缓存和为 Azure Redis 缓存计划更新
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: ddb9dd49af4557e6ff8d38110de4a99a9cf6fed7
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687003"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>如何管理 Azure Redis 缓存
本主题介绍如何为 Azure Redis 缓存实例执行管理任务，如[重启](#reboot)和[计划更新](#schedule-updates)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>重新启动
可通过“重新启动”边栏选项卡重新启动缓存的一个或多个节点。 如果有缓存节点发生故障，此重新启动功能可用于测试应用程序的复原能力。

![重新启动](./media/cache-administration/redis-cache-administration-reboot.png)

选择要重新启动的节点，并单击“重新启动”。

![重新启动](./media/cache-administration/redis-cache-reboot.png)

如果高级缓存启用了群集功能，则可选择要重新启动的缓存分片。

![重新启动](./media/cache-administration/redis-cache-reboot-cluster.png)

要重新启动缓存的一个或多个节点，请选择所需节点，并单击“重新启动”。 如果高级缓存启用了群集功能，请选择要重新启动的所需分片，并单击“重新启动”。 几分钟后，所选节点将重新启动，再过几分钟后，又会回到联机状态。

对客户端应用程序的影响因用户重新启动的节点而有所不同。

* **主** - 重新启动主节点时，Azure Redis 缓存将故障转移到副本节点，并将其提升为主节点。 在此故障转移期间，可能会有一个较短的时间间隔无法连接到缓存。
* **从属** - 重新启动从属节点时，通常不会影响缓存客户端。
* **主和从属** - 同时重新启动这两个缓存节点时，缓存中的所有数据将丢失，并且无法连接到缓存，直到主节点重新联机。 如果已配置[数据持久性](cache-how-to-premium-persistence.md)，则在缓存重新联机时会还原最新备份，但在最新备份后发生的所有缓存写入都将丢失。
* **已启用群集的高级缓存的节点** - 重新启动已启用群集的高级缓存的一个或多个节点时，所选节点的行为与重新启动非群集缓存的对应节点时相同。

> [!IMPORTANT]
> 现在所有定价层都可以重新启动。
> 
> 

## <a name="reboot-faq"></a>重新启动常见问题
* [测试应用程序时，应重新启动哪个节点？](#which-node-should-i-reboot-to-test-my-application)
* [能否通过重新启动缓存来清除客户端连接？](#can-i-reboot-the-cache-to-clear-client-connections)
* [如果执行重新启动，是否会丢失缓存中的数据？](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [能否使用 PowerShell、CLI 或其他管理工具重新启动缓存？](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [哪些定价层可以使用重新启动功能？](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>测试我的应用程序时，应重新启动哪个节点？
若要针对缓存的主节点故障测试应用程序的复原能力，请重新启动**主**节点。 若要针对辅助节点的故障测试应用程序的复原能力，请重新启动**从属**节点。 若要针对缓存的总故障测试应用程序的复原能力，请同时重新启动这**两个**节点。

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>能否通过重新启动缓存来清除客户端连接？
能，如果重新启动缓存，将清除所有客户端连接。 当所有客户端连接均已用完（由于客户端应用程序中的逻辑错误或 bug）时，重新启动会很有用。 每个定价层对于不同大小都有不同的[客户端连接数限制](cache-configure.md#default-redis-server-configuration)，达到这些限制后，将不再接受客户端连接。 通过重新启动缓存可以清除所有客户端连接。

> [!IMPORTANT]
> 如果通过重新启动缓存来清除客户端连接，则一旦 Redis 节点重回联机状态，StackExchange.Redis 就会自动重新连接。 如果未解决这一基本问题，客户端连接会继续用完。
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>如果我执行重新启动，是否会丢失缓存中的数据？
如果同时重新启动**主**节点和**从属**节点，则缓存中或该分片中（如果用户使用的是已启用群集的高级缓存）的所有数据都可能会丢失，但这种情况也不一定会发生。 如果已配置 [数据持久性](cache-how-to-premium-persistence.md)，则在缓存重新联机时会还原最新备份，但在进行该备份后发生的所有缓存写入都将丢失。

如果只重新启动其中一个节点，数据通常不会丢失，但仍然存在丢失的可能。 例如，如果重新启动主节点时正在进行缓存写入，则缓存写入的数据会丢失。 发生数据丢失的另一种情况是，重新启动一个节点时，另一个节点恰巧因故障而关闭。 有关数据丢失的可能原因的详细信息，请参阅 [What happened to my data in Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)（Redis 中的数据发生了什么情况？）

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>能否使用 PowerShell、CLI 或其他管理工具重新启动缓存？
能，有关 PowerShell 说明，请参阅[重新启动 Azure Redis 缓存](cache-howto-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis)。

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>哪些定价层可以使用重新启动功能？
所有定价层都可以重新启动。

## <a name="schedule-updates"></a>计划更新
使用“计划更新”边栏选项卡可为高级层缓存指定维护时段。 指定维护时段后，会在此时段内进行任何 Redis 服务器更新。 

> [!NOTE] 
> 维护时段仅适用于 Redis 服务器更新，不适用于任何 Azure 更新或托管缓存的 VM 的操作系统更新。
> 
> 

![计划更新](./media/cache-administration/redis-schedule-updates.png)

要指定维护时段，请勾选合适的日期，并指定每天的维护时段开始时间，最后再单击“确定”。 请注意，维护时段使用 UTC 时间。 

更新的默认最小维护时段为 5 小时。 此值不可以在 Azure 门户中配置，但可以在 PowerShell 中使用 [New-AzRmRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) cmdlet 的 `MaintenanceWindow` 参数进行配置。 有关详细信息，请参阅是否可以使用 PowerShell、CLI 或其他管理工具管理计划的更新？


## <a name="schedule-updates-faq"></a>计划更新常见问题
* [如果不使用计划更新功能，何时进行更新？](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [在计划的维护时段进行哪种类型的更新？](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [能否使用 PowerShell、CLI 或其他管理工具管理计划的更新？](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>如果我不使用计划更新功能，何时进行更新？
如果未指定维护时段，可以随时进行更新。

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>在计划的维护时段进行哪种类型的更新？
仅在计划的维护时段进行 Redis 服务器更新。 维护时段不适用于 Azure 更新或 VM 操作系统更新。

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>有关详细信息，请参阅能否使用 PowerShell、CLI 或其他管理工具管理计划的更新？
可以使用以下 PowerShell cmdlet 管理计划的更新：

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>后续步骤
* 了解更多 [Azure Redis 缓存高级层](cache-premium-tier-intro.md)功能。

