---
title: 在脱机模式下安装 Azure VM 代理 | Microsoft Docs
description: 了解如何在脱机模式下安装 Azure VM 代理。
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 438143d3253f1cab1afb958a90f427dcba59a98e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059237"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>在脱机模式下安装 Azure 虚拟机代理 

Azure 虚拟机代理（VM 代理）可提供多种有用的功能，例如本地管理员密码重置和脚本推送。 本文演示如何为脱机的 Windows 虚拟机 (VM) 安装 VM 代理。 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>何时在脱机模式下使用 VM 代理

如有以下情况，可在脱机模式下安装 VM 代理：

- 已部署的 Azure VM 未安装 VM 代理，或代理不能正常工作。
- 忘记了 VM 的管理员密码，或无法访问 VM。

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>如何在脱机模式下安装 VM 代理

可以使用以下步骤，在脱机模式下安装 VM 代理。

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>步骤 1：将 VM 的 OS 磁盘作为数据磁盘附加到另一 VM

1. 为受影响的 VM 的 OS 磁盘拍摄快照，从快照创建磁盘，然后将该磁盘附加到故障排除 VM。 有关详细信息，请参阅[使用 Azure 门户将 OS 磁盘附加到恢复 VM，对 WINDOWS VM 进行故障排除](troubleshoot-recovery-disks-portal-windows.md)。 对于经典 VM，请删除 VM 并保留 OS 磁盘，然后将 OS 磁盘附加到故障排除 VM。

2.  连接到故障排除 VM。 转到“计算机管理” > “磁盘管理”。 确认 OS 磁盘处于联机状态，并且已将驱动器号分配到磁盘分区。

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>步骤 2：修改 OS 磁盘以安装 Azure VM 代理

1.  远程桌面连接到故障排除 VM。

2.  在疑难解答 VM 中，浏览到已附加的 OS 磁盘，打开 \windows\system32\config 文件夹。 将此文件夹中的所有文件复制为备份，以备回滚之需。

3.  启动注册表编辑器 (regedit.exe)。

4.  选择“HKEY_LOCAL_MACHINE”项。 在菜单上，选择“文件” > “加载配置单元”：

    ![加载配置单元](./media/install-vm-agent-offline/load-hive.png)

5.  浏览到已附加 OS 磁盘上的 \windows\system32\config\SYSTEM 文件夹。 输入“BROKENSYSTEM”作为配置单元名称。 新的注册表配置单元将显示在“HKEY_LOCAL_MACHINE”项之下。

6.  浏览到已附加 OS 磁盘上的 \windows\system32\config\SOFTWARE 文件夹。 输入“BROKENSOFTWARE”作为配置单元软件。

7. 如果附加的 OS 磁盘安装了 VM 代理，请执行当前配置的备份。 如果未安装 VM 代理，请转到下一步。
      
    1. 将 \windowsazure 文件夹重命名为 \windowsazure.old。

    2. 导出以下注册表：
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  将故障排除 VM 上的现有文件用作 VM 代理安装的存储库。 完成以下步骤：

    1. 从故障排除 VM 中，以注册表格式 (.reg) 导出以下子项： 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureTelemetryService
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![导出注册表子项](./media/install-vm-agent-offline/backup-reg.png)

    2. 编辑注册表文件。 在每个文件中，将项值 SYSTEM改为 BROKENSYSTEM（如下图所示）并保存该文件。 请记住当前 VM 代理的 **ImagePath**。 我们将需要将相应的文件夹复制到附加的 OS 磁盘。 

        ![更改注册表子项值](./media/install-vm-agent-offline/change-reg.png)

    3. 双击每个注册表文件，将注册表文件导入存储库。

    4. 确认将以下三个子项成功导入 BROKENSYSTEM 配置单元：
        - WindowsAzureGuestAgent
        - WindowsAzureTelemetryService
        - RdAgent

    5. 将当前的 VM 代理的安装文件夹复制到附加的 OS 磁盘： 

        1.  在附加的 OS 磁盘上的根路径中创建一个名为 WindowsAzure 的文件夹。

        2.  转到故障排除 VM 上的 C:\WindowsAzure，查找名为“C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX”的任何文件夹。 将具有最新版本号的 GuestAgent 文件夹从 C:\WindowsAzure 复制到附加 OS 磁盘中的 WindowsAzure 文件夹。 如果不确定应复制哪个文件夹，请复制所有 GuestAgent 文件夹。 下图显示了复制到附加 OS 磁盘的 GuestAgent 文件夹的示例。

             ![复制 GuestAgent 文件夹](./media/install-vm-agent-offline/copy-files.png)

9.  选择“BROKENSYSTEM”。 在菜单上，选择“文件” > “卸载配置单元”

10.  选择“BROKENSOFTWARE”。 在菜单上，选择“文件” > “卸载配置单元”

11.  分离 OS 磁盘，然后[更改受影响的 VM 的 os 磁盘](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm)。 对于经典 VM，请使用修复的 OS 磁盘创建新的 VM。

12.  访问 VM。 请注意，RdAgent 正在运行，并且正在生成日志。

如果使用资源管理器部署模型创建了 VM，则无需再进行额外操作。

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>对于经典 VM，使用 ProvisionGuestAgent 属性

如果使用经典模型创建了 VM，请使用 Azure PowerShell 模块更新 ProvisionGuestAgent 属性。 该属性会通知 Azure 该 VM 已安装 VM 代理。

若要设置 ProvisionGuestAgent 属性，请在 Azure PowerShell 中运行以下命令：

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

然后运行 `Get-AzureVM` 命令。 请注意，GuestAgentStatus 属性现已得到数据填充：

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>后续步骤

- [Azure 虚拟机代理概述](../extensions/agent-windows.md)
- [适用于 Windows 的虚拟机扩展和功能](../extensions/features-windows.md)
