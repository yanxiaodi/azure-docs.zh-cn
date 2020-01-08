---
title: Azure 中 SQL Server 的性能准则 | Microsoft Docs
description: 提供有关优化 Microsoft Azure VM 中的 SQL Server 性能的准则。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/26/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6a386096d8a94c240e9a00457d87d04254e02920
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102004"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Azure 虚拟机中的 SQL Server 的性能准则

## <a name="overview"></a>概述

本文提供了有关在 Microsoft Azure 虚拟机中优化 SQL Server 性能的最佳做法。 在 Azure 虚拟机中运行 SQL Server 时，我们建议继续使用适用于 SQL Server 在本地服务器环境中的相同数据库性能优化选项。 但是，关系数据库在公有云中的性能取决于许多因素，如虚拟机的大小和数据磁盘的配置。

[在 Azure 门户中预配的 SQL Server 映像](quickstart-sql-vm-create-portal.md)遵循一般的存储配置最佳做法（有关存储配置情况的详细信息，请参阅 [SQL Server VM 的存储配置](virtual-machines-windows-sql-server-storage-configuration.md)）。 在预配后，请考虑应用本文中讨论的其他优化措施。 根据你的工作负荷进行选择并通过测试进行验证。

> [!TIP]
> 通常需要在针对成本优化和针对性能优化之间进行权衡。 本文重点介绍获得 SQL Server 在 Azure VM 上的*最佳*性能。 如果工作负荷要求较低，可能不需要下面列出的每项优化。 评估这些建议时应考虑性能需求、成本和工作负荷模式。

## <a name="quick-check-list"></a>快速检查列表

下面是 SQL Server 在 Azure 虚拟机中的优化性能快速检查列表：

| 区域 | 优化 |
| --- | --- |
| [VM 大小](#vm-size-guidance) | SQL Enterprise Edition：- [DS3_v2](../sizes-general.md) 或更高版本。<br/><br/> SQL Standard 和 Web Edition：- [DS2_v2](../sizes-general.md) 或更高版本。 |
| [存储](#storage-guidance) | - 使用[高级 SSD](../disks-types.md)。 仅建议将标准存储用于开发/测试。<br/><br/> - 将[存储帐户](../../../storage/common/storage-create-storage-account.md)和 SQL Server VM 保存在相同的区域。<br/><br/> * 在存储帐户上禁用 Azure [异地冗余存储](../../../storage/common/storage-redundancy.md)（异地复制）。 |
| [磁盘](#disks-guidance) | - 使用至少 2 个 [P30 磁盘](../disks-types.md#premium-ssd)（一个用于日志，另一个用于数据文件，包括 TempDB）。 对于需要约 50,000 IOPS 的工作负荷，请考虑使用超级 SSD。 <br/><br/> - 避免使用操作系统或临时磁盘进行数据库存储或日志记录。<br/><br/> - 在托管数据文件和 TempDB 数据文件的磁盘上启用读取缓存。<br/><br/> - 请勿在托管日志文件的磁盘上启用缓存。  **重要说明**：更改 Azure VM 磁盘的缓存设置时，请停止 SQL Server 服务。<br/><br/> - 条带化多个 Azure 数据磁盘，提高 IO 吞吐量。<br/><br/> - 使用规定的分配大小格式化。 <br/><br/> -将 TempDB 放置在本地 SSD `D:\`驱动器上, 用于任务关键型 SQL Server 工作负荷 (选择正确的 VM 大小之后)。 [使用 ssd 存储 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)的博客中的详细信息。  |
| [I/O](#io-guidance) |- 启用数据库页面压缩。<br/><br/> - 对数据文件启用即时文件初始化。<br/><br/> - 限制数据库自动增长。<br/><br/> - 禁用数据库自动收缩。<br/><br/> - 将所有数据库（包括系统数据库）转移到数据磁盘。<br/><br/> - 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。<br/><br/> - 设置默认的备份和数据库文件位置。<br/><br/> - 启用锁定页面。<br/><br/> - 应用 SQL Server 性能修复程序。 |
| [Feature-specific](#feature-specific-guidance) | - 直接备份到 blob 存储。 |

有关*如何*和*为何*进行这些优化的详细信息，请参阅以下部分提供的详细信息与指导。

## <a name="vm-size-guidance"></a>VM 大小指导原则

对于性能敏感型应用程序，建议使用以下[虚拟机大小](../sizes.md)：

* **SQL Server Enterprise Edition**：DS3_v2 或更高
* **SQL Server Standard 和 Web Edition**：DS2_v2 或更高

[DSv2 系列](../sizes-general.md#dsv2-series) VM 支持高级存储，高级存储是能提供最佳性能的推荐选择。 这里推荐的大小是基线，实际选择的计算机大小取决于工作负载的需求。 DSv2 系列 VM 是常规用途的 VM，适用于各种工作负载，而其他计算机大小针对特定工作负载类型进行了优化。 例如，[M 系列](../sizes-memory.md#m-series)为最大型的 SQL Server 工作负载提供最高的 vCPU 数量和内存。 [GS 系列](../sizes-previous-gen.md#gs-series)和 [DSv2 系列 11 到 15](../sizes-memory.md#dsv2-series-11-15) 经过优化，以满足较大的内存需求。 还可以在[受约束的核心大小](../../windows/constrained-vcpu.md)中选择这两种系列，这样能降低计算需求，从而节省工作负载的成本。 [Ls 系列](../sizes-storage.md)计算机是针对较高的磁盘吞吐量和 IO 进行优化的。 请务必在对 VM 系列和大小进行选择时考虑特定 SQL Server 工作负载的实际情况。

## <a name="storage-guidance"></a>存储指导原则

DS 系列（以及 DSv2 系列和 GS 系列）VM 支持[高级 SSD](../disks-types.md)。 建议为所有生产工作负荷使用高级 SSD。

> [!WARNING]
> 标准 HDD 和 SSD 具有不同的延迟和带宽，建议仅用于开发/测试工作负荷。 生产工作负荷应使用高级 SSD。

此外，我们建议创建 Azure 存储帐户与 SQL Server 虚拟机在同一数据中心中，以减小传输延迟。 创建存储帐户时应禁用异地复制，因为无法保证在多个磁盘上的写入顺序一致。 相反，请考虑在两个 Azure 数据中心之间配置一个 SQL Server 灾难恢复技术。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md)。

## <a name="disks-guidance"></a>磁盘指导原则

Azure VM 上有三种主要磁盘类型：

* **OS 磁盘**：创建 Azure 虚拟机时，该平台至少将一个磁盘（标记为 **C** 驱动器）附加到 VM 作为操作系统磁盘。 此磁盘是一个 VHD，在存储空间中存储为一个页 blob。
* **临时磁盘**：Azure 虚拟机包含另一个称为临时磁盘的磁盘（标记为 **D**: 驱动器）。 这是可用于暂存空间的节点上的一个磁盘。
* **数据磁盘**：还可以将其他磁盘作为数据磁盘附加到虚拟机，这些磁盘会在存储空间中存储为页 Blob。

以下部分说明了有关使用这些不同磁盘的建议。

### <a name="operating-system-disk"></a>操作系统磁盘

操作系统磁盘是可以作为操作系统的运行版本来启动和装载的 VHD，并且标记为 **C** 驱动器。

操作系统磁盘上的默认缓存策略是**读/写**。 对于性能敏感型应用程序，我们建议使用数据磁盘而不是操作系统磁盘。 请参阅下面有关数据磁盘的部分。

### <a name="temporary-disk"></a>临时磁盘

临时存储驱动器，标记为 **D**: 驱动器，不会保留到 Azure Blob 存储区。 不要在 **D**: 驱动器中存储用户数据库文件或用户事务日志文件。

D 系列、Dv2 系列和 G 系列 VM 上的临时驱动器基于 SSD。 如果工作负荷重度使用 TempDB（例如，要处理临时对象或复杂联接），在 **D** 驱动器上存储 TempDB 可能会提高 TempDB 吞吐量并降低 TempDB 延迟。 有关示例方案，请参阅以下博客文章中的 TempDB 介绍：[有关 Azure VM 上 SQL Server 的存储配置指导原则](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm)

对于支持高级 SSD 的 VM（DS 系列、DSv2 系列和 GS 系列），建议将 TempDB 存储在支持高级 SSD 且已启用读取缓存的磁盘上。

这项建议有一种例外情况；如果 TempDB 的使用是写入密集型的，则可以通过将 TempDB 存储在本地 D 驱动器（在这些计算机大小上也是基于 SSD）上来实现更高性能。 有关详细信息, 请参阅将[ssd 与 TempDB 结合使用](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)博客。 

### <a name="data-disks"></a>数据磁盘

* **将数据磁盘用于数据和日志文件**：如果不使用磁盘条带化, 请使用两个高级 SSD P30 磁盘, 其中一个磁盘包含日志文件, 另一个包含数据和 TempDB (如上所述的任务关键工作负荷和写繁重工作负载除外)。 每个高级 SSD 均根据其大小提供许多 IOP 和带宽 (MB/s)，如[选择磁盘类型](../disks-types.md)一文中所述。 如果使用磁盘条带化技术，例如存储空间，则可实现最佳性能，因为将具有两个池，一个用于日志文件，另一个用于数据文件。 但是，如果你打算使用 SQL Server 故障转移群集实例 (FCI)，则必须配置单个池。

   > [!TIP]
   > - 有关不同磁盘和工作负荷配置的测试结果，请参阅以下博客文章：[有关 Azure VM 上 SQL Server 的存储配置指导原则](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)
   > - 对于需要约 50,000 IOPS 的 SQL Server 的任务关键型性能，请考虑使用超级 SSD 替换 10 -P30 磁盘。 有关详细信息，请参阅以下博客文章：[具有超级 SSD 的任务关键型性能](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)。

   > [!NOTE]
   > 在门户中预配 SQL Server VM 时，可以选择编辑存储配置。 根据配置，Azure 将配置一个或多个磁盘。 使用条带化，可将多个磁盘组合到单个存储池中。 数据文件和日志文件一起位于此配置中。 有关详细信息，请参阅 [SQL Server VM 的存储配置](virtual-machines-windows-sql-server-storage-configuration.md)。

* **磁盘条带化**：为提高吞吐量，可以添加更多的数据磁盘，并使用磁盘条带化。 若要确定数据磁盘的数量，需要分析日志文件以及数据和 TempDB 文件所需的 IOPS 数量和带宽。 请注意，不同的 VM 大小对受支持的 IOP 数量和带宽有不同的限制，请参阅每个 [VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的 IOPS 表。 遵循以下指南：

  * 对于 Windows 8/Windows Server 2012 或更高版本，按照以下指南使用[存储空间](https://technet.microsoft.com/library/hh831739.aspx)：

      1. 对于 OLTP 工作负荷，将交错（条带大小）设置为 64 KB（65536 字节），对于数据仓库工作负荷，将交错（条带大小）设置为 256 KB（262144 字节），以避免分区定位错误导致的性能影响。 这必须使用 PowerShell 设置。
      2. 设置列计数 = 物理磁盘的数量。 配置的磁盘超过 8 个时，请使用 PowerShell（而不是服务器管理器 UI）。 

    例如，以下 PowerShell 创建新的存储池时将交错大小设为 64 KB，将列数设为 2：

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * 对于 Windows 2008 R2 或更早版本，可以使用动态磁盘（操作系统条带化卷），条带大小始终为 64 KB。 请注意，从 Windows 8/Windows Server 2012 开始不推荐使用此选项。 有关信息，请参阅[虚拟磁盘服务正在过渡到 Windows 存储管理 API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx) 中的支持声明。

  * 如果将[存储空间直通 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) 与 [SQL Server 故障转移群集实例](virtual-machines-windows-portal-sql-create-failover-cluster.md)配合使用，则必须配置单个池。 请注意，虽然可以在该单个池上创建不同的卷，但它们都拥有相同的特征，例如相同的缓存策略。

  * 根据负载预期确定与你的存储池相关联的磁盘数。 请记住，不同的 VM 大小允许不同数量的附加数据磁盘。 有关详细信息，请参阅[虚拟机的大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

  * 如果使用的不是高级 SSD（开发/测试方案），建议添加 [VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)支持的最大数量的数据磁盘并使用磁盘条带化。

* **缓存策略**：请注意用于缓存策略的以下建议，具体取决于你的存储配置。

  * 如果为数据文件和日志文件使用不同的磁盘，请在承载着数据文件和 TempDB 数据文件的数据磁盘上启用读取缓存。 这可能会明显提高性能。 不要在存放日志文件的磁盘上启用缓存，因为这会导致性能稍微降低。

  * 如果在单个存储池中使用磁盘条带化，则大多数工作负荷都会从读取缓存受益。 如果日志文件和数据文件分别具有单独的存储池，请仅在数据文件的存储池上启用读取缓存。 在某些高写入工作负荷中，不使用缓存时可能会获得更好的性能。 这只能通过测试来确定。

  * 前面的建议适用于高级 SSD。 如果使用的不是高级 SSD，不要在任何数据磁盘上启用任何缓存。

  * 有关配置磁盘缓存的说明，请参阅以下文章。 对于经典 (ASM) 部署模型，请参阅：[Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) 和 [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx)。 对于 Azure 资源管理器部署模型，请参阅：[Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) 和 [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk)。

     > [!WARNING]
     > 请在更改 Azure VM 磁盘的缓存设置时停止 SQL Server 服务，以免出现数据库损坏的情况。

* **NTFS 分配单元大小**：格式化数据磁盘时，建议为数据和日志文件以及 TempDB 使用 64-KB 分配单元大小。

* **磁盘管理最佳做法**：删除数据磁盘或更改其缓存类型时，请在更改过程中停止 SQL Server 服务。 在 OS 磁盘上更改缓存设置时，Azure 会先停止 VM，在更改缓存类型后再重新启动 VM。 更改数据磁盘的缓存设置时，不会停止 VM，但会在更改期间将数据磁盘从 VM 分离，完成后再重新附加该数据磁盘。

  > [!WARNING]
  > 在进行这些操作时，如果无法停止 SQL Server 服务，则会导致数据库损坏。


## <a name="io-guidance"></a>I/O 指导原则

* 并行化应用程序和请求时可实现使用高级 SSD 的最佳结果。 高级 SSD 专为 IO 队列深度大于 1 的方案设计，因此对于单线程串行请求（即使它们是存储密集型），不会看到明显的性能提升。 例如，这会影响性能分析工具（如 SQLIO）的单线程测试结果。

* 请考虑使用[数据库页压缩](https://msdn.microsoft.com/library/cc280449.aspx)，因为这有助于提高 I/O 密集型工作负荷的性能。 但是，数据压缩可能会增加数据库服务器上的 CPU 消耗。

* 请考虑启用即时文件初始化以减少初始文件分配所需的时间。 要利用即时文件初始化，请将 SE_MANAGE_VOLUME_NAME 授予 SQL Server (MSSQLSERVER) 服务帐户并将其添加到**执行卷维护任务**安全策略。 如果使用的是用于 Azure 的 SQL Server 平台映像，默认服务帐户 (NT Service\MSSQLSERVER) 不会添加到**执行卷维护任务**安全策略。 换而言之，在 SQL Server Azure 平台映像中不启用即时文件初始化。 将 SQL Server 服务帐户添加到**执行卷维护任务**安全策略后，请重新启动 SQL Server 服务。 使用此功能可能有一些安全注意事项。 有关详细信息，请参阅[数据库文件初始化](https://msdn.microsoft.com/library/ms175935.aspx)。

* **自动增长**被视为只是非预期增长的偶发情况。 请勿使用自动增长来管理数据和日志每天的增长。 如果使用自动增长，请使用大小开关预先增长文件。

* 请确保禁用**自动收缩**以避免可能对性能产生负面影响的不必要开销。

* 将所有数据库（包括系统数据库）转移到数据磁盘。 有关详细信息，请参阅 [Move System Databases](https://msdn.microsoft.com/library/ms345408.aspx)（移动系统数据库）。

* 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。 在 SQL Server 配置管理器中右键单击 SQL Server 实例并选择属性，即可实现此目的。 可以在“启动参数”选项卡中更改错误日志和跟踪文件设置。在“高级”选项卡中指定转储目录。以下屏幕截图显示了错误日志启动参数的位置。

    ![SQL 错误日志屏幕截图](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* 设置默认的备份和数据库文件位置。 使用本文中的建议，并在“服务器属性”窗口中进行更改。 有关说明，请参阅 [View or Change the Default Locations for Data and Log Files (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)（查看或更改数据和日志文件的默认位置 (SQL Server Management Studio)）。 以下屏幕截图演示了进行这些更改的位置。

    ![SQL 数据日志和备份文件](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* 建立锁定的页以减少 IO 和任何分页活动。 有关详细信息，请参阅 [Enable the Lock Pages in Memory Option (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)（启用在内存中锁定页面的选项 (Windows)）。

* 如果运行的是 SQL Server 2012，安装 Service Pack 1 Cumulative Update 10。 此更新包含在 SQL Server 2012 中执行“select into temporary table”语句时出现的 I/O 性能不良的修补程序。 有关信息，请参阅此[知识库文章](https://support.microsoft.com/kb/2958012)。

* 请考虑在传入/传出 Azure 时压缩所有数据文件。

## <a name="feature-specific-guidance"></a>功能特定指南

某些部署可以使用更高级的配置技术，获得更多的性能好处。 下面的列表主要介绍可帮助你实现更佳性能的一些 SQL Server 功能：

### <a name="backup-to-azure-storage"></a>备份到 Azure 存储
为在 Azure 虚拟机中运行的 SQL Server 执行备份时，可以使用 [SQL Server 备份到 URL](https://msdn.microsoft.com/library/dn435916.aspx)。 此功能是从 SQL Server 2012 SP1 CU2 开始提供的，在备份到附加数据磁盘时建议使用。 备份到 Azure 存储或从中还原时，请按照 [SQL Server 备份到 URL 最佳实践和故障排除以及从 Azure 存储中存储的备份还原](https://msdn.microsoft.com/library/jj919149.aspx)中提供的建议操作。 此外还可以使用 [Azure 虚拟机中 SQL Server 的自动备份](virtual-machines-windows-sql-automated-backup.md)自动执行这些备份。

对于 SQL Server 2012 以前版本，可以使用 [SQL Server 备份到 Azure 工具](https://www.microsoft.com/download/details.aspx?id=40740)。 此工具可以通过使用多个备份条带目标帮助提高备份吞吐量。

### <a name="sql-server-data-files-in-azure"></a>Azure 中的 SQL Server 数据文件

[Azure 中的 SQL Server 数据文件](https://msdn.microsoft.com/library/dn385720.aspx)这一新功能从 SQL Server 2014 开始提供。 在 SQL Server 上运行 Azure 中的数据文件，与使用 Azure 数据磁盘时的性能特征相当。

### <a name="failover-cluster-instance-and-storage-spaces"></a>故障转移群集实例和存储空间

如果正在使用存储空间，则在“确认”页上向群集添加节点时，请清除标记为“将所有符合条件的存储添加到群集”的复选框。 

![取消选中符合条件的存储](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

如果正在使用存储空间，且选中了“将所有符合条件的存储添加到群集”，Windows 会在群集进程中分离虚拟磁盘。 这样一来，这些虚拟磁盘将不会出现在磁盘管理器或资源管理器之中，除非从群集中删除存储空间，并使用 PowerShell 将其重新附加。 存储空间将多个磁盘集合到存储池中。 有关详细信息，请参阅[存储空间](/windows-server/storage/storage-spaces/overview)。

## <a name="next-steps"></a>后续步骤

有关存储和性能的详细信息，请参阅 [Azure VM 上的 SQL Server 的存储配置准则](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

有关安全最佳实践，请参阅 [Azure 虚拟机中 SQL Server 的安全注意事项](virtual-machines-windows-sql-security.md)。

查看 [Azure 虚拟机上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)中的其他 SQL Server 虚拟机文章。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)。
