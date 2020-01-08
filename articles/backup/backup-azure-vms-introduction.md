---
title: 关于 Azure VM 备份
description: 了解 Azure VM 备份，并记下一些最佳做法。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: dacurwin
ms.openlocfilehash: db3e4b8a8abea4718f5779790906bf45591d221c
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018692"
---
# <a name="an-overview-of-azure-vm-backup"></a>概要了解 Azure VM 备份

本文介绍 [Azure 备份服务](backup-introduction-to-azure-backup.md)如何备份 Azure 虚拟机 (VM)。

## <a name="backup-process"></a>备份过程

下面介绍 Azure 备份如何对 Azure VM 完成备份：

1. 对于选择进行备份的 Azure VM，Azure 备份服务将根据指定的备份计划启动备份作业。
1. 首次备份期间，如果 VM 已运行，则会在 VM 上安装备份扩展。
    - 对于 Windows VM，将安装 _VMSnapshot_ 扩展。
    - 对于 Linux VM，将安装 _VMSnapshotLinux_ 扩展。
1. 对于正在运行的 Windows VM，备份服务将与卷影复制服务 (VSS) 互相配合，来创建 VM 的应用一致性快照。
    - 备份服务默认创建完整的 VSS 备份。
    - 如果备份服务无法创建应用一致性快照，则会创建基础存储的文件一致性快照（因为当 VM 停止时，不会发生应用程序写入）。
1. 对于 Linux VM，Azure 备份将创建文件一致性备份。 对于应用一致性快照，需要手动自定义前脚本/后脚本。
1. 备份服务创建快照后，会将数据传输到保管库。
    - 可以通过并行备份每个 VM 磁盘来优化备份。
    - 对于每个要备份的磁盘，Azure 备份将读取磁盘上的块，识别并只传输自上次备份以来已发生更改的数据块（增量传输）。
    - 快照数据可能不会立即复制到保管库。 在高峰期，可能需要好几个小时才能完成复制。 每日备份策略规定的 VM 备份总时间不会超过 24 小时。
 1. 在 Windows VM 上启用 Azure 备份后，对 VM 所做的更改包括：
    -   在 VM 中安装 Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660
    -   将卷影复制服务 (VSS) 的启动类型从手动更改为自动
    -   添加 IaaSVmProvider Windows 服务

1. 数据传输完成后，会删除快照并创建恢复点。

![Azure 虚拟机备份体系结构](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="encryption-of-azure-vm-backups"></a>加密 Azure VM 备份

使用 Azure 备份备份 Azure VM 时，将使用存储服务加密 (SSE) 对 VM 进行静态加密。 Azure 备份还可以备份使用 Azure 磁盘加密进行加密的 Azure VM。

**加密** | **详细信息** | **支持**
--- | --- | ---
**Azure 磁盘加密** | Azure 磁盘加密可以加密 Azure VM 的 OS 磁盘和数据磁盘。<br/><br/> Azure 磁盘加密与在 Key Vault 中作为机密受到保护的 BitLocker 加密密钥 (BEK) 相集成。 Azure 磁盘加密还与 Azure Key Vault 密钥加密密钥 (KEK) 相集成。 | Azure 备份支持备份仅使用 BEK 加密的，或者同时使用 BEK 和 KEK 加密的托管型和非托管型 Azure VM。<br/><br/> BEK 和 KEK 都会得到备份和加密。<br/><br/> 由于 KEK 和 BEK 都会得到备份，拥有相应权限的用户可根据需要，将密钥和机密还原到 Key Vault。 这些用户还可以恢复已加密的 VM。<br/><br/> 未经授权的用户或 Azure 无法读取已加密的密钥和机密。
**SSE** | Azure 存储使用 SSE 提供静态加密，在存储数据之前，它会自动加密数据。 Azure 存储还会在检索数据之前解密数据。 | Azure 备份使用 SSE 对 Azure VM 进行静态加密。

对于托管和非托管 Azure VM，备份服务支持仅经过 BEK 加密的或者同时经过 BEK 和 KEK 加密的 VM。

备份的 BEK（机密）和 KEK（密钥）将会加密。 只有在经授权的用户将它们还原到 Key Vault 之后，才能读取和使用它们。 未经授权的用户和 Azure 都无法读取或使用备份的密钥或机密。

同时会备份 BEK。 因此，在 BEK 丢失的情况下，经授权的用户可将 BEK 还原到 Key Vault 并恢复加密的 VM。 只有拥有所需的权限级别的用户才可以备份和还原加密的 VM 或密钥和机密。

## <a name="snapshot-creation"></a>快照创建

Azure 备份根据备份计划创建快照。

- **Windows VM：** 对于 Windows VM，备份服务将与 VSS 相配合，来创建 VM 磁盘的应用一致性快照。

  - 默认情况下，Azure 备份会获取完整的 VSS 备份。 [了解详细信息](https://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)。
  - 若要更改设置，使 Azure 备份创建 VSS 副本备份，请在命令提示符下设置以下注册表项：

    **REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgent" /v USEVSSCOPYBACKUP /t REG_SZ /d TRUE /f**

- **Linux VM：** 若要创建 Linux VM 的应用一致性快照，请使用 Linux 前脚本和后脚本框架编写自己的自定义脚本，以确保一致性。

  - Azure 备份只调用你编写的前脚本/后脚本。
  - 如果前脚本和后脚本成功执行，Azure 备份会将恢复点标记为应用程序一致。 但是，在使用自定义脚本时，你最终需要为应用程序一致性负责。
  - [详细了解](backup-azure-linux-app-consistent.md)如何配置脚本。

### <a name="snapshot-consistency"></a>快照一致性

下表解释了不同的快照一致性类型：

**快照** | **详细信息** | **恢复** | **注意事项**
--- | --- | --- | ---
**应用程序一致** | 应用一致性备份捕获内存内容和挂起的 I/O 操作。 应用一致性快照使用 VSS 编写器（或适用于 Linux 的前/后脚本）来确保备份之前的应用数据一致性。 | 使用应用一致性快照恢复 VM 时，VM 将会启动。 没有数据损坏或丢失。 应用以一致的状态启动。 | Windows:所有 VSS 编写器均成功<br/><br/> Linux：前/后脚本已配置并成功
**文件系统一致性** | 文件系统一致性备份通过同时创建所有文件的快照来提供一致性。<br/><br/> | 使用文件系统一致性快照恢复 VM 时，VM 将会启动。 没有数据损坏或丢失。 应用需要实现自己的“修复”机制以确保还原的数据一致。 | Windows:部分 VSS 编写器失败 <br/><br/> Linux：默认值（如果前/后脚本未配置或失败）
**故障一致** | 如果在备份时 Azure VM 关闭，则往往会发生崩溃一致性快照。 仅会捕获和备份备份时磁盘上已存在的数据。<br/><br/> 存在崩溃一致性问题的恢复点不能保证操作系统或应用的数据一致性。 | VM 通常会启动，然后启动磁盘检查来修复损坏错误，但不能保证这一点。 在崩溃之前未传输到磁盘的任何内存中数据或写入操作将会丢失。 应用实现自己的数据验证。 例如，数据库应用可以使用其事务日志进行验证。 如果事务日志中有条目不在数据库中，则数据库软件将回滚事务，直到数据一致。 | VM 处于关闭状态

## <a name="backup-and-restore-considerations"></a>备份和还原注意事项

**注意事项** | **详细信息**
--- | ---
**磁盘** | 备份 VM 磁盘属于并行操作。 例如，如果 VM 有 4 个磁盘，则备份服务会尝试并行备份所有 4 个磁盘。 备份是增量式的（仅备份已更改的数据）。
**计划** |  若要减少备份流量，请在一天的不同时间备份不同的 VM，并确保时间不重叠。 同时备份 VM 会导致流量拥塞。
**准备备份** | 注意准备备份所需的时间。 准备时间包括安装或更新备份扩展，以及根据备份计划触发快照。
**数据传输** | 考虑 Azure 备份识别上一备份中的增量更改所需的时间。<br/><br/> 在增量备份中，Azure 备份将通过计算块的校验和来确定更改。 如果某个块发生更改，则将该块标识为要传输到保管库。 该服务将分析已标识的块，以试图进一步地尽量减少要传输的数据量。 评估所有已更改的块后，Azure 备份会将更改传输到保管库。<br/><br/> 创建快照之后，可能要经过一段滞后时间才会将它复制到保管库。<br/><br/> 在高峰期，处理备份最长可能需要花费 8 个小时。 对于每日备份，VM 的备份时间小于 24 小时。
**初始备份** | 增量备份的总备份时间不超过 24 小时，但是，首次备份可能并非如此。 初始备份所需的时间取决于数据大小和备份处理时间。
**还原队列** | Azure 备份同时处理来自多个存储账户的还原作业，因此还原请求会排入队列中。
**还原副本** | 在还原过程中，将保管库中的数据复制到存储帐户。<br/><br/> 总还原时间取决于存储帐户的每秒 I/O 操作次数 (IOPS) 和吞吐量。<br/><br/> 若要减少复制时间，请选择一个不带其他应用程序写入和读取负载的存储帐户。

### <a name="backup-performance"></a>备份性能

这些常见的场景可能会影响总备份时间：

- **将新磁盘添加到受保护的 Azure VM：** 如果 VM 正在进行增量备份，此时将一个新磁盘添加到其中，则备份时间将会增大。 总备份时间可能会超过 24 小时，因为需要对新磁盘进行初始复制，并且需要对现有磁盘进行增量复制。
- **磁盘有碎片：** 如果磁盘上的更改是相邻的，则备份操作会更快。 如果更改分散在磁盘的各个位置并出现碎片，则备份会变慢。
- **磁盘变动率：** 如果正在进行增量备份的受保护磁盘的每日变动率超过 200 GB，则备份可能需要花费很长时间（8 小时以上）才能完成。
- **备份版本：** 最新版本的备份（称为“即时还原”版本）使用比校验和比较更佳的优化进程来识别更改。 但是，如果使用即时还原并删除了备份快照，则备份将改用校验和比较。 在这种情况下，备份操作将超过 24 小时（或失败）。

## <a name="best-practices"></a>最佳实践

我们建议在配置 VM 备份时遵循以下做法：

- 修改策略中设置的默认计划时间。 例如，如果策略中的默认时间是凌晨 12:00，请将时间递增几分钟，确保以最佳方式使用资源。
- 如果从单个保管库还原 VM，我们强烈建议使用不同的[常规用途 v2 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)，以确保目标存储帐户不会受到限制。 例如，每个 VM 必须具有不同的存储帐户。 例如，如果还原 10 个 VM，请使用 10 个不同的存储帐户。
- 若要通过 Instant Restore 备份使用高级存储的 VM，建议从总的已分配存储空间中分配 *50%* 的可用空间，这**只**在首次备份时是必需的。 首次备份完成后，50% 的可用空间不再是备份的要求
- 从常规用途 v1 存储层（快照）进行的还原将在几分钟内完成，因为快照位于同一存储帐户中。 从常规用途 v2 存储层（保管库）进行的还原可能需要数小时。 如果数据在常规用途 v1 存储中提供，则我们建议使用[即时还原](backup-instant-restore-capability.md)功能来加快还原速度。 （如果必须从保管库还原数据，则需要更多时间。）
- 每个存储帐户的磁盘数限制与基础结构即服务 (IaaS) VM 上运行的应用程序访问磁盘的大小有关。 通常情况下，如果单个存储帐户上存在 5 至 10 个或以上磁盘，则通过将一些磁盘移动到单独的存储帐户以均衡负载。

## <a name="backup-costs"></a>备份成本

使用 Azure 备份备份的 Azure VM 采用 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)。

第一个备份成功完成后才会开始计费。 存储和受保护的 VM 也会在此同时开始计费。 只要针对 VM 的任何备份数据存储在保管库中，就会持续计费。 如果对 VM 停止保护，但保管库中存在该 VM 的备份数据，则会继续计费。

针对特定 VM 的计费仅在停止保护并且删除全部备份数据后才会停止。 当停止保护并且没有活动的备份作业时，最后一个成功的 VM 备份的大小将成为用于每月帐单的受保护实例大小。

受保护实例大小计算基于 VM 的实际大小。 VM 的大小是 VM 中除临时存储以外的所有数据之和。 定价基于数据磁盘中存储的实际数据，而不是附加到 VM 的每个数据磁盘的最大支持大小。

与此类似，备份存储的收费是基于 Azure 备份中存储的数据量，即每个恢复点中实际数据之和。

以 A2-Standard 大小的 VM 为例，该 VM 有两个额外的数据磁盘，其最大大小各为 4 TB。 下表显示了其中每个磁盘上存储的实际数据：

**磁盘** | **最大大小** | **实际存在的数据**
--- | --- | ---
操作系统磁盘 | 4095 GB | 17 GB
本地/临时磁盘 | 135 GB | 5 GB（不包括在备份中）
数据磁盘 1 | 4095 GB | 30 GB
数据磁盘 2 | 4095 GB | 0 GB

此示例中，VM 的实际大小为 17 GB + 30 GB + 0 GB = 47 GB。 此受保护实例大小 (47 GB) 成为按月计费的基础。 随着 VM 中数据量的增长，用于计费的受保护实例大小也会相应变化。

<a name="limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30tb"></a>
## <a name="public-preview-backup-of-vm-with-disk-sizes-up-to-30-tb"></a>公共预览版：备份磁盘大小最大为 30 TB 的 VM

Azure 备份现在支持对大小高达 30 TB 的大型[Azure 托管磁盘](https://azure.microsoft.com/blog/larger-more-powerful-managed-disks-for-azure-virtual-machines/)进行公共预览。 该预览版为托管虚拟机提供生产级别的支持。

虚拟机的备份，每个磁盘的最大大小为30TB，而 VM 中所有磁盘的最大256TB 合并，则不会影响现有备份。 如果虚拟机已配置了 Azure 备份，则无需用户操作即可为大大小磁盘运行备份。

所有具有配置了备份的大型磁盘的 Azure 虚拟机都应成功备份。

## <a name="next-steps"></a>后续步骤

现在请[准备进行 Azure VM 备份](backup-azure-arm-vms-prepare.md)。
