---
title: Azure SQL 数据库服务层级 - 基于 DTU 的购买模型 | Microsoft Docs
description: 了解单一数据库和共用数据库的基于 DTU 的购买模型中的服务层级，以提供计算大小和存储大小。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 09/06/2019
ms.openlocfilehash: 03f16987941f79f9161ccbc172bb2ca1a7139384
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773212"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>基于 DTU 的购买模型中的服务层

基于 DTU 的购买模型中的服务层级根据一系列具有固定随附存储量、固定备份保留期和固定价格的计算大小进行区分。 基于 DTU 的购买模型中的所有服务层级都提供了以最短[停机时间](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)更改计算大小的灵活性；但是，在切换期间，与数据库的连接会短时间丢失，可以使用重试逻辑来缓解这种情况。 单一数据库和弹性池根据服务层级和计算大小按小时计费。

> [!IMPORTANT]
> SQL 数据库托管实例不支持基于 DTU 的购买模型。 有关详细信息，请参阅 [Azure SQL 数据库托管实例](sql-database-managed-instance.md)。
> [!NOTE]
> 有关基于 vCore 的服务层级的信息，请参阅[基于 vCore 的服务层级](sql-database-service-tiers-vcore.md)。 有关区分基于 DTU 的服务层级和基于 vCore 的服务层级的信息，请参阅 [Azure SQL 数据库购买模型](sql-database-purchase-models.md)。

## <a name="compare-the-dtu-based-service-tiers"></a>比较基于 DTU 的服务层级

选择服务层级首要考虑的是业务连续性、存储和性能需求。

||基本|标准|高级|
| :-- | --: |--:| --:|
|目标工作负荷|开发和生产|开发和生产|开发和生产|
|运行时间 SLA|99.99%|99.99%|99.99%|
|备份保留|7 天|35 天|35 天|
|CPU|低|低、中、高|中、高|
|IO 吞吐量（近似） |每个 DTU 1-5 IOPS| 每个 DTU 1-5 IOPS | 每个 DTU 25 IOPS|
|IO 延迟（近似）|5 毫秒（读取），10 毫秒（写入）|5 毫秒（读取），10 毫秒（写入）|2 毫秒（读取/写入）|
|列存储索引 |不可用|S3 及更高版本|支持|
|内存中 OLTP|不可用|不可用|支持|
|||||

> [!NOTE]
> 可以结合 Azure 免费帐户在基本服务层获取免费的 Azure SQL 数据库，以探索 Azure。 有关信息，请参阅[使用 Azure 免费帐户创建托管的云数据库](https://azure.microsoft.com/free/services/sql-database/)。

## <a name="single-database-dtu-and-storage-limits"></a>单一数据库 DTU 和存储限制

单一数据库的计算大小以数据库事务单位 (DTU) 表示，弹性池则以弹性数据库事务单位 (eDTU) 表示。 有关 DTU 和 eDTU 的更多信息，请参阅[基于 DTU 的购买模型](sql-database-purchase-models.md#dtu-based-purchasing-model)？

||基本|标准|高级|
| :-- | --: | --: | --: |
| 最大存储大小 | 2 GB | 1 TB | 4 TB  |
| 最大 DTU | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>弹性池 eDTU、存储和共用数据库限制

| | **基本** | **标准** | **高级** |
| :-- | --: | --: | --: |
| 每个数据库的最大存储大小  | 2 GB | 1 TB | 1 TB |
| 每个池的最大存储大小 | 156 GB | 4 TB | 4 TB |
| 每个数据库的最大 eDTU 数 | 5 | 3000 | 4000 |
| 每个池的最大 eDTU 数 | 1600 | 3000 | 4000 |
| 每个池的数据库数目上限 | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> 除以下区域外，其他所有区域的高级层目前均可提供超过 1 TB 的存储：中国东部、中国北部、德国中部、德国东北部、美国中西部、US DoD 区域和美国政府中部。 在这些区域，高级层中的最大存储限制为 1 TB。  有关详细信息，请参阅[P11-P15 当前限制](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)。  
> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="dtu-benchmark"></a>DTU 基准

使用模拟真实数据库工作负荷的基准检验校准与每个 DTU 度量值相关的物理特性（CPU、内存、IO）。

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>将基准检验结果与实际数据库性能进行关联

请务必了解，所有基准检验只具有代表性和指示性。 使用基准检验应用程序完成的事务率不会与使用其他应用程序可能完成的事务率相同。 基准检验包含不同事务类型的集合，这些事务针对包含一系列表和数据类型的架构运行。 虽然基准检验执行普遍适用于所有 OLTP 工作负荷的相同基本操作，但它并不代表任何特定类别的数据库或应用程序。 基准检验的目标是为在上调或下调计算大小时可预期的数据库相对性能提供合理的指导。 实际上，数据库具有不同的大小和复杂度，会遇到工作负荷的不同组合，并且以不同方式进行响应。 例如，IO 密集型应用程序可能会更快地达到 IO 阈值，或者 CPU 密集型应用程序可能会更快地达到 CPU 限制。 不能保证任何特定数据库在不断增加的负载下会以与基准检验相同的方式扩展。

基准检验及其方法会在下面更详细地说明。

### <a name="benchmark-summary"></a>基准检验摘要

基准检验将度量联机事务处理 (OLTP) 工作负荷中最常发生的基本数据库操作组合的性能。 尽管在设计基准检验时考虑到了云计算，但已将数据库架构、数据填充和事务设计为广泛代表 OLTP 工作负荷中最常用的基本元素。

### <a name="schema"></a>架构

架构设计为具有足够的多样性和复杂度以支持各种操作。 基准检验针对包含六个表的数据库运行。 这些表分为三个类别：固定大小、缩放和增长。 有两个固定大小表、三个缩放性表和一个增长性表。 固定大小表的行数不变。 缩放性表具有一个与数据库性能成正比的基数，但在基准检验期间不会更改。 增长性表的大小调整在初始加载时与缩放性表类似，但随后在运行基准检验的过程中随着插入和删除行基数会更改。

架构包含数据类型（包括整数、数字、字符和日期/时间）的组合。 架构包含主键和辅助键，但不包含任何外键（即，表之间没有任何引用完整性约束）。

数据生成程序会生成初始数据库的数据。 使用不同策略生成整数和数字数据。 在某些情况下，值在某一范围内随机分布。 在其他情况下，会对一组值进行随机排列以确保维持特定分布。 文本字段从加权单词列表中生成以产生具有真实感的数据。

数据库根据“缩放比例”调整大小。 缩放比例（简称为 SF）确定缩放性表和增长性表的基数。 如下面的“用户和步调”部分中所述，数据库大小、用户数和最大性能全都相互成比例缩放。

### <a name="transactions"></a>事务

工作负荷由九种事务类型组成，如下表中所示。 每种事务旨在强调数据库引擎和系统硬件中的特定一组系统特征，与其他事务形成高反差。 此方法可更方便地评估不同组件对总体性能的影响。 例如，事务“Read Heavy”将从磁盘生成大量的读取操作。

| 事务类型 | 描述 |
| --- | --- |
| Read Lite |SELECT；在内存中；只读 |
| Read Medium |SELECT；大多数在内存中；只读 |
| Read Heavy |SELECT；大多数不在内存中；只读 |
| Update Lite |UPDATE；在内存中；读写 |
| Update Heavy |UPDATE；大多数在内存中；读写 |
| Insert Lite |INSERT；在内存中；读写 |
| Insert Heavy |INSERT；大多数不在内存中；读写 |
| DELETE |DELETE；在内存中和不在内存中的组合；读写 |
| CPU Heavy |SELECT；在内存中；相对较高的 CPU 负载；只读 |

### <a name="workload-mix"></a>工作负荷组合

从具有以下整体组合的加权分布中随机选择事务。 整体组合的读/写比率大约为 2:1。

| 事务类型 | 组合百分比 |
| --- | --- |
| Read Lite |35 |
| Read Medium |20 |
| Read Heavy |5 |
| Update Lite |20 |
| Update Heavy |3 |
| Insert Lite |3 |
| Insert Heavy |2 |
| Delete |2 |
| CPU Heavy |10 |

### <a name="users-and-pacing"></a>用户和步调

基准检验工作负荷是由一个工具驱动的，该工具通过一组连接提交事务以模拟大量并发用户的行为。 虽然所有连接和事务都是由计算机生成的，但为简单起见我们将这些连接称为“用户”。 虽然每个用户都独立于所有其他用户进行操作，但所有用户都执行相同的步骤循环，如下所示：

1. 建立数据库连接。
2. 重复执行，直到收到退出通知：
   - 随机选择事务（从加权分布中）。
   - 执行所选的事务并测量响应时间。
   - 等待步调延迟。
3. 关闭数据库连接。
4. 退出。

随机选择了步调延迟（在步骤 2c 中），但却使用了平均值为 1.0 秒的分布。 因此，平均而言，每个用户每秒最多可以生成一个事务。

### <a name="scaling-rules"></a>缩放规则

用户数由数据库大小（以缩放比例单位数表示）确定。 每个五个缩放比例单位有一个用户。 由于步调延迟，一个用户平均每秒最多可以生成一个事务。

例如，比例因子为 500 (SF = 500) 的数据库将具有 100 个用户，并且可以实现的最大速率为 100 TPS。 若要驱动更高的 TPS 速率，需要更多的用户和更大的数据库。

### <a name="measurement-duration"></a>度量持续时间

有效地运行基准检验需要稳定状态度量持续时间至少为 1 小时。

### <a name="metrics"></a>指标

基准检验中的关键指标是吞吐量和响应时间。

- 吞吐量是基准检验中至关重要的性能度量值。 吞吐量以每时间单位的事务数的形式报告，计算所有事务类型。
- 响应时间是性能可预测性的度量值。 响应时间约束因服务等级而异，服务等级越高，响应时间要求越更严格，如下所示。

| 服务等级 | 吞吐量度量值 | 响应时间要求 |
| --- | --- | --- |
| 高级 |每秒创建的事务数 |0\.5 秒时达到 95% |
| 标准 |每分钟事务数 |1\.0 秒时达到 90% |
| 基本 |每小时事务数 |2\.0 秒时达到 80% |

## <a name="next-steps"></a>后续步骤

- 有关适用于单一数据库的特定计算大小和存储大小选项的详细信息，请参阅[适用于单一数据库的 SQL 数据库基于 DTU 的资源限制](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)。
- 有关适用于弹性池的特定计算大小和存储大小选项的详细信息，请参阅 [SQL 数据库基于 DTU 的资源限制](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)。
