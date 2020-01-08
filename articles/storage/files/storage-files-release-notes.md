---
title: Azure 文件同步代理发行说明 | Microsoft Docs
description: Azure 文件同步代理发行说明。
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 8/14/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 7286d8465d857b24c72c46e9d671abb83ccefc21
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259356"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 文件同步代理发行说明
借助 Azure 文件同步，既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 Windows Server 安装可转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上提供的任意协议（包括 SMB、NFS 和 FTPS）以本地方式访问数据， 并且可以根据需要在世界各地设置多个缓存。

本文提供受支持的 Azure 文件同步代理版本的发行说明。

## <a name="supported-versions"></a>支持的版本
以下版本是 Azure 文件同步代理支持的：

| 里程碑 | 代理版本号 | 发布日期 | 状态 |
|----|----------------------|--------------|------------------|
| 2019年7月更新汇总- [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019 年 7 月 24 日 | 支持 |
| 2019年7月更新汇总- [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 2019年7月12日 | 支持 |
| V7 版本- [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 2019年6月19日 | 支持 |
| 2019年6月更新汇总- [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 2019年6月27日 | 支持 |
| 2019年6月更新汇总- [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 2019 年 6 月 13 日 | 支持 |
| 5月2019更新汇总- [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 2019 年 5 月 7 日 | 支持 |
| V6 版本- [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 2019年4月21日 | 支持 |
| 2019年4月更新汇总- [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 2019年4月4日 | 支持 |
| 2019年3月更新汇总- [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 2019年3月7日 | 支持 |
| V5 版本 - [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 2019 年 2 月 12 日 | 支持 |
| 2019 年 1 月更新汇总 - [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 2019 年 1 月 14 日 | 支持的代理版本将于2019年11月5日过期 |
| 2018 年 12 月更新汇总 - [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 2018 年 12 月 10 日 | 支持的代理版本将于2019年11月5日过期 |
| 2018 年 12 月更新汇总 | 4.1.0.0 | 2018 年 12 月 4 日 | 支持的代理版本将于2019年11月5日过期 |
| V4 版本 | 4.0.1.0 | 2018 年 11 月 13 日 | 支持的代理版本将于2019年11月5日过期 |
| V3 版本 | 3.1.0.0-为3.4.0。0 | 不受支持 | 不支持-代理版本在2019年8月19日过期 |
| 预发行版代理 | 1.1.0.0 - 3.0.13.0 | 不可用 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-7200"></a>代理版本7.2.0。0
以下发行说明适用于2019年7月24日发布的 Azure 文件同步代理的版本7.2.0.0。 除了为版本7.0.0.0 列出的发行说明外，还提供了这些说明。

此版本修复的问题列表：  
- 如果代理配置为 null，则存储同步代理（FileSyncSvc）崩溃。
- 如果服务器上有多个终结点具有相同的名称，服务器终结点将启动 BCDR （错误 0x80c80257-ECS_E_BCDR_IN_PROGRESS）。
- 云分层可靠性改进。

## <a name="agent-version-7100"></a>代理版本7.1.0。0
以下发行说明适用于2019年7月12日发布的 Azure 文件同步代理的版本7.1.0.0。 除了为版本7.0.0.0 列出的发行说明外，还提供了这些说明。

此版本修复的问题列表：  
- 在 Windows Server 2012 R2 上访问或浏览 SMB 上的服务器终结点位置速度缓慢。 
- 安装 Azure 文件同步 v6 代理后增加了 CPU 使用率。
- 云分层遥测改进。
- 针对云分层和同步的其他可靠性改进。

## <a name="agent-version-7000"></a>代理版本7.0.0。0
以下发行说明适用于 Azure 文件同步代理的版本7.0.0.0 （发布时间为2019年6月19日）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 支持较大的文件共享大小
    - 预览更大的 Azure 文件共享，我们也增加了对文件同步的支持限制。 在第一步中，Azure 文件同步现在最多可在一个同步命名空间中支持 25 TB 和50000000文件。 若要申请大型文件共享预览，请填写此窗体 https://aka.ms/azurefilesatscalesurvey 。 
- 支持存储帐户上的防火墙和虚拟网络设置
    - Azure 文件同步现在支持存储帐户上的防火墙和虚拟网络设置。 若要将部署配置为使用防火墙和虚拟网络设置，请参阅[配置防火墙和虚拟网络设置](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)。
- 用于立即同步 Azure 文件共享中已更改文件的 PowerShell cmdlet
    - 若要立即同步 Azure 文件共享中已更改的文件，可使用 AzStorageSyncChangeDetection PowerShell cmdlet 手动启动对 Azure 文件共享中的更改的检测。 此 cmdlet 适用于某些类型的自动化过程在 Azure 文件共享中进行更改或由管理员完成更改的情况（如将文件和目录移动到共享中）。 对于最终用户的更改，建议在 IaaS VM 中安装 Azure 文件同步代理，并让最终用户通过 IaaS VM 访问文件共享。 这样一来，所有更改都将快速同步到其他代理，而无需使用 AzStorageSyncChangeDetection cmdlet。 若要了解详细信息，请参阅[AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)文档。
- 改进的门户体验（如果遇到未同步的文件）
    - 如果你有未能同步的文件，现在我们会在门户中区分暂时性错误和永久性错误。 暂时性错误通常会自行解决，而无需执行管理操作。 例如，当前正在使用的文件在文件句柄关闭之前将不会同步。 对于持久性错误，我们现在会显示受每个错误影响的文件数。 持久性错误计数还显示在同步组中所有服务器终结点的 "文件未同步" 列中。
- 改进了 Azure 备份文件级还原
    - 现在会检测使用 Azure 备份还原的单个文件并将其同步到服务器终结点。
- 提高了云分层召回 cmdlet 的可靠性 
    - 云分层召回 cmdlet （StorageSyncFileRecall）现在支持每个文件重试计数和重试延迟，这与 robocopy 类似。
- 仅支持 TLS 1.2 （禁用 TLS 1.0 和1.1）
    - Azure 文件同步现在仅支持在禁用了 TLS 1.0 和1.1 的服务器上使用 TLS 1.2。 在此改进之前，如果服务器上禁用了 TLS 1.0 和1.1，服务器注册将失败。
- 同步和云分层的其他性能和可靠性改进
    - 在此版本中，有几个可靠性和性能改进。 其中一些功能旨在使云分层更高效，并在这些情况下更好地 Azure 文件同步。

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- Nano Server 部署选项不支持代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，这可能会导致意外的结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的自定义访问控制列表 (DACL) 部分（如果其大小大于 2 KB）。 （只有在单个项上的访问控制条目 (ACE) 大于某个数（大约为 40）的情况下，这才是一个问题。）
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。

## <a name="agent-version-6300"></a>代理版本6.3.0。0
以下发行说明适用于2019年6月27日发布的 Azure 文件同步代理的版本6.3.0.0。 除了为版本6.0.0.0 列出的发行说明外，还提供了这些说明。

此版本修复的问题列表：  
- Windows Server 2012 R2 上的 Windows Server endpoint location 访问或浏览 SMB 速度缓慢 
- 安装 Azure 文件同步 v6 代理后增加了 CPU 使用率
- 云分层遥测改进

## <a name="agent-version-6200"></a>代理版本6.2.0。0
以下发行说明适用于2019年6月13日发布的 Azure 文件同步代理的版本6.2.0.0。 除了为版本6.0.0.0 列出的发行说明外，还提供了这些说明。

此版本修复的问题列表：  
- 创建服务器终结点后，当后台回调将文件下载到服务器时，可能会出现高 CPU 使用率
- 由于令牌过期，同步和云分层操作可能会失败并出现错误 ECS_E_SERVER_CREDENTIAL_NEEDED
- 如果下载文件的 URL 包含保留字符，则撤回文件可能会失败 

## <a name="agent-version-6100"></a>代理版本6.1.0。0
以下发行说明适用于2019年5月6日发布的 Azure 文件同步代理的版本6.1.0.0。 除了为版本6.0.0.0 列出的发行说明外，还提供了这些说明。

此版本修复的问题列表：  
- Windows 管理中心无法显示安装了 Azure 文件同步代理版本6.0 的服务器上的代理版本和服务器终结点配置。

## <a name="agent-version-6000"></a>代理版本6.0.0。0
以下发行说明适用于 Azure 文件同步代理的版本6.0.0.0 （发布于2019年4月22日）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 代理自动更新支持
  - 我们已听取你的反馈，并已将自动更新功能添加到 Azure 文件同步服务器代理中。 有关详细信息，请参阅[Azure 文件同步代理更新策略](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy)。
- 支持 Azure 文件共享 Acl
  - Azure 文件同步始终支持在服务器终结点之间同步 Acl，但 Acl 未同步到云终结点（Azure 文件共享）。 此版本添加了对在服务器和云终结点之间同步 Acl 的支持。
- 并行上传和下载服务器终结点的同步会话 
  - 服务器终结点现在支持同时上载和下载文件。 不再等待下载完成，因此文件可以上传到 Azure 文件共享。 
- 用于获取卷和分层状态的新云分层 cmdlet
  - 现在可以使用两个新的服务器本地 PowerShell cmdlet 来获取云分层和文件撤回信息。 它们使服务器上的两个事件通道中的日志记录信息可用：
    - StorageSyncFileTieringResult 将列出尚未进行分层的所有文件及其路径，并报告原因。
    - StorageSyncFileRecallResult 报告所有文件撤回事件。 它列出回调的每个文件及其路径以及该回调的成功或错误。
  - 默认情况下，两个事件通道最多可存储 1 MB –你可以通过增加事件通道大小增加报告的文件量。
- 支持 FIPS 模式
  - Azure 文件同步现在支持在安装了 Azure 文件同步代理的服务器上启用 FIPS 模式。
    - 在服务器上启用 FIPS 模式之前，请在服务器上安装 Azure 文件同步 agent 和[PackageManagement 模块](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2)。 如果已在服务器上启用了 FIPS，请手动将[PackageManagement 模块](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2)[下载](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download)到你的服务器。
- 云分层和同步的其他可靠性改进

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- Nano Server 部署选项不支持代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，那样做会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的自定义访问控制列表 (DACL) 部分（如果其大小大于 2 KB）。 （只有在单个项上的访问控制条目 (ACE) 大于某个数（大约为 40）的情况下，这才是一个问题。）
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。
- 从 SMB 客户端查看文件属性时，脱机属性可能会显示为设置不正确，因为对文件元数据进行了 SMB 缓存。

## <a name="agent-version-5200"></a>代理版本5.2.0。0
以下发行说明适用于2019年4月4日发布的 Azure 文件同步代理的版本5.2.0.0。 除了为版本5.0.2.0 列出的发行说明外，还提供了这些说明。

此版本修复的问题列表：  
- 脱机数据传输和数据传输恢复功能的可靠性改进
- 同步遥测改进

## <a name="agent-version-5100"></a>代理版本5.1.0。0
以下发行说明适用于2019年3月7日发布的 Azure 文件同步代理的版本5.1.0.0。 除了为版本5.0.2.0 列出的发行说明外，还提供了这些说明。

此版本修复的问题列表：  
- 如果在服务器上更改枚举失败，则文件可能无法同步，并出现错误0x80c8031d （ECS_E_CONCURRENCY_CHECK_FAILED）
- 如果同步会话或文件收到错误0x80072f78 （WININET_E_INVALID_SERVER_RESPONSE），同步将立即重试该操作
- 文件可能无法同步，并出现错误0x80c80203 （ECS_E_SYNC_INVALID_STAGED_FILE）
- 撤回文件时可能会出现高内存使用率
- 云分层遥测改进 

## <a name="agent-version-5020"></a>代理版本 5.0.2.0
以下发行说明适用于 Azure 文件同步代理版本 5.0.2.0（2019 年 2 月 12 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 支持 Azure 政府云
  - 我们增加了针对 Azure 政府云的预览版支持。 这需要将订阅加入允许列表，并从 Microsoft 下载特殊代理。 若要访问预览版，请将电子邮件直接发送至 [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)。
- 支持重复数据删除
    - Windows Server 2016 和 Windows Server 2019 现在完全支持重复数据删除并启用了云分层功能。 在启用了云分层的卷上启用重复数据删除以后，即可在本地缓存更多的文件，不需预配更多的存储。
- 支持脱机数据传输（例如，通过 Data Box 进行的脱机数据传输）
    - 轻松地通过任何所选手段将大量数据迁移到 Azure 文件同步。 您可以选择 Azure Data Box、AzCopy 甚至第三方迁移服务。 不需消耗大量带宽将数据传输到 Azure。如果使用 Data Box，只需直接将其邮寄过去就可以了！ 若要了解详细信息，请参阅[脱机数据传输文档](https://aka.ms/AFS/OfflineDataTransfer)。
- 改进同步性能
    - 在此版本发布之前，如果在同一卷上有多个服务器终结点，客户会体验到同步性能下降。 Azure 文件同步每天在服务器上创建临时 VSS 快照一次，以同步包含开放句柄的文件。 现在，在 VSS 同步会话时处于活动状态时，Azure 文件同步支持多个服务器终结点在一个卷上进行同步。 完成 VSS 同步会话不再需要等待，因此可以在该卷的其他服务器终结点上继续同步。
- 改进在门户中进行的监视
    - 在存储同步服务门户中添加了图表，用于查看：
        - 已同步的文件数
        - 已传输数据的大小
        - 未同步文件的数目
        - 回调数据的大小
        - 服务器连接状态
    - 若要了解详细信息，请参阅[监视 Azure 文件同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring)。
- 提高了可伸缩性和可靠性
    - 一个目录中文件系统对象（目录和文件）的最大数目已增加到 1,000,000。 以前的限制为 200,000。
    - 当传输因文件过大而中断时，Azure 文件同步会继续进行数据传输而不会重新传输。 

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- 此代理在 Windows Server Core 或 Nano Server 部署选项上不受支持。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。
- FIPS 模式不受支持，必须禁用。 

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，那样做会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的自定义访问控制列表 (DACL) 部分（如果其大小大于 2 KB）。 （只有在单个项上的访问控制条目 (ACE) 大于某个数（大约为 40）的情况下，这才是一个问题。）
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。
- 从 SMB 客户端查看文件属性时，脱机属性可能会显示为设置不正确，因为对文件元数据进行了 SMB 缓存。

## <a name="agent-version-4300"></a>代理版本 4.3.0.0
以下发行说明适用于 Azure 文件同步代理版本 4.3.0.0（2019 年 1 月 14 日发布）。 这些说明是对针对版本 4.0.1.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 将 Azure 文件同步代理升级到版本 4.x 后，文件将不会分层。
- Windows Server 2019 现在支持 AfsUpdater.exe。
- 针对同步的其他可靠性改进。 

## <a name="agent-version-4200"></a>代理版本 4.2.0.0
以下发行说明适用于 Azure 文件同步代理版本 4.2.0.0（2018 年 12 月 10 日发布）。 这些说明是对针对版本 4.0.1.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 创建 VSS 快照时，可能会发生停止错误 0x3B 或停止错误 0x1E。  
- 启用云分层时，可能会发生内存泄漏  

## <a name="agent-version-4100"></a>代理版本 4.1.0.0
以下发行说明适用于 Azure 文件同步代理版本 4.1.0.0（2018 年 12 月 4 日发布）。 这些说明是对针对版本 4.0.1.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 服务器可能因云分层内存泄漏而无响应。  
- 代理安装失败，显示以下错误：错误 1921。 无法停止服务“存储同步代理”(FileSyncSvc)。  请确保有足够的权限停止系统服务。  
- 内存使用率很高时，存储同步代理 (FileSyncSvc) 服务可能会崩溃。  
- 针对云分层和同步的其他可靠性改进。

## <a name="agent-version-4010"></a>代理版本 4.0.1.0
以下发行说明适用于 Azure 文件同步代理版本 4.0.1.0（2018 年 11 月 13 日发布）。

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- 此代理在 Windows Server Core 或 Nano Server 部署选项上不受支持。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。
- FIPS 模式不受支持，必须禁用。 
- 创建 VSS 快照时，可能会发生停止错误 0x3B 或停止错误 0x1E。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，那样做会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。
- 不支持在同一卷上进行重复数据删除和云分层操作。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的自定义访问控制列表 (DACL) 部分（如果其大小大于 2 KB）。 （只有在单个项上的访问控制条目 (ACE) 大于某个数（大约为 40）的情况下，这才是一个问题。）
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 基于日期的云分层策略设置用来指定如果在指定的天数内访问过文件，则应当缓存文件。 若要了解详细信息，请参阅[云分层概述](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering)。
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。
- 从 SMB 客户端查看文件属性时，脱机属性可能会显示为设置不正确，因为对文件元数据进行了 SMB 缓存。
