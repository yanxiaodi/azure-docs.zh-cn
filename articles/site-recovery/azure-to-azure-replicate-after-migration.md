---
title: 通过 Azure Site Recovery 在迁移到 Azure 后为 Azure Vm 设置灾难恢复
description: 本文介绍如何准备好计算机，以便在迁移到 Azure 后使用 Azure Site Recovery 设置 Azure 区域之间的灾难恢复。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: ff35c5e23c5d8a448d62a3eeb8d15ba8d5a531e4
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814530"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>设置 Azure VM 迁移到 Azure 后的灾难恢复 


如果已使用 [Site Recovery](site-recovery-overview.md) 服务[将本地计算机迁移到 Azure VM](tutorial-migrate-on-premises-to-azure.md)，请按本文操作。现在，需设置 VM，以便灾难恢复到辅助 Azure 区域。 本文介绍如何确保将 Azure VM 代理安装在迁移的 VM 上，以及如何删除在迁移后不再需要的 Site Recovery 移动服务。



## <a name="verify-migration"></a>验证迁移

设置灾难恢复之前，请确保已按预期完成迁移。 若要成功完成迁移，在故障转移后，应为要迁移的每台计算机选择“完成迁移”选项。 

## <a name="verify-the-azure-vm-agent"></a>验证 Azure VM 代理

每个 Azure VM 都必须安装 [Azure VM 代理](../virtual-machines/extensions/agent-windows.md)。 为了复制 Azure VM，Site Recovery 会在代理上安装一个扩展。

- 如果计算机运行 9.7.0.0 或更高版本的 Site Recovery 移动服务，则移动服务会将 Azure VM 代理自动安装在 Windows VM 上。 在更低版本的移动服务上，需自动安装该代理。
- 对于 Linux VM，必须手动安装 Azure VM 代理。仅当迁移计算机上安装的移动服务为 9.6 或更低版本时，才需要安装 Azure VM 代理。


### <a name="install-the-agent-on-windows-vms"></a>在 Windows VM 上安装代理

如果运行的 Site Recovery 移动服务的版本低于 9.7.0.0，或者因有其他需求而必须手动安装代理，则请执行以下操作：  

1. 确保在 VM 上有管理员权限。
2. 下载 [VM 代理安装程序](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
3. 运行安装程序文件。

#### <a name="validate-the-installation"></a>验证安装
检查是否已安装代理：

1. 在 Azure VM 上的 C:\WindowsAzure\Packages 文件夹中，应看到 WaAppAgent.exe 文件。
2. 右键单击该文件，在“属性”中选择“详细信息”选项卡。
3. 验证“产品版本”字段是否显示 2.6.1198.718 或更高版本。

[详细了解](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) Windows 的代理安装。

### <a name="install-the-agent-on-linux-vms"></a>在 Linux VM 上安装代理

手动安装 [Azure Linux VM](../virtual-machines/extensions/agent-linux.md) 代理，如下所示：

1. 确保在计算机上有管理员权限。
2. 强烈建议使用分发版包存储库中的 RPM 或 DEB 包安装 Linux VM 代理。 所有[认可的分发版提供商](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)会将 Azure Linux 代理包集成到其映像和存储库。
    - 强烈建议只通过分发存储库更新代理。
    - 我们不建议直接从 GitHub 安装 Linux VM 代理并将其更新。
    -  如果分发没有可用的最新代理，请联系分发支持部门，了解如何安装最新代理。 

#### <a name="validate-the-installation"></a>验证安装 

1. 运行 **ps -e** 命令，确保 Azure 代理可在 Linux VM 上运行：
2. 如果该进程未运行，请使用以下命令进行重启：
    - 对于 Ubuntu：**service walinuxagent start**
    - 对于其他发行版：**service waagent start**


## <a name="uninstall-the-mobility-service"></a>卸载移动服务

1. 使用以下方法之一从 Azure VM 上手动卸载移动服务。 
    - 对于 Windows，在控制面板中 >“添加/删除程序”，卸载“Microsoft Azure Site Recovery 移动服务/主目标服务器”。 在提升的命令提示符下，运行：
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - 对于 Linux，以根用户身份登录。 在终端中，转到 **/user/local/ASR**，运行以下命令：
        ```
        ./uninstall.sh -Y
        ```
2. 在配置复制之前，请重新启动 VM。

## <a name="next-steps"></a>后续步骤

[查看故障排除](site-recovery-extension-troubleshoot.md)，了解 Azure VM 代理上的 Site Recovery 扩展。
将 Azure VM [快速复制](azure-to-azure-quickstart.md)到次要区域。
