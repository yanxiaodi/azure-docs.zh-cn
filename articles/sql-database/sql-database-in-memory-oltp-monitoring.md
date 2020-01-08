---
title: 监视 XTP 内存中存储 | Microsoft Docs
description: 估算和监视 XTP 内存中存储用量与容量；解决容量错误 41823
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 1c5a57f634c01cc42934a98decd8f392334dede6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567985"
---
# <a name="monitor-in-memory-oltp-storage"></a>监视内存中 OLTP 存储

使用[内存中 OLTP](sql-database-in-memory.md) 时，内存优化表中的数据和表变量将驻留在内存中 OLTP 存储内。 每个“高级”和“业务关键”服务层级都具有最大内存中 OLTP 存储大小。 请参阅[基于 DTU 的资源限制 - 单一数据库](sql-database-dtu-resource-limits-single-databases.md)、[基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-elastic-pools.md)、[基于 vCore 的资源限制 - 单一数据库](sql-database-vcore-resource-limits-single-databases.md)和[基于 vCore 的资源限制 - 弹性池](sql-database-vcore-resource-limits-elastic-pools.md)。

一旦超过此限制，可能会导致插入和更新操作失败，出现错误 41823（针对单一数据库）和错误 41840（针对弹性池）。 到时，需要删除数据以回收内存，或升级数据库的服务层级或计算大小。

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>确定数据是否在内存中 OLTP 存储容量限制范围内

确定不同服务层级的存储上限。 请参阅[基于 DTU 的资源限制 - 单一数据库](sql-database-dtu-resource-limits-single-databases.md)、[基于 DTU 的资源限制 - 弹性池](sql-database-dtu-resource-limits-elastic-pools.md)、[基于 vCore 的资源限制 - 单一数据库](sql-database-vcore-resource-limits-single-databases.md)和[基于 vCore 的资源限制 - 弹性池](sql-database-vcore-resource-limits-elastic-pools.md)。

估计内存优化表的内存要求，如同在 Azure SQL 数据库中估计 SQL Server 的内存要求一样。 花费几分钟时间查看 [MSDN](https://msdn.microsoft.com/library/dn282389.aspx) 上的相关文章。

表和表变量行以及索引都将计入最大用户数据大小。 此外，ALTER TABLE 需要足够的空间来创建新版的完整表及其索引。

## <a name="monitoring-and-alerting"></a>监视和警报
可以在 [Azure 门户](https://portal.azure.com/)中，通过计算大小的存储上限百分比来监视内存中存储用量： 

1. 在“数据库”边栏选项卡上，找出“资源使用率”框并单击“编辑”。
2. 选择指标 `In-Memory OLTP Storage percentage`。
3. 要添加警报，请单击“资源使用率”框以打开“度量值”边栏选项卡，并单击“添加警报”。

或者，使用以下查询来显示内存中存储的使用率：

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>更正超过内存中 OLTP 存储的情况 - 错误 41823 和 41840

数据库达到内存中 OLTP 存储上限会导致 INSERT、UPDATE、ALTER 和 CREATE 操作失败，出现错误消息 41823（针对单一数据库）或错误 41840（针对弹性池）。 这两个错误均会导致活动的事务中止。

错误消息 41823 和 41840 指示数据库或池中的内存优化表和表变量已达到最大的内存中 OLTP 存储大小。

若要解决此错误，请执行以下操作之一：

* 从内存优化表中删除数据，为此，可以将数据卸载到传统的基于磁盘的表；或者，
* 将服务层级升级到具有足够内存中存储的服务层级，使保存需要保留在内存优化表中的数据。

> [!NOTE] 
> 在极少数情况下，错误 41823 和 41840 可能是暂时的，即有足够的可用内存中 OLTP 存储，重试后该操作成功。 因此，我们建议既要监视总体的可用内存中 OLTP 存储，又要在首次遇到错误 41823 或 41840 时重试。 有关重试逻辑的详细信息，请参阅[内存中 OLTP 的冲突检测和重试逻辑](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic)。

## <a name="next-steps"></a>后续步骤
有关监视指南，请参阅[使用动态管理视图监视 Azure SQL 数据库](sql-database-monitoring-with-dmvs.md)。
