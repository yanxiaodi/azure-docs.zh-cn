---
title: 使用 Azure 备份服务器将工作负荷备份到 Azure
description: 使用 Azure 备份服务器保护工作负荷或将其备份到 Azure 门户。
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: dacurwin
ms.openlocfilehash: 3f427726a128eed426a64bc533075ba0cdde9544
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241094"
---
# <a name="install-and-upgrade-azure-backup-server"></a>安装和升级 Azure 备份服务器

> [!div class="op_single_selector"]
> * [Azure 备份服务器](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> 适用于：MABS v3。 （不再支持 MABS v2。 如果你使用的版本早于 MABS v3，请升级到最新版本。）

本文介绍如何准备环境，以使用 Microsoft Azure 备份服务器 (MABS) 来备份工作负荷。 使用 Azure 备份服务器，可以通过一个控制台保护应用程序工作负载，如 Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange 和 Windows 客户端。

> [!NOTE]
> Azure 备份服务器现在可以保护 VMware VM 并提供改进的安全功能。 请按以下各部分所述安装该产品以及最新的 Azure 备份代理。 若要详细了解如何使用 Azure 备份服务器备份 VMware 服务器，请参阅[使用 Azure 备份服务器备份 VMware 服务器](backup-azure-backup-server-vmware.md)一文。 若要了解安全功能，请参阅 [Azure 备份安全功能文档](backup-azure-security-feature.md)。
>
>

部署在 Azure VM 中的 MABS 可以在 Azure 中备份 Vm，但这些 Vm 应该位于同一个域中，以启用备份操作。 备份 Azure VM 的过程仍与在本地备份 VM 的过程相同，但在 Azure 中部署 MABS 有一些限制。 有关限制的详细信息，请参阅[DPM 作为 Azure 虚拟机](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> Azure 有两种用于创建和使用资源的部署模型：[资源管理器部署模型和经典部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文提供有关还原使用 Resource Manager 模型部署的 VM 的信息和过程。
>
>

Azure 备份服务器从 Data Protection Manager (DPM) 继承了大量工作负荷备份功能。 此文章链接到 DPM 文档，以介绍一些共享功能。 尽管 Azure 备份服务器与 DPM 有许多相同的功能，但 Azure 备份服务器无法备份到磁带，也不与 System Center 集成。

## <a name="choose-an-installation-platform"></a>选择安装平台

若要启动并运行 Azure 备份服务器，首先要设置 Windows Server。 服务器可在 Azure 中或者在本地。

### <a name="using-a-server-in-azure"></a>使用 Azure 中的服务器

选择用于运行 Azure 备份服务器的服务器时，建议从 Windows Server 2016 Datacenter 或 Windows Server 2019 Datacenter 的库映像开始。 [在 Azure 门户中创建第一个 Windows 虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)一文提供了如何在 Azure 中开始使用建议的虚拟机的教程，即使以前从未使用过 Azure 也没关系。 建议服务器虚拟机 (VM) 最低要求应为：具有四个核心和 8 GB RAM 的 Standard_A4_v2。

使用 Azure 备份服务器保护工作负荷有许多细微差异需要注意。 可通过[将 DPM 安装为 Azure 虚拟机](https://technet.microsoft.com/library/jj852163.aspx)一文了解这些细微差异。 部署计算机前，请先阅读完本文。

### <a name="using-an-on-premises-server"></a>使用本地服务器

如果不希望在 Azure 中运行基本服务器，则可以在 Hyper-V VM、VMware VM 或物理主机上运行服务器。 建议的服务器硬件最低要求为2核和 8 GB RAM。 下表列出了支持的操作系统：

| 操作系统 | 平台 | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 位 |Standard、Datacenter、Essentials |
| Windows Server 2016 和最新的 SP |64 位 |Standard、Datacenter、Essentials  |


可以使用 Windows Server 重复数据删除来删除 DPM 存储中的重复数据。 了解有关在 Hyper-V VM 中部署时 [DPM 和重复数据删除](https://technet.microsoft.com/library/dn891438.aspx)如何配合工作的详细信息。

> [!NOTE]
> Azure 备份服务器设计为在专用的单一用途服务器上运行。 不能在以下计算机上安装 Azure 备份服务器：
> - 作为域控制器运行的计算机
> - 安装了应用程序服务器角色的计算机
> - 作为 System Center Operations Manager 管理服务器的计算机
> - 运行 Exchange Server 的计算机
> - 作为群集节点的计算机

始终将 Azure 备份服务器加入域。 如果计划将服务器移到其他域，请先安装 Azure 备份服务器，然后将服务器加入到新域。 部署之后，*不支持*将现有 Azure 备份服务器计算机移到新域中。

无论是将备份数据发送到 Azure 还是在本地保留，都必须将 Azure 备份服务器注册到恢复服务保管库。

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>设置存储复制

存储复制选项可让用户在异地冗余存储与本地冗余存储之间进行选择。 默认情况下，恢复服务保管库使用异地冗余存储。 如果此保管库是主保管库，请保留异地冗余存储这一存储选项。 如果想要一个更便宜、但持久性不太高的选项，请选择本地冗余存储。 请参阅 [Azure 存储复制概述](../storage/common/storage-redundancy.md)部分，深入了解[异地冗余](../storage/common/storage-redundancy-grs.md)和[本地冗余](../storage/common/storage-redundancy-lrs.md)存储选项。

若要编辑存储复制设置，请执行以下操作：

1. 在“恢复服务保管库”边栏选项卡中，单击新保管库。 在“设置”部分下，单击“属性”。
2. 在“属性”中的“备份配置”下，单击“更新”。

3. 选择存储复制类型，然后单击“保存”。

     ![设置新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>软件包

### <a name="downloading-the-software-package"></a>下载软件包

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 如果已打开恢复服务保管库，请转到步骤 3。 如果未打开恢复服务保管库，而是位于 Azure 门户中，请在主菜单中单击“浏览”。

   * 在资源列表中，键入“恢复服务”。
   * 开始键入时，会根据输入筛选该列表。 出现“恢复服务保管库”时，请单击它。

     ![创建恢复服务保管库步骤 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     此时会显示恢复服务保管库列表。
   * 在恢复服务保管库列表中选择一个保管库。

     此时会打开选定的保管库仪表板。

     ![打开保管库边栏选项卡](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. 默认情况下会打开“设置”边栏选项卡。 如果“设置”边栏选项卡已关闭，请单击“设置”将它打开。

    ![打开保管库边栏选项卡](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. 单击“备份”打开“开始使用”向导。

    ![备份入门](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    在打开的“开始备份”边栏选项卡中，会自动选择“备份目标”。

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. 在“备份目标”边栏选项卡中，从“工作负荷的运行位置”菜单中选择“本地”。

    ![用作目标的“本地”和“工作负荷”](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    从 "**要备份的内容？"** 下拉菜单中，选择要使用 Azure 备份服务器保护的工作负荷，然后单击 **"确定"** 。

    “开始备份”向导可切换“准备基础结构”选项以将工作负荷备份到 Azure。

   > [!NOTE]
   > 如果只想备份文件和文件夹，建议使用 Azure 备份代理，并遵循[初步了解：备份文件和文件夹](backup-try-azure-backup-in-10-mins.md)一文中的指南。 如果要保护的不止是文件和文件夹，或者计划在将来扩大保护需求，请选择这些工作负荷。
   >
   >

    ![快速启动向导更改](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. 在打开的“准备基础结构”边栏选项卡中，单击用于安装 Azure 备份服务器和下载保管库凭据的“下载”链接。 在将 Azure 备份服务器注册到恢复服务保管库期间，请使用保管库凭据。 使用此链接转到“下载中心”，可从中下载软件包。

    ![为 Azure 备份服务器准备基础结构](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. 选择所有文件，并单击“**下一步**”。 下载 Microsoft Azure 备份下载页中的所有文件，并将所有文件放在同一个文件夹中。

    ![下载中心 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    由于所有文件的下载大小一起为 > 3G，在 10 Mbps 下载链接上，下载完成最多可能需要60分钟。

### <a name="extracting-the-software-package"></a>解压缩软件包

下载所有文件之后，单击 **MicrosoftAzureBackupInstaller.exe**。 这会启动“**Microsoft Azure 备份安装向导**”，并将安装程序文件解压缩到指定的位置。 继续运行向导，并单击“**解压缩**”按钮开始解压缩过程。

> [!WARNING]
> 提取安装程序文件需要至少 4 GB 的可用空间。
>
>

![Microsoft Azure 备份安装向导](./media/backup-azure-microsoft-azure-backup/extract/03.png)

解压缩过程完成后，请选中相应的框，以启动刚刚解压缩的 *setup.exe* 来开始安装 Microsoft Azure 备份服务器，并单击“**完成**”按钮。

### <a name="installing-the-software-package"></a>安装软件包

1. 单击“**Microsoft Azure 备份**”以启动安装向导。

    ![Microsoft Azure 备份安装向导](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. 在欢迎屏幕上，单击 "**下一步**" 按钮。 随后将转到“*先决条件检查*”部分。 在此屏幕上单击“检查”，以确定是否符合 Azure 备份服务器的硬件和软件先决条件。 如果完全符合所有先决条件，将看到一条指明计算机符合要求的消息。 单击“**下一步**”按钮。

    ![Azure 备份服务器 - 欢迎页和先决条件检查](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure 备份服务器需要 SQL Server Enterprise。 此外，如果你不想使用自己的 SQL，Azure 备份服务器安装包还会根据需要随附相应的 SQL Server 二进制文件。 在开始全新安装 Azure 备份服务器时，应该选择“**在此安装程序中安装新的 SQL Server 实例**”，并单击“**检查并安装**”按钮。 成功安装必备组件后，单击“**下一步**”。

    ![Azure 备份服务器 - SQL 检查](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    如果发生故障并且系统建议重新启动计算机，请按说明操作，并单击“**再次检查**”。 如果有任何 SQL 配置问题，请按照 SQL 准则重新配置 SQL，并使用现有的 SQL 实例重试安装/升级 MABS。

   > [!NOTE]
   > Azure 备份服务器不能与远程 SQL Server 实例配合使用。 Azure 备份服务器使用的实例需在本地。 如果对 MABS 使用现有的 SQL Server，MABS 安装程序仅支持使用 SQL Server 的命名实例。

   **手动配置**

   使用自己的 SQL 实例时，请务必将 builtin\Administrators 添加到 master 数据库的 sysadmin 角色。

    **使用 SQL 2017 时的 SSRS 配置**

    使用自己的 SQL 2017 实例时，需要手动配置 SSRS。 配置 SSRS 后，请确保 SSRS 的 *IsInitialized* 属性设置为 *True*。 如果此属性设置为 True，MABS 将假设已配置 SSRS，因此会跳过 SSRS 配置。

    对 SSRS 配置使用以下值： 
    - 服务帐户："使用内置帐户" 应该是网络服务
    - Web 服务 URL："虚拟目录" 应为 ReportServer_<SQLInstanceName>
    - 数据库：DatabaseName 应为 ReportServer $<SQLInstanceName>
    - Web 门户 URL："虚拟目录" 应为 Reports_<SQLInstanceName>

    [详细了解](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) SSRS 配置。

4. 提供 Microsoft Azure 备份服务器文件的安装位置，并单击“**下一步**”。

    ![Microsoft Azure 备份先决条件 2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    备份到 Azure 需要有暂存位置。 请确保暂存位置的空间至少为要备份到云的数据的 5%。 在磁盘保护方面，安装完成之后需要配置独立的磁盘。 有关存储池的详细信息，请参阅[配置存储池和磁盘存储](https://technet.microsoft.com/library/hh758075.aspx)。
5. 为受限的本地用户帐户提供强密码，并单击“**下一步**”。

    ![Microsoft Azure 备份先决条件 2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. 选择是否要使用 *Microsoft 更新*来检查更新，并单击“**下一步**”。

   > [!NOTE]
   > 我们建议让 Windows 更新重定向到 Microsoft 更新，此网站为 Windows 和 Microsoft Azure 备份服务器等其他产品提供了安全更新与重要更新。
   >
   >

    ![Microsoft Azure 备份先决条件 2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. 复查“*设置摘要*”，并单击“**安装**”。

    ![Microsoft Azure 备份先决条件 2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. 安装会分阶段进行。 在第一阶段中，Microsoft Azure 恢复服务代理安装在服务器上。 向导还会检查 Internet 连接。 如果可以连接到 Internet，则可以继续安装，否则需要提供代理详细信息以连接到 Internet。

    下一个步骤是配置 Microsoft Azure 恢复服务代理。 在配置过程中，必须提供保管库凭据，以向恢复服务保管库注册计算机。 还需要提供通行短语来加密/解密 Azure 与本地之间发送的数据。 可以自动生成通行短语，或提供自己的通行短语（最少包含 16 个字符）。 请继续运行向导，直到代理已完成配置。

    ![Azure 备份服务器先决条件 2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Microsoft Azure 备份服务器注册成功完成后，整个安装向导将继续安装和配置 SQL Server 及 Azure 备份服务器的组件。 SQL Server 组件安装完成后，将安装 Azure 备份服务器组件。

    ![Azure 备份服务器](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

安装步骤完成后，会一同创建产品的桌面图标。 双击该图标即可启动该产品。

### <a name="add-backup-storage"></a>添加备份存储

第一个备份副本保存在已附加到 Azure 备份服务器计算机的存储中。 有关添加磁盘的详细信息，请参阅[配置存储池和磁盘存储](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage)。

> [!NOTE]
> 即使你打算将数据发送到 Azure，也需要添加备份存储。 在当前的 Azure 备份服务器体系结构中，Azure 备份保管库将保存数据的*第二个*副本，而本地存储将保存第一个（必需的）备份副本。
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>安装和更新 Data Protection Manager 保护代理

MABS 使用 System Center Data Protection Manager 保护代理。 [此处](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807)介绍了在保护服务器上安装保护代理的步骤。

以下部分介绍如何更新客户端计算机的保护代理。

1. 在备份服务器管理员控制台中，选择“管理” > “代理”。

2. 在显示窗格中，选择要为其更新保护代理的客户端计算机。

   > [!NOTE]
   > “代理更新”列指示每个受保护计算机何时有保护代理更新可用。 在“操作”窗格中，仅当选择了受保护计算机并且有可用更新时，“更新”操作才可用。
   >
   >

3. 要在所选计算机上安装更新的保护代理，请在“操作”窗格中，选择“更新”。

4. 对于未连接到网络的客户端计算机，在计算机连接到网络之前，“代理状态”列会显示“挂起更新”状态。

   在客户端计算机连接到网络之后，客户端计算机的“代理更新”列会显示“正在更新”状态。

## <a name="move-mabs-to-a-new-server"></a>将 MABS 移到新服务器

如果需要将 MABS 移到新服务器，同时保留存储，请执行以下步骤。 仅当所有数据都在新式备份存储中时，才能执行此操作。


  > [!IMPORTANT]
  > - 新服务器的名称必须与原始 Azure 备份服务器实例的名称相同。 若要使用以前的存储池和 MABS 数据库(DPMDB)保留恢复点，则不能更改新 Azure 备份服务器实例的名称。
  > - 必须有 MABS 数据库 (DPMDB) 的备份。 稍后需要还原数据库。

1. 在显示窗格中，选择要为其更新保护代理的客户端计算机。
2. 关闭原始 Azure 备份服务器或将其从线路断开。
3. 重置 Active Directory 中的计算机帐户。
4. 在新计算机上安装 Server 2016 并将其命名为与原始 Azure 备份服务器相同的计算机名称。
5. 加入域
6. 安装 Azure 备份服务器 V3 或更高版本（从旧服务器移 MABS 存储池磁盘并导入）
7. 还原步骤 1 中创建的 DPMDB。
8. 将存储从原始备份服务器连接到新服务器。
9. 从 SQL 还原 DPMDB
10. 从新服务器 cd 的管理员命令行到 Microsoft Azure 备份安装位置和 bin 文件夹

    路径示例：C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. 若要进行 Azure 备份，请运行 DPMSYNC -SYNC

    如果已将新磁盘添加到 DPM 存储池（而不是移动旧磁盘），请运行 DPMSYNC -Reallocatereplica

## <a name="network-connectivity"></a>网络连接

Azure 备份服务器需要连接到 Azure 备份服务才能成功运行。 若要验证计算机是否已连接到 Azure，请在 Azure 备份服务器 PowerShell 控制台中使用 ```Get-DPMCloudConnection``` cmdlet。 如果该 cmdlet 的输出为 TRUE，则表示已建立连接，否则表示未建立连接。

同时，Azure 订阅必须处于正常运行状态。 若要了解订阅的状态并对其进行管理，请登录到[订阅门户](https://account.windowsazure.com/Subscriptions)。

了解 Azure 连接和 Azure 订阅的状态后，可以使用下表来确定提供的备份/还原功能受到了哪些影响。

| 连接状态 | Azure 订阅 | 备份到 Azure | 备份到磁盘 | 从 Azure 还原 | 从磁盘还原 |
| --- | --- | --- | --- | --- | --- |
| 已连接 |活动 |允许 |允许 |允许 |允许 |
| 已连接 |已过期 |已停止 |已停止 |允许 |允许 |
| 已连接 |已解除设置 |已停止 |已停止 |已停止且已删除 Azure 恢复点 |已停止 |
| 连接断开超过 15 天 |可用 |已停止 |已停止 |允许 |允许 |
| 连接断开超过 15 天 |Expired |已停止 |已停止 |允许 |允许 |
| 连接断开超过 15 天 |已取消预配 |已停止 |已停止 |已停止且已删除 Azure 恢复点 |已停止 |

### <a name="recovering-from-loss-of-connectivity"></a>连接断开后进行恢复

如果防火墙或代理导致无法访问 Azure，则需要在防火墙/代理配置文件中允许以下域地址：

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

在 Azure 备份服务器计算机上还原与 Azure 的连接之后，可执行的操作取决于 Azure 订阅状态。 上表详细列出了有关计算机在“连接”之后允许的操作的信息。

### <a name="handling-subscription-states"></a>处理订阅状态

可以将 Azure 订阅从“*已过期*”或“*已取消预配*”状态更改为“*活动*”状态。 但是，当状态不是“活动”时，此操作对产品的行为会造成某些影响：

* “*已取消预配*”的订阅在取消预配的这段期间将失去功能。 切换为“*活动*”后，将恢复产品的备份/还原功能。 此外，只要以够长的保留期来保存本地磁盘上的备份数据，则还可以检索这些数据。 但是，一旦订阅进入“*已取消预配*”状态，Azure 中的备份数据便会丢失且不可检索。
* “*已过期*”的订阅只会在恢复“*活动*”状态之前失去功能。 在订阅处于“*已过期*”期间计划的任何备份都不会运行。

## <a name="upgrade-mabs"></a>升级 MABS

使用以下过程升级 MABS。

### <a name="upgrade-from-mabs-v2-to-v3"></a>从 MABS V2 升级到 V3

> [!NOTE]
>
> MABS V2 不是安装 MABS V3 的先决条件。 但是，只能从 MABS V2 升级到 MABS V3。

使用以下步骤升级 MABS：

1. 若要从 MABS V2 升级到 MABS V3，请根据需要将 OS 升级到 Windows Server 2016 或 Windows Server 2019。

2. 升级服务器。 这些步骤类似于[安装](#install-and-upgrade-azure-backup-server)。 但是，在进行 SQL 设置时，可以通过一个选项将 SQL 实例升级到 SQL 2017，或使用自己的 SQL Server 2017 实例。

   > [!NOTE]
   >
   > 升级 SQL 实例期间请不要退出，否则会卸载 SQL 报告实例，导致重新升级 MABS 的尝试失败。

   需要注意的几个要点：

   > [!IMPORTANT]
   >
   >  在升级到 SQL 2017 的过程中，我们会备份 SQL 加密密钥并卸载报告服务。 升级 SQL Server 后，将安装报告服务 (14.0.6827.4788) 并还原加密密钥。
   >
   > 手动配置 SQL 2017 时，请参阅“安装说明”下的“使用 SQL 2017 时的 SSRS 配置”部分。

3. 在受保护的服务器上更新保护代理。
4. 备份应会继续，而无需重启生产服务器。
5. 现在，可以开始保护数据。 如果在保护状态下升级到新式备份存储，则还可以选择备份要存储到的卷，并检查预配不足的空间。 [了解详细信息](backup-mabs-add-storage.md)。

## <a name="troubleshooting"></a>疑难解答

如果 Microsoft Azure 备份服务器在安装阶段（或者备份或还原时）失败并出现错误，请参阅此[错误代码文档](https://support.microsoft.com/kb/3041338)以获取详细信息。
此外，还可以参考 [Azure 备份相关的常见问题](backup-azure-backup-faq.md)

## <a name="next-steps"></a>后续步骤

可以在 Microsoft TechNet 站点上获取有关[为 DPM 准备环境](https://technet.microsoft.com/library/hh758176.aspx)的详细信息。 其中还包含有关可在其上部署和使用 Azure 备份服务器的受支持配置的信息。 可以使用一系列 [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) 来执行各种操作。

请参阅这些文章，以深入了解如何使用 Microsoft Azure 备份服务器来保护工作负荷。

* [SQL Server 备份](backup-azure-backup-sql.md)
* [SharePoint Server 备份](backup-azure-backup-sharepoint.md)
* [备用服务器备份](backup-azure-alternate-dpm-server.md)
