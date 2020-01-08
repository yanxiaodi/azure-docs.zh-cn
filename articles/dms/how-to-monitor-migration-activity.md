---
title: 使用 Azure 数据库迁移服务监视迁移活动 | Microsoft Docs
description: 了解如何使用 Azure 数据库迁移服务来监视迁移活动。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 325bbee3f3d5ad5097f710cb56fe03baff97388a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60532817"
---
# <a name="monitor-migration-activity"></a>监视迁移活动
本文介绍如何在数据库级别和表级别监视迁移进度。

## <a name="monitor-at-the-database-level"></a>在数据库级别监视
若要在数据库级别监视活动，请查看数据库级别的边栏选项卡：

![数据库级别的边栏选项卡](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> 选择数据库超链接会显示表的列表及其迁移进度。

下表列出了数据库级别边栏选项卡中的字段，并描述了与每个字段关联的各个状态值。

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>字段名称</strong></th>
      <th><strong>字段子状态</strong></th>
      <th><strong>说明</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>活动状态</strong></td>
      <td>正在运行</td>
      <td>迁移活动正在运行。</td>
    </tr>
    <tr>
      <td>已成功</td>
      <td>迁移活动成功且未出现问题。</td>
    </tr>
    <tr>
      <td>出错</td>
      <td>迁移失败。 选择迁移详细信息下的“查看错误详细信息”链接可以查看完整的错误消息。</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Status</strong></td>
      <td>正在初始化</td>
      <td>DMS 正在设置迁移管道。</td>
    </tr>
    <tr>
      <td>正在运行</td>
      <td>DMS 管道正在运行并执行迁移。</td>
    </tr>
    <tr>
      <td>完成</td>
      <td>迁移已完成。</td>
    </tr>
    <tr>
      <td>已失败</td>
      <td>迁移失败。 单击迁移详细信息可查看迁移错误。</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>迁移详细信息</strong></td>
      <td>正在初始化迁移管道</td>
      <td>DMS 正在设置迁移管道。</td>
    </tr>
    <tr>
      <td>正在进行完整数据加载</td>
      <td>DMS 正在执行初始加载。</td>
    </tr>
    <tr>
      <td>已准备好交接</td>
      <td>初始加载完成后，DMS 会将数据库标记为已准备好交接。 用户应检查数据是否已跟上连续同步的进度。</td>
    </tr>
    <tr>
      <td>已应用所有更改</td>
      <td>初始加载和连续同步已完成。 成功交接数据库后，也会出现此状态。</td>
    </tr>
    <tr>
      <td>查看错误详细信息</td>
      <td>单击该链接会显示错误详细信息。</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>持续时间</strong></td>
      <td>不适用</td>
      <td>从迁移活动初始化到迁移完成或迁移出错的总时间。</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>表级别的监视 – 快速摘要
若要在表级别监视活动，请查看表级别的边栏选项卡。 该边栏选项卡的上半部分详细显示在完整加载和增量更新过程中迁移的行数。 

该边栏选项卡的下半部分列出一些表格，显示迁移进度的快速摘要。

![表级别的边栏选项卡 - 快速摘要](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

下表描述了表级别详细信息中显示的字段。

| 字段名        | 描述       |
| ------------- | ------------- |
| **完整加载已完成**      | 已完成完整数据加载的表数。 |
| **完整加载已排队**      | 正在排队等待完整加载的表数。      |
| **完整加载正在进行** | 失败的表数。      |
| **增量更新**      | 行中已应用到目标的变更数据捕获 (CDC) 更新数。 |
| **增量插入**      | 行中已应用到目标的 CDC 插入操作数目。      |
| **增量删除** | 行中已应用到目标的 CDC 删除操作数目。      |
| **挂起的更改**      | 行中仍在等待应用到目标的 CDC 数。 |
| **应用的更改**      | 行中已应用到目标的 CDC 更新、插入和删除操作总数。      |
| **处于错误状态的表** | 在迁移期间处于“错误”状态的表数。 表可能进入错误状态的部分示例包括：在目标中识别到重复项，或者数据不兼容，无法载入目标表。      |

## <a name="monitor-at-table-level--detailed-summary"></a>表级别的监视 – 详细摘要
有两个选项卡显示了执行完整加载和增量数据同步时的迁移进度。
    
![“完整加载”选项卡](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![“增量数据同步”选项卡](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

下表描述了表级别迁移进度中显示的字段。

| 字段名        | 描述       |
| ------------- | ------------- |
| **状态 - 正在同步**      | 连续同步正在运行。 |
| **插入**      | 行中已应用到目标的 CDC 插入操作数目。      |
| **更新** | 行中已应用到目标的 CDC 更新操作数目。      |
| **删除**      | 行中已应用到目标的 CDC 删除操作数目。 |
| **已应用的操作总数**      | 行中已应用到目标的 CDC 更新、插入和删除操作总数。 |
| **数据错误** | 此表中发生的数据错误数。 错误的一些示例包括 *511：无法创建大小为 %d 的行，该行大于允许的最大行大小 %d，8114：将数据类型 %ls 转换为 %ls 时出错。*  客户应查询 Azure 目标中的 dms_apply_exceptions 表，以查看错误详细信息。    |

> [!NOTE]
> 交接数据库或重新开始迁移后，“插入”、“更新”、“删除”和“已应用的操作总数”的 CDC 值可能会减小。

## <a name="next-steps"></a>后续步骤
- 在 Microsoft [Database Migration Guide](https://datamigration.microsoft.com/)（Microsoft 数据库迁移指南）中查看迁移指南。