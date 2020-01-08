---
title: 在灾难恢复期间使用 Azure Site Recovery 进行故障转移 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 服务在灾难恢复期间对 VM 和物理服务器进行故障转移。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/30/2019
ms.author: raynew
ms.openlocfilehash: da55d83665792f6ea2f4c78aa2a6c3ca26c39233
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383186"
---
# <a name="fail-over-vms-and-physical-servers"></a>对 VM 和物理服务器进行故障转移 

本文介绍如何故障转移受 Site Recovery 保护的虚拟机和物理服务器。

## <a name="prerequisites"></a>先决条件
1. 执行故障转移之前，请先执行[测试故障转移](site-recovery-test-failover-to-azure.md)，确保一切按预期工作。
1. 执行故障转移之前，请在目标位置[准备好网络](site-recovery-network-design.md)。  

通过下表了解 Azure Site Recovery 提供的故障转移选项。 所列选项针对不同的故障转移方案。

| 应用场景 | 应用程序恢复要求 | Hyper V 工作流 | VMware 工作流
|---|--|--|--|
|由于即将到来的数据中心故障时间而计划的故障转移| 执行计划活动时应用程序的零数据丢失| 对于 Hyper-V，ASR 以用户指定的复制频率复制数据。 使用计划的故障转移在启动故障转移之前覆盖频率并复制最终更改。 <br/> <br/> 1.根据你的业务变更管理流程规划维护时段。 <br/><br/> 2.通知用户即将发生的停机时间。 <br/><br/> 3.使面向用户的应用程序脱机运行。<br/><br/>4.使用 ASR 门户启动计划的故障转移。 本地虚拟机将自动关闭。<br/><br/>有效的应用程序数据丢失 = 0 <br/><br/>对于希望使用较早恢复点的用户，还可以提供保留期内的恢复点日志。 （对于 HYPER-V，保留期为 24 小时）。 如果复制已在保留期的时间范围之外停止，则客户仍可以使用最新的可用恢复点进行故障转移。 | 对于 VMware，ASR 使用 CDP 持续复制数据。 故障转移可以让用户选择故障转移到最新数据（包括应用程序关闭后）<br/><br/> 1.根据变更管理流程规划维护时段 <br/><br/>2.通知用户即将到来的故障时间 <br/><br/>3.使面向用户的应用程序脱机运行。<br/><br/>4.在应用程序脱机后，使用 ASR 门户启动到最新点的计划故障转移。 使用门户上的 "计划的故障转移" 选项，并选择要进行故障转移的最新点。 本地虚拟机将自动关闭。<br/><br/>有效的应用程序数据丢失 = 0 <br/><br/>对于希望使用较早恢复点的用户，可以提供保留期内的恢复点日志。 （对于 VMware，保留期为 72 小时）。 如果复制已在保留期的时间范围之外停止，则客户仍可以使用最新的可用恢复点进行故障转移。
|由于计划外的数据中心故障时间（自然或 IT 灾难）而执行的故障转移 | 应用程序的最小数据丢失 | 1.启动组织的 BCP 计划 <br/><br/>2.使用 ASR 门户启动到最新点或保留期（日志）内的某个点的计划外故障转移。| 1.启动组织的 BCP 计划。 <br/><br/>2.使用 ASR 门户启动到最新点或保留期（日志）内的某个点的计划外故障转移。


## <a name="run-a-failover"></a>运行故障转移
本过程介绍如何根据[恢复计划](site-recovery-create-recovery-plans.md)运行故障转移。 或者，也可以通过“复制的项”页针对单个虚拟机或物理服务器运行故障转移，如[此处](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure)所述。


![故障转移](./media/site-recovery-failover/Failover.png)

1. 选择“**恢复计划**” > “*recoveryplan_name*”。 单击“故障转移”
2. 在“故障转移”屏幕上，选择要故障转移到的“恢复点”。 可以使用以下选项之一：
   1. **最新**：此选项通过首先处理已发送到 Site Recovery 服务的所有数据来启动作业。 处理数据可为每个虚拟机创建一个恢复点。 虚拟机在故障转移期间使用此恢复点。 此选项提供最低 RPO（恢复点目标），因为故障转移后创建的虚拟机包含触发故障转移时已复制到 Site Recovery 服务的所有数据。
   1. **最新处理**：此选项将恢复计划的所有虚拟机故障转移到已由 Site Recovery 服务处理的最新恢复点。 对虚拟机执行测试故障转移时，还会显示最新处理的恢复点的时间戳。 如果要针对恢复计划执行故障转移，可以转到单个虚拟机，并查看“最新恢复点”磁贴来获取此信息。 由于不会花费时间来处理未经处理的数据，此选项是 RTO（恢复时间目标）较低的故障转移选项。
   1. **最新的应用一致**：此选项将恢复计划的所有虚拟机故障转移到已由 Site Recovery 服务处理的最新应用程序一致恢复点。 对虚拟机执行测试故障转移时，还会显示最新应用一致性恢复点的时间戳。 如果要针对恢复计划执行故障转移，可以转到单个虚拟机，并查看“最新恢复点”磁贴来获取此信息。
   1. **最新多 VM 已处理**：此选项仅可用于至少有一个虚拟机已启用多 VM 一致性的恢复计划。 属于复制组的虚拟机将故障转移到最新通用多 VM 一致恢复点。 其他虚拟机将故障转移到其最近处理的恢复点。  
   1. **最新多 VM 应用一致**：此选项仅可用于至少有一个虚拟机已启用多 VM 一致性的恢复计划。 属于复制组的虚拟机将故障转移到最新通用多 VM 应用程序一致恢复点。 其他虚拟机将故障转移到其最新的应用程序一致恢复点。
   1. **自定义**：如果正在对虚拟机执行测试故障转移，则可以使用此选项故障转移到特定恢复点。

      > [!NOTE]
      > 仅当故障转移到 Azure 时，选择恢复点的选项才可用。
      >
      >


1. 如果恢复计划中的某些虚拟机在上一个轮次中已故障转移，并且这些虚拟机在源位置和目标位置现已处于活动状态，则可以使用“更改方向”选项来确定故障转移的方向。
1. 如果要故障转移到 Azure 并且为云启用了数据加密（仅当通过 VMM 服务器保护 Hyper-V 虚拟机时才适用），请在“加密密钥”中，选择在 VMM 服务器上进行设置期间启用数据加密时颁发的证书。
1. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源虚拟机，请选择“在开始故障转移之前关闭计算机”。 即使关闭失败，故障转移也仍会继续。  

    > [!NOTE]
    > 如果 Hyper-V 虚拟机受到保护，则关闭选项还会在触发故障转移之前，尝试同步尚未发送到服务的本地数据。
    >
    >

1. 可以在“作业”页上跟踪故障转移进度。 即使在非计划的故障转移期间发生错误，恢复计划也会运行到完成为止。
1. 故障转移后，请登录到虚拟机来对它进行验证。 如果想要切换到虚拟机的另一个恢复点，可以使用“更改恢复点”选项。
1. 如果故障转移后的虚拟机符合要求，可以**提交**故障转移。 提交操作会删除服务中提供的所有恢复点，并且“更改恢复点”选项将不再可用。

## <a name="planned-failover"></a>计划的故障转移
使用 Site Recovery 保护的虚拟机/物理服务器也支持计划的故障转移。 计划的故障转移是一个不会丢失任何数据的故障转移选项。 触发计划内故障转移时，首先会关闭源虚拟机，同步最新数据，然后再触发故障转移。

> [!NOTE]
> 在不同的本地站点之间故障转移 Hyper-V 虚拟机时，要返回到主要本地站点，必须先将虚拟机**反向复制**回到主站点，然后再触发故障转移。 如果主虚拟机不可用，则在开始**反向复制**之前，必须从备份还原虚拟机。   
 
 
## <a name="failover-job"></a>故障转移作业

![故障转移](./media/site-recovery-failover/FailoverJob.png)

触发故障转移时，将执行以下步骤：

1. 先决条件检查：此步骤确保满足故障转移所需的所有条件
1. 故障转移：此步骤处理并准备好数据，以便能够基于这些数据创建 Azure 虚拟机。 如果选择了“最新”恢复点，此步骤将基于发送到服务的数据创建恢复点。
1. 开始：此步骤使用上一步骤中处理的数据创建 Azure 虚拟机。

> [!WARNING]
> **不要取消正在进行的故障转移**：开始故障转移之前，将停止虚拟机的复制。 如果**取消**正在进行的作业，故障转移会停止，但虚拟机不会开始复制。 无法再次启动复制。
>
>

## <a name="time-taken-for-failover-to-azure"></a>故障转移至 Azure 的耗时

在某些情况下，虚拟机的故障转移需要额外的中间步骤，中间步骤通常耗费约 8 到 10 分钟才能完成。 在以下情况下，故障转移所需的时间比通常更多：

* VMware 虚拟机所使用的移动服务版本早于 9.8
* 物理服务器
* VMware Linux 虚拟机
* 作为物理服务器受到保护的 Hyper-V 虚拟机
* VMware 虚拟机，其中下列驱动程序不作为启动驱动程序
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* 未启用 DHCP 服务的 VMware 虚拟机（无论它们使用 DHCP 还是使用静态 IP 地址）

在其他所有情况下，此中间步骤都非必需，因而故障转移的耗时会减少。

## <a name="using-scripts-in-failover"></a>在故障转移中使用脚本
在执行故障转移时，你可能想要将某些操作自动化。 在[恢复计划](site-recovery-create-recovery-plans.md)中使用脚本或 [Azure 自动化 Runbook](site-recovery-runbook-automation.md) 可以实现此目的。

## <a name="post-failover-considerations"></a>故障转移后注意事项
故障转移后，可能需要考虑以下建议：
### <a name="retaining-drive-letter-after-failover"></a>在故障转移后保留驱动器号
Azure Site Recovery 会处理驱动器号的保留。 [阅读更多信息](vmware-azure-exclude-disk.md#example-1-exclude-the-sql-server-tempdb-disk)，了解选择排除某些磁盘时它是如何完成的。

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

如果想要在故障转移后使用 RDP/SSH 连接到 Azure VM，请遵照[此处](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)表格中汇总的要求。

按照[此处](site-recovery-failover-to-azure-troubleshoot.md)所述步骤对故障转移后的任何连接问题进行故障排除。


## <a name="next-steps"></a>后续步骤

> [!WARNING]
> 故障转移虚拟机并且本地数据中心可用后，应该在本地数据中心[**重新保护**](vmware-azure-reprotect.md) VMware 虚拟机。

使用[**计划的故障转移**](hyper-v-azure-failback.md)选项可将 Hyper-V 虚拟机从 Azure **故障回复**到本地。

如果已将 Hyper-V 虚拟机故障转移到 VMM 服务器管理的另一个本地数据中心并且主数据中心可用，请使用“反向复制”选项开始复制回到主数据中心。
