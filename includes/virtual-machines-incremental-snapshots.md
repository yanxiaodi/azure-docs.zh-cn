---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 06e6e491fa1e9a047527efb78149855b125771ef
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172934"
---
# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>使用递增快照备份 Azure 非托管 VM 磁盘
## <a name="overview"></a>概述
Azure 存储提供创建 Blob 快照的功能。 快照将捕获该时间点的 Blob 状态。 本文介绍有关如何使用快照维护虚拟机磁盘备份的方案。 如果选择不使用 Azure 的备份和恢复服务，但想要为虚拟机磁盘创建自定义的备份策略，则可以使用此方法。

Azure 虚拟机磁盘在 Azure 存储中将存储为页 Blob。 本文介绍的是虚拟机磁盘的备份策略，因此，我们指的是页 Blob 上下文中的快照。 若要详细了解快照，请参阅 [Creating a Snapshot of a Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)（创建 Blob 的快照）。

## <a name="what-is-a-snapshot"></a>是什么快照？
Blob 快照是在某个时间点捕获的 Blob 只读版本。 在创建快照后，可以读取、复制或删除该快照，但无法对其进行修改。 利用快照，可以在某个时间点备份 Blob。 在 REST 2015-04-05 版之前，可以复制完整快照。 使用 REST 2015-07-08 版或更高版本，还可以复制增量快照。

## <a name="full-snapshot-copy"></a>完整快照复制
可将快照作为 Blob 复制到另一个存储帐户，以保留基本 Blob 的备份。 还可以复制快照 Blob 来覆盖基本 Blob，这类似于将 Blob 还原到以前的版本。 将快照从某个存储帐户复制到另一个存储帐户时，将占用与基本页 Blob 相同的空间。 因此，将整个快照从某个存储帐户复制到另一个存储帐户时速度较慢，并且会消耗目标存储帐户中的大量空间。

> [!NOTE]
> 如果将基本 Blob 复制到另一个目标，则不会一起复制 Blob 的快照。 同样，如果使用副本覆盖基本 Blob，与基本 Blob 关联的快照不会受到影响，并且可让基本 Blob 名称保持不变。
> 
> 

### <a name="back-up-disks-using-snapshots"></a>使用快照备份磁盘
作为虚拟机磁盘的备份策略，可以创建磁盘或页 Blob 的定期快照，并使用[复制 Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) 操作或 [AzCopy](../articles/storage/common/storage-use-azcopy.md) 之类的工具将其复制到另一个存储帐户。 可将快照复制到具有不同名称的目标页 Blob。 生成的目标页 Blob 是可编写的页 Blob，而不是快照。 本文稍后介绍使用快照创建虚拟机磁盘备份的步骤。

### <a name="restore-disks-using-snapshots"></a>使用快照还原磁盘
需要将磁盘还原到以前在某个备份快照中捕获的稳定版本时，可以复制一个快照来覆盖基本页 Blob。 将快照升级到基本页 Blob 之后，快照会保留，但会使用可读写的副本覆盖其源。 本文稍后介绍从快照还原旧版磁盘的步骤。

### <a name="implementing-full-snapshot-copy"></a>实现完整快照复制
可以通过执行以下操作来实现完整快照复制：

* 首先，使用[快照 Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) 操作获取基本 Blob 的快照。
* 然后，使用[复制 Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) 将快照复制到目标存储帐户。
* 重复此过程以保留基本 Blob 的备份副本。

## <a name="incremental-snapshot-copy"></a>增量快照复制
[GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API 中的新功能提供更好的方式来备份页 Blob 或磁盘的快照。 此 API 返回在基 blob 和快照之间所做更改的列表，从而减少了在备份帐户上使用的存储空间量。 该 API 支持高级存储以及标准存储的页 Blob。 可以使用此 API 为 Azure VM 构建更快速有效的备份解决方案。 此 API 适用于 REST 2015-07-08 和更高版本。

增量快照复制可让你将以下两者之间的差异从一个存储帐户复制到另一个存储帐户：

* 基本 Blob 及其快照，或
* 基本 Blob 的任意两个快照

必须符合以下先决条件：

* Blob 是在 2016 年 1 月 1 日或之后创建。
* 未在两个快照之间使用 [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) 或[复制 Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) 覆盖 Blob。

**注意**：此功能仅适用于高级和标准 Azure 页 Blob。

如果存在使用快照的自定义备份策略，则将快照从一个存储帐户复制到另一个存储帐户可能会慢，并且将消耗大量的存储空间。 可以将连续快照之间的差异写入备份页 Blob，而不是将整个快照复制到备份存储帐户。 这样，便可以大量减少复制的时间和存储备份的空间。

### <a name="implementing-incremental-snapshot-copy"></a>实现增量快照复制
可以通过执行以下操作来实现增量快照复制：

* 使用[快照 Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) 获取基本 Blob 的快照。
* 使用[复制 Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) 将快照复制到相同区域或任何其他 Azure 区域中的目标备份存储帐户。 这是备份页 Blob。 创建备份页 Blob 的快照，并将其存储在备份帐户中。
* 使用快照 Blob 创建基本 Blob 的另一个快照。
* 使用 [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) 获取基本 Blob 的第一个与第二个快照之间的差异。 使用新参数 **prevsnapshot** 指定要用于获取差异的快照的 DateTime 值。 如果提供此参数，则 REST 响应只包含在目标快照与先前快照之间更改的页面（包括清除页面）。
* 使用 [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) 将这些更改应用到备份页 Blob。
* 最后，创建备份页 Blob 的快照，并将其存储在备份存储帐户中。

在下一部分中，我们将详细说明如何使用增量快照复制维护磁盘的备份

## <a name="scenario"></a>场景
在本部分中，我们介绍一种方案，它涉及到使用快照针对虚拟机磁盘实施自定义的备份策略。

假设在某个 DS 系列 Azure VM 上附加了一个高级存储 P30 磁盘。 名为 mypremiumdisk  的 P30 磁盘存储在名为 mypremiumaccount  的高级存储帐户中。 名为 mybackupstdaccount  的标准存储帐户用于存储 mypremiumdisk  的备份。 我们希望每隔 12 小时保留一个 mypremiumdisk  的快照。

要了解如何创建存储帐户，请参阅[创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。

若要了解如何备份 Azure VM，请参阅[规划 Azure VM 备份](../articles/backup/backup-azure-vms-introduction.md)。

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>使用增量快照维护磁盘备份的步骤
下述步骤介绍如何创建 mypremiumdisk  的快照，并在 mybackupstdaccount  中维护备份。 备份是名为 mybackupstdpageblob  的标准页 Blob。 备份页 Blob 始终反映与 mypremiumdisk  的最新快照相同的状态。

1. 为高级存储磁盘创建备份页 blob，方法是拍摄名为 mypremiumdisk_ss1 的  mypremiumdisk 的快照。 
2. 将此快照复制到 mybackupstdaccount，用作名为 mybackupstdpageblob  的页 Blob。
3. 使用[创建 Blob 快照](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob)为 *mybackupstdpageblob* 创建名为 *mybackupstdpageblob_ss1* 的快照，并将其存储在 *mybackupstdaccount* 中。
4. 在备份时段内，创建 mypremiumdisk  的快照（即 mypremiumdisk_ss2  ），并将其存储在 mypremiumaccount  中。
5. 在 **prevsnapshot** 参数设置为 mypremiumdisk_ss1  时间戳的情况下，在 mypremiumdisk_ss2  使用 [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) 获取两个快照（mypremiumdisk_ss2  与 mypremiumdisk_ss1  ）之间的增量更改。 将这些增量更改写入 mybackupstdaccount  中的备份页 Blob mybackupstdpageblob  。 如果增量更改中有已删除的范围，则必须从备份页 Blob 中清除这些范围。 使用 [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) 将增量更改写入备份页 Blob。
6. 获取名为 mybackupstdpageblob_ss2  的备份页 Blob mybackupstdpageblob  快照。 从高级存储帐户删除以前的快照 mypremiumdisk_ss1  。
7. 在每个备份时段内重复步骤 4-6。 这样，即可在标准存储帐户中维护 mypremiumdisk  的备份。

![使用增量快照备份磁盘](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>从快照还原磁盘的步骤
下述步骤介绍如何将高级磁盘 mypremiumdisk  从备份存储帐户 mybackupstdaccount  还原到以前的快照。

1. 确定要将高级磁盘还原到的时间点。 假设这是存储在备份存储帐户 mybackupstdaccount  中的快照 mybackupstdpageblob_ss2  。
2. 在 mybackupstdaccount 中，将快照 mybackupstdpageblob_ss2  升级为新的备份基本页 Blob mybackupstdpageblobrestored  。
3. 获取这个已还原备份页 Blob 的、名为 mybackupstdpageblobrestored_ss1  的快照。
4. 将已还原页 Blob *mybackupstdpageblobrestored* 从 *mybackupstdaccount* 复制到 *mypremiumaccount*，作为新的高级磁盘 *mypremiumdiskrestored*。
5. 为 *mypremiumdiskrestored* 创建名为 *mypremiumdiskrestored_ss1* 的快照，以便将来执行增量备份。
6. 将 DS 系列 VM 指向已还原的磁盘 *mypremiumdiskrestored*，并从 VM 分离旧的 *mypremiumdisk*。
7. 使用 mybackupstdpageblobrestored  作为备份页 Blob，根据前一部分中所述，开始针对已还原的磁盘 mypremiumdiskrestored  执行备份过程。

![从快照还原磁盘](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>后续步骤
使用以下链接详细了解如何创建 Blob 的快照和规划 VM 备份基础结构。

* [创建 Blob 的快照](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [规划 VM 备份基础结构](../articles/backup/backup-azure-vms-introduction.md)

