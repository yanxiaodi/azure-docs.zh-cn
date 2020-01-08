---
title: 有关如何使用 Azure Site Recovery 进行 Azure 到 Azure 灾难恢复的常见问题
description: 本文回答了有关如何使用 Azure Site Recovery 将 Azure VM 灾难恢复到另一 Azure 区域的常见问题
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 04/29/2019
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: cd1c6cf0ff5a963720df7420a5d983d24e7b4d3e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861388"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>常见问题：Azure 到 Azure 的灾难恢复

本文回答了有关如何使用 [Site Recovery](site-recovery-overview.md) 将 Azure VM 灾难恢复到另一 Azure 区域的常见问题。 


## <a name="general"></a>常规

### <a name="how-is-site-recovery-priced"></a>Site Recovery 如何计费？
请查看 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)。
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery 的免费层是如何工作的？
每个使用 Azure Site Recovery 保护的实例在其保护期的前 31 天内均享受免费。 从第 32 天起，将按以上收费率对实例的保护进行计费。
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在前 31 天的期限内，会产生其他 Azure 费用吗？
是，尽管受保护实例的 Azure Site Recovery 在前 31 天内为免费，但你可能会产生 Azure 存储器、存储事务和数据传输的费用。 恢复后的虚拟机也可能会产生 Azure 计算费用。 可以在[此处](https://azure.microsoft.com/pricing/details/site-recovery)获取有关定价的完整详细信息

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>在哪里可以找到 Azure VM 灾难恢复的最佳做法？ 
1. [了解 Azure 到 Azure 体系结构](azure-to-azure-architecture.md)
2. [查看支持和不支持的配置](azure-to-azure-support-matrix.md)
3. [为 Azure VM 设置灾难恢复](azure-to-azure-how-to-enable-replication.md)
4. [运行测试故障转移](azure-to-azure-tutorial-dr-drill.md)
5. [故障转移和故障回复到主要区域](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>如何保证目标区域的容量？
Site Recovery 团队会与 Azure 容量管理团队合作，规划足够的基础结构容量，并确保受 Site Recovery 保护的 VM 在启动故障转移时可以成功部署到目标区域。

## <a name="replication"></a>复制

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>是否可以复制通过 Azure 磁盘加密启用的 VM？

是的，Site Recovery 支持启用了 Azure 磁盘加密（ADE）的 Vm 的灾难恢复。 启用复制时，所有所需的磁盘加密密钥和机密都将从源区域复制到用户上下文中的目标区域。 如果你没有相应的权限，则可以将随时可用的脚本传递给安全管理员，以复制密钥和机密。

- Site Recovery 支持运行 Windows 的 Azure Vm 的 ADE。
- Site recovery 支持 ADE 版本0.1，其中架构使用 Azure Active Directory （AAD），版本1.1，无 AAD。 [了解详细信息](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata)。
- ADE 版本1.1，必须将 Windows Vm 用于托管磁盘。
- [详细了解](azure-to-azure-how-to-enable-replication-ade-vms.md)如何为加密的 vm 启用复制。



### <a name="can-i-replicate-vms-to-another-subscription"></a>是否可将 VM 复制到另一个订阅？
是的，可将 Azure VM 复制到同一 Azure AD 租户中的不同订阅。
配置[跨订阅](https://azure.microsoft.com/blog/cross-subscription-dr)的灾难恢复非常简单。 可以在复制时选择另一个订阅。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>是否可将区域固定的 Azure VM 复制到另一个区域？
是的，可[将区域固定的 VM 复制到](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)另一个区域。

### <a name="can-i-exclude-disks"></a>是否可以排除磁盘？

是的，可以在保护时使用 PowerShell 排除磁盘。 有关详细信息，请参阅[此文章](azure-to-azure-exclude-disks.md)

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>是否可以向复制的 VM 添加新磁盘并为这些磁盘启用复制？

可以。使用托管磁盘的 Azure VM 允许这样做。 将新磁盘添加到启用了复制的 Azure VM 时, VM 的复制运行状况将显示警告, 并显示一个说明, 指出 VM 上的一个或多个磁盘可用于保护。 可以为添加的磁盘启用复制。
- 如果为添加的磁盘启用保护，此警告会在初始复制后消失。
- 如果选择不为磁盘启用复制，则可选择关闭此警告。
- 对某个 VM 进行故障转移时，如果向其添加了磁盘并为该磁盘启用了复制，则复制点会显示可用于恢复的磁盘。 例如，如果 VM 有一个磁盘，你添加了一个新的磁盘，则在添加磁盘之前创建的复制点会显示复制点包含“1 个磁盘（共 2 个）”。

Site Recovery 不支持复制的 VM 中的磁盘 "热删除"。 如果删除某个 VM 磁盘，则需先禁用该 VM 的复制，然后为其重新启用复制。


### <a name="how-often-can-i-replicate-to-azure"></a>可以多久复制到 Azure 一次？
将 Azure VM 复制到另一个 Azure 区域时，复制是持续性的。 有关详细信息，请参阅 [Azure 到 Azure 复制体系结构](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)。

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>是否可以在某个区域中复制虚拟机？ 我需要使用此方法来迁移 VM。
无法使用 Azure 到 Azure 的灾难恢复解决方案在区域中复制 VM。

### <a name="can-i-replicate-vms-to-any-azure-region"></a>是否可将 VM 复制到任意 Azure 区域？
使用 Site Recovery，可以在同一地理群集中的任意两个区域之间复制和恢复 VM。 地理群集的定义考虑到了数据延迟和主权。 有关详细信息，请参阅 Site Recovery 的[区域支持矩阵](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery 需要建立 Internet 连接？

否，Site Recovery 不需要建立 Internet 连接。 但它需要访问 Site Recovery URL 和 IP 范围，如[此文](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)中所述。

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>是否可以为不同的层复制具有不同资源组的应用程序？
是的，你可以复制应用程序并且也在另一个资源组中保留灾难恢复配置。
例如，如果你有一个应用程序，并且每层的应用、数据库和 Web 位于不同的资源组中，则必须三次单击[复制向导](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication)来保护所有层。 Site Recovery 会将这三个层复制到三个不同的资源组中。

## <a name="replication-policy"></a>复制策略

### <a name="what-is-a-replication-policy"></a>什么是复制策略？
复制策略定义恢复点历史记录保留期和应用一致性快照频率的设置。 默认情况下，Azure Site Recovery 使用以下默认设置创建新的复制策略：

* 恢复点历史记录的保留期为 24 小时。
* 应用一致性快照的频率为 60 分钟。

[了解详细信息](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings)。

### <a name="what-is-a-crash-consistent-recovery-point"></a>什么是崩溃一致性恢复点？
崩溃一致性恢复点表示在 VM 发生崩溃或者创建快照期间从服务器上拔下电源线时磁盘上的数据。 它不包括创建快照时内存中存在的任何数据。

目前，大多数应用程序都可以从崩溃一致性快照正常恢复。 对于无数据库的操作系统以及文件服务器、DHCP 服务器、打印服务器等应用程序而言，崩溃一致性恢复点通常已足够。

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>崩溃一致性恢复点生成的频率是多少？
Site Recovery 每隔 5 分钟创建崩溃一致性恢复点。

### <a name="what-is-an-application-consistent-recovery-point"></a>什么是应用程序一致性恢复点？
应用程序一致性恢复点是从应用程序一致性快照创建的。 应用程序一致性恢复点捕获的数据与崩溃一致性快照相同，此外还会加上内存中的数据，以及所有正在进行的事务。
由于包含额外的内容，应用程序一致性快照涉及的操作最多，且执行时间最长。 我们建议对数据库操作系统以及 SQL Server 等应用程序使用应用程序一致性恢复点。

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>应用程序一致性恢复点对应用程序性能有何影响？
由于应用程序一致性恢复点会捕获内存中的以及正在处理的所有数据，因此它会要求框架（例如 Windows 上的 VSS）静止应用程序。 当工作负荷已经非常繁忙时，如果非常频繁地这样做，可能会影响性能。 对于非数据库工作负荷，通常建议不要对应用程序一致性恢复点使用低频率，即使对于数据库工作负荷，采用 1 小时的频率也足够了。

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>应用程序一致性恢复点生成的最低频率是多少？
Site Recovery 可以创建一个应用程序一致性恢复点，其最小频率为 1 小时。

### <a name="how-are-recovery-points-generated-and-saved"></a>如何生成和保存恢复点？
若要了解 Site Recovery 如何生成恢复点，让我们查看一个复制策略的示例，其中，恢复点保留期为 24 小时，应用一致性快照的频率为 1 小时。

Site Recovery 每隔 5 分钟创建崩溃一致性恢复点。 用户无法更改此频率。 因此，在过去 1 小时，用户可以从 12 个崩溃一致性恢复点和 1 个应用一致性恢复点中进行选择。 一段时间后，Site Recovery 将删除 1 小时以前的所有恢复点，每小时只保存 1 个恢复点。

以下屏幕截图演示了该示例。 在屏幕截图中：

1. 在过去 1 小时内，以 5 分钟的频率创建了恢复点。
2. 对于超出 1 小时的期限，Site Recovery 只保留了 1 个恢复点。

   ![生成的恢复点列表](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>可以恢复到哪个最早的时间点？
可以使用的最早恢复点是 72 小时。

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>如果我的复制策略是创建 24 小时的恢复点，但某个问题导致 Site Recovery 有 24 小时以上无法生成恢复点，将会发生什么情况？ 以前的恢复点是否将丢失？
不会，Site Recovery 将保留以前的所有恢复点。 根据恢复点保留时段（在本示例中为 24 小时），Site Recovery 仅在有新生成的恢复点时才替换最旧的恢复点。 在本示例中，由于某个问题而不会生成新的恢复点，到达保留时段后，所有旧恢复点将保持不变。

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>在 VM 上启用复制后，如何更改复制策略？
转到“Site Recovery 保管库” > “Site Recovery 基础结构” > “复制策略”。 选择要编辑的策略并保存所做的更改。 任何更改也会应用到现有的所有复制。

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>所有恢复点是包含 VM 的完整副本还是差异副本？
生成的第一个恢复点包含完整副本。 任何后续恢复点包含增量更改。

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>增大恢复点保留期是否会增加存储成本？
是的。 如果将保留期从 24 小时增大到 72 小时，则 Site Recovery 将保存额外 48 小时的恢复点。 增加的时间会产生存储费用。 例如，如果单个恢复点包含 10 GB 的增量更改，每 GB 费用为 0.16 美元/月，则每月会产生 1.6 美元 * 48 的额外费用。

## <a name="multi-vm-consistency"></a>多 VM 一致性

### <a name="what-is-multi-vm-consistency"></a>什么是多 VM 一致性？
多 VM 一致性可以确保恢复点在所有复制的虚拟机之间保持一致。
Site Recovery 提供“多 VM 一致性”选项，选择该选项会创建一个复制组，该组中的所有虚拟机可一同复制。
故障转移后，所有虚拟机将具有共享的崩溃一致性恢复点和应用一致性恢复点。
请阅读有关[启用多 VM 一致性](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)的整篇教程。

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>是否可以故障转移多 VM 一致性复制组中的单个虚拟机？
选择“多 VM 一致性”选项即表明应用程序依赖于组中的所有虚拟机。 因此，不允许故障转移单个虚拟机。

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>可以通过多 VM 一致性复制组复制多少个虚拟机？
在一个复制组中，可以一同复制 16 个虚拟机。

### <a name="when-should-i-enable-multi-vm-consistency-"></a>何时应启用多 VM 一致性？
由于多 VM 一致性的 CPU 消耗量较大，启用此功能可能会影响工作负荷性能。 仅当计算机运行相同的工作负荷并且你需要在多个计算机之间保持一致时，才使用此功能。 例如，如果应用程序中有两个 SQL Server 实例和两个 Web 服务器，则你只应为 SQL Server 实例启用多 VM 一致性。


## <a name="failover"></a>故障转移

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>如何保证 Azure VM 在目标区域的容量？
Site Recovery 团队会与 Azure 容量管理团队合作，规划足够的基础结构容量，确保支持灾难恢复的 VM 在启动故障转移时可以成功部署到目标区域中。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？

故障转移不是自动的。 可以在门户中单击一下鼠标来启动故障转移，或者使用 [PowerShell](azure-to-azure-powershell.md) 来触发故障转移。

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>是否可以在故障转移后保留公共 IP 地址？

无法在故障转移后保留生产应用程序的公共 IP 地址。
- 必须为故障转移过程中启动的工作负载分配一个在目标区域中可用的 Azure 公共 IP 资源。
- 可以手动执行此操作，也可以通过恢复计划自动执行它。
- 了解如何[设置故障转移后的公共 IP 地址](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)。  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>在故障转移期间是否可以保留专用 IP 地址？
是的，可以保留专用 IP 地址。 默认情况下，为 Azure VM 启动灾难恢复时，Site Recovery 将根据源资源设置创建目标资源。 - 对于配置有静态 IP 地址的 Azure VM，Site Recovery 会尝试为目标 VM 预配同一 IP 地址（如果未占用）。
了解如何[在故障转移期间保留 IP 地址](site-recovery-retain-ip-azure-vm-failover.md)。

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>故障转移之后，为何向服务器分配新 IP 地址？

故障转移时，Site Recovery 会尽量提供 IP 地址。 如果该地址被另一个虚拟机占用，则 Site Recovery 会将下一个可用 IP 地址设为目标。
详细了解如何[设置 VNet 的网络映射和 IP 寻址](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)。

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>什么是**最新（最低 RPO）** 恢复点？
“最新(最低 RPO)”选项首先处理已发送到 Site Recovery 服务的所有数据，为每个 VM 创建恢复点，然后将其故障转移到该恢复点。 此选项提供最低的恢复点目标 (RPO)，因为故障转移后创建的 VM 具有触发故障转移时复制到 Site Recovery 的所有数据。

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**最新（最低 RPO）** 恢复点是否影响故障转移 RTO？
是的。 Site Recovery 在故障转移之前需要处理所有挂起的数据，因此，此选项的恢复时间目标 (RTO) 比其他选项更高。

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>恢复点中的“最新处理”选项指的是什么？
“最新处理”选项将计划中的所有 VM 故障转移到 Site Recovery 处理的最新恢复点。 若要查看特定 VM 的最新恢复点，请检查 VM 设置中的“最新恢复点”。 此选项提供低的 RTO，因为无需费时处理未经处理的数据。

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>如果主要区域的服务意外中断，会出现什么情况？
可以在服务中断后触发故障转移。 Site Recovery 不需要从主要区域建立连接即可执行故障转移。

### <a name="what-is-a-rto-of-a-vm-failover-"></a>什么是 VM 故障转移的 RTO？
Site Recovery 的 [RTO SLA 为 2小时](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 但是，大多数情况下，Site Recovery 会在几分钟内对虚拟机进行故障转移。 可以转到故障转移作业来计算 RTO，该作业显示启动 VM 所需的时间。 有关恢复计划 RTO，请参阅以下部分。

## <a name="recovery-plans"></a>恢复计划

### <a name="what-is-a-recovery-plan"></a>什么是恢复计划？
Site Recovery 中的恢复计划可以协调 VM 的故障转移恢复。 它有助于实现恢复的一致准确性、可重复性和自动化。 恢复计划可为用户解决以下需求：

- 定义一组可以一起故障转移的虚拟机
- 定义虚拟机之间的依赖关系，使应用程序能够适时启动
- 自动完成恢复以及自定义的手动操作，以便除虚拟机故障转移以外，还可以实现其他任务

[详细了解](site-recovery-create-recovery-plans.md)恢复计划。

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>如何在恢复计划中实现定序？

在恢复计划中，可以创建多个组来实现定序。 每次故障转移一个组。 同一个组中的 VM 将一起故障转移，然后再故障转移另一个组中的 VM。 若要了解如何使用恢复计划为应用程序建模，请参阅[关于恢复计划](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到恢复计划的 RTO？
若要检查恢复计划的 RTO，请对恢复计划执行测试故障转移，然后转到“Site Recovery 作业”。
在以下示例中，名为 SAPTestRecoveryPlan 的作业花费了 8 分 59 秒来故障转移所有虚拟机和执行指定的操作。

![Site Recovery 作业列表](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>是否可将自动化 Runbook 添加到恢复计划？
是的，可将 Azure 自动化 Runbook 集成到恢复计划中。 [了解详细信息](site-recovery-runbook-automation.md)。

## <a name="reprotection-and-failback"></a>重新保护和故障回复

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>从主要区域故障转移到灾难恢复区域后，灾难恢复区域中的 VM 是否自动受到保护？
否。 将 Azure VM 从一个区域[故障转移](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)到另一个区域后，VM 将在灾难恢复区域中启动，但处于不受保护状态。 若要将 VM 故障回复到主要区域，需要[重新保护](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)次要区域中的 VM。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>重新保护时，Site Recovery 是否将完整的数据从次要区域复制到主要区域？
这取决于具体的情况。 例如，如果源区域 VM 存在，则只会同步源磁盘与目标磁盘之间的更改。 Site Recovery 将通过比较磁盘来计算差异，然后传输数据。 此过程通常需要几个小时。 有关重新保护期间所发生情况的详细信息，请参阅[在主要区域中重新保护已故障转移的 Azure VM]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)。

### <a name="how-much-time-does-it-take-to-fail-back"></a>故障回复需要多长时间？
进行重新保护后，故障回复所需的时间通常类似于从主要区域故障转移到次要区域所需的时间。

## <a name="capacity"></a>功能

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>如何保证 Azure VM 在目标区域的容量？
Site Recovery 团队会与 Azure 容量管理团队合作，规划足够的基础结构容量，确保支持灾难恢复的 VM 在启动故障转移时可以成功部署到目标区域中。

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery 是否适用于预留实例？
是的, 你可以在灾难恢复区域中购买[保留实例](https://azure.microsoft.com/pricing/reserved-vm-instances/), Site Recovery 故障转移操作将使用它们。 </br> 不需要任何其他配置。


## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>复制数据是否会发送到 Site Recovery 服务？
不会。Site Recovery 不会拦截复制的数据，也没有 VM 上运行的组件的任何相关信息。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。  
站点恢复已通过 ISO 27001:2013、27018、HIPAA、DPA 认证，目前正在接受 SOC2 和 FedRAMP JAB 评估。

### <a name="does-site-recovery-encrypt-replication"></a>站点恢复是否将复制数据加密？
是的，传输中加密和 [Azure 中的静态加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)均受支持。

## <a name="next-steps"></a>后续步骤
* [查看](azure-to-azure-support-matrix.md)支持要求。
* [设置](azure-to-azure-tutorial-enable-replication.md) Azure 到 Azure 的复制。
- 如果在阅读本文后有任何问题，请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)上发布问题。
