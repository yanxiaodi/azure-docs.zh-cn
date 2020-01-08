---
title: SQL 数据库灾难恢复演练 | Microsoft 文档
description: 了解使用 Azure SQL 数据库执行灾难恢复演练的指南和最佳做法。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 2923ae8b9b25932ae214cfa45780dffb8780dd39
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568854"
---
# <a name="performing-disaster-recovery-drill"></a>执行灾难恢复演练

建议定期对恢复工作流执行应用程序就绪性验证。 验证应用程序的行为以及数据丢失和/或涉及到故障转移的中断所造成的影响，是一种良好的工程实践。 许多行业标准在涉及到业务连续性认证方面也会提出此要求。

执行灾难恢复演练的操作包括：

* 模拟数据层中断
* 恢复
* 验证恢复后的应用程序完整性

根据[针对业务连续性设计应用程序](sql-database-business-continuity.md)的方式，用于执行演练的工作流会有所不同。 本文讨论在 Azure SQL 数据库上下文中执行灾难恢复演练的最佳实践。

## <a name="geo-restore"></a>异地还原

若要防止执行灾难恢复演练时发生潜在的数据丢失，请通过创建生产环境的副本在测试环境中执行演练，并使用测试环境来验证应用程序的故障转移工作流。

### <a name="outage-simulation"></a>中断模拟

若要模拟中断，可重命名源数据库。 此名称更改会导致应用程序连接失败。

### <a name="recovery"></a>恢复

* 根据[此处](sql-database-disaster-recovery.md)所述，在另一台服务器中执行数据库异地还原。
* 更改应用程序配置以连接到已恢复的数据库，并按照[在恢复后配置数据库](sql-database-disaster-recovery.md)指南完成恢复。

### <a name="validation"></a>验证

通过验证恢复后的应用程序完整性（包括连接字符串、登录名、基本功能测试，或标准应用程序验收过程的其他验证部分）来完成演练。

## <a name="failover-groups"></a>故障转移组

对于使用故障转移组保护的数据库，演练过程包括按计划故障转移到辅助服务器。 计划的故障转移可确保在切换角色后，故障转移组中的主数据库和辅助数据库保持同步。 与非计划的故障转移不同，此操作不会导致数据丢失，因此可以在生产环境中执行演练。

### <a name="outage-simulation"></a>中断模拟

若要模拟中断，可以禁用已连接到数据库的 Web 应用程序或虚拟机。 此中断模拟会导致 Web 客户端连接失败。

### <a name="recovery"></a>恢复

* 确保 DR 区域中的应用程序配置指向以前的辅助数据库，故障转移后，该数据库将成为完全可访问的新主数据库。
* 从辅助服务器启动故障转移组的[计划故障转移](scripts/sql-database-setup-geodr-and-failover-database-powershell.md)。
* 根据在[恢复后配置数据库](sql-database-disaster-recovery.md)指南完成恢复。

### <a name="validation"></a>验证

通过验证恢复后的应用程序完整性（包括连接性、基本功能测试或验收过程所需的其他验证部分）来完成演练。

## <a name="next-steps"></a>后续步骤

* 若要了解业务连续性方案，请参阅[连续性方案](sql-database-business-continuity.md)。
* 若要了解 Azure SQL 数据库的自动备份，请参阅 [SQL 数据库自动备份](sql-database-automated-backups.md)
* 若要了解如何使用自动备份进行恢复，请参阅[从服务启动的备份中还原数据库](sql-database-recovery-using-backups.md)。
* 要了解更快的恢复选项，请参阅[活动异地复制](sql-database-active-geo-replication.md)和[自动故障转移组](sql-database-auto-failover-group.md)。
