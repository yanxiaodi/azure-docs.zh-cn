---
title: include 文件
description: include 文件
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 05/23/2019
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 7b9b30f1598f7e50d25b15aaf2fda896ee9e5012
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248929"
---
# <a name="enable-write-accelerator"></a>启用写入加速器

写入加速器是 M 系列虚拟机 (VM) 的磁盘功能，且只能与 Azure 托管磁盘一起在高级存储上使用。 顾名思义，该功能的目的是改善对 Azure 高级存储的写入操作的 I/O 延迟。 写入加速器非常适合需要更新日志文件，并以高性能方式将现代数据库保存到磁盘的情况。

通常公有云中的 M 系列 VM 可提供写入加速器。

## <a name="planning-for-using-write-accelerator"></a>使用写入加速器进行规划

应该对包含事务日志或 DBMS 重做日志的卷使用写入加速器。 建议不要对 DBMS 数据卷使用写入加速器，因为此功能已针对日志磁盘进行了优化。

只能配合 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)使用写入加速器。

> [!IMPORTANT]
> 对 VM 的操作系统磁盘启用写入加速器会重新启动该 VM。
>
> 若要针对不属于基于多个磁盘构建的，且使用 Windows 磁盘或卷管理器、Windows 存储空间、Windows 横向扩展文件服务器 (SOFS)、Linux LVM 或 MDADM 的现有 Azure 磁盘启用写入加速器，需要关闭访问该 Azure 磁盘的工作负荷。 必须关闭使用 Azure 磁盘的数据库应用程序。
>
> 若要针对基于多个 Azure 高级存储磁盘构建的现有卷，或者使用 Windows 磁盘或卷管理器、Windows 存储空间、Windows 横向扩展文件服务器 (SOFS)、Linux LVM 或 MDADM 的条带化卷启用或禁用写入加速器，必须通过单独的步骤，对构成该卷的所有磁盘启用或禁用写入加速器。 **在此类配置中启用或禁用写入加速器之前，请关闭 Azure VM**。

对于 SAP 相关的 VM 配置，应该不需要为 OS 磁盘启用写入加速器。

### <a name="restrictions-when-using-write-accelerator"></a>使用写入加速器时的限制

对 Azure 磁盘/VHD 使用写入加速器时，需遵循以下限制：

- 必须将高级磁盘缓存设置为“无”或“只读”。 不支持其他所有缓存模式。
- 启用了写入加速器的磁盘当前不支持快照。 在备份期间，Azure 备份服务会自动排除连接到 VM 且启用了写入加速器的磁盘。
- 仅较小的 I/O (<=512 KiB) 大小会采用加速路径。 在以下工作负荷情形下，I/O 写入到磁盘的更改不会采用加速路径：数据大容量加载或者数据在持久保存到存储之前，不同 DBMS 的事务日志缓冲区已较大程度上填满。

写入加速器在每个 VM 中支持的 Azure 高级存储 VHD 数目有限制。 当前限制为：

| VM SKU | 写入加速器磁盘数 | 每个 VM 的写入加速器磁盘 IOPS |
| --- | --- | --- |
| M208ms_v2, M208s_v2| 8 | 10000 |
| M128ms、128s | 16 | 20000 |
| M64ms、M64ls、M64s | 8 | 10000 |
| M32ms、M32ls、M32ts、M32s | 4 | 5000 |
| M16ms、M16s | 2 | 2500 |
| M8ms、M8s | 第 | 1250 |

IOPS 限制是针对每个 VM 而不是每个磁盘  。 对于每个 VM，所有写入加速器磁盘具有相同的 IOPS 限制。

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>在特定磁盘上启用写入加速器

以下几个部分介绍如何在 Azure 高级存储 VHD 上启用写入加速器。

### <a name="prerequisites"></a>必备组件

目前，使用写入加速器必须满足以下先决条件：

- 要向其应用 Azure 写入加速器的磁盘需是高级存储上的 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。
- 必须使用 M 系列 VM

## <a name="enabling-azure-write-accelerator-using-azure-powershell"></a>使用 Azure PowerShell 启用 Azure 写入加速器

Azure PowerShell 模块 5.5.0 和更高版本对相关的 cmdlet 做了更改，以便能够针对特定的 Azure 高级存储磁盘启用或禁用写入加速器。
为了启用或部署写入加速器支持的磁盘，以下 PowerShell 命令已发生更改，并已扩展为接受写入加速器的参数。

已将新的开关参数 **-WriteAccelerator** 添加到以下 cmdlet：

- [Set-AzVMOsDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk?view=azurermps-6.0.0)
- [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMDataDisk?view=azurermps-6.0.0)
- [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVMDataDisk?view=azurermps-6.0.0)
- [Add-AzVmssDataDisk](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVmssDataDisk?view=azurermps-6.0.0)

不指定该参数会将属性设置为 false，并且会部署不受写入加速器支持的磁盘。

已将新的开关参数 **-OsDiskWriteAccelerator** 添加到以下 cmdlet：

- [Set-AzVmssStorageProfile](https://docs.microsoft.com/powershell/module/az.compute/Set-AzVmssStorageProfile?view=azurermps-6.0.0)

默认情况下，不指定该参数会将属性设置为 false，并且会返回不利用写入加速器的磁盘。

已将新的可选布尔参数（不可为 null） **-OsDiskWriteAccelerator** 添加到以下 cmdlet：

- [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVM?view=azurermps-6.0.0)
- [Update-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/Update-AzVmss?view=azurermps-6.0.0)

指定 $true 或 $false 可以控制磁盘对 Azure 写入加速器的支持。

示例命令如下所示：

```powershell
New-AzVMConfig | Set-AzVMOsDisk | Add-AzVMDataDisk -Name "datadisk1" | Add-AzVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVM

Get-AzVM | Update-AzVM -OsDiskWriteAccelerator $true

New-AzVmssConfig | Set-AzVmssStorageProfile -OsDiskWriteAccelerator | Add-AzVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzVmss

Get-AzVmss | Update-AzVmss -OsDiskWriteAccelerator:$false
```

以下部分了演示了两个主要场景的脚本编写方式。

### <a name="adding-a-new-disk-supported-by-write-accelerator-using-powershell"></a>使用 PowerShell 添加写入加速器支持的新磁盘

可以使用此脚本将新磁盘添加到 VM。 使用此脚本创建的磁盘将使用写入加速器。

将 `myVM`、`myWAVMs`、`log001`、磁盘大小和磁盘的 LunID 替换为适用于特定部署的值。

```powershell
# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

### <a name="enabling-write-accelerator-on-an-existing-azure-disk-using-powershell"></a>使用 PowerShell 在现有 Azure 磁盘上启用写入加速器

可以使用以下脚本在现有磁盘上启用写入加速器。 将 `myVM`、`myWAVMs` 和 `test-log001` 替换为适用于特定部署的值。 该脚本会将写入加速器添加到 **$newstatus** 值设置为“$true”的现有磁盘。 使用 $false 值会在给定的磁盘上禁用写入加速器。

```powershell
#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzVM -ResourceGroupName $rgname -VM $vm
```

> [!Note]
> 执行上述脚本会分离指定的磁盘，针对该磁盘启用写入加速器，然后重新附加该磁盘

## <a name="enabling-write-accelerator-using-the-azure-portal"></a>使用 Azure 门户启用写入加速器

可以通过指定磁盘缓存设置的门户启用写入加速器：

![Azure 门户中的写入加速器](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)

## <a name="enabling-write-accelerator-using-the-azure-cli"></a>使用 Azure CLI 启用写入加速器

可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 来启用写入加速器。

若要在现有磁盘上启用写入加速器，请使用 [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update)；若要将 diskName、VMName 和 ResourceGroup 替换为自己的值，可使用以下示例：`az vm update -g group1 -n vm1 -write-accelerator 1=true`

若要附加启用了写入加速器的磁盘，请使用 [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk?view=azure-cli-latest#az-vm-disk-attach)；若要替换为自己的值，可使用以下示例：`az vm disk attach -g group1 -vm-name vm1 -disk d1 --enable-write-accelerator`

若要禁用写入加速器，请使用 [az vm update](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-update) 将属性设置为 false：`az vm update -g group1 -n vm1 -write-accelerator 0=false 1=false`

## <a name="enabling-write-accelerator-using-rest-apis"></a>使用 Rest API 启用写入加速器

若要通过 Azure REST API 进行部署，需要安装 Azure armclient。

### <a name="install-armclient"></a>安装 armclient

若要运行 armclient，需要通过 Chocolatey 安装它。 可以通过 cmd.exe 或 PowerShell 来安装它。 使用提升的权限执行这些命令（“以管理员身份运行”）。

使用 cmd.exe 运行以下命令：`@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"`

使用 Power Shell 运行以下命令：`Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))`

现在，可以在 cmd.exe 或 PowerShell 中使用以下命令来安装 armclient：`choco install armclient`

### <a name="getting-your-current-vm-configuration"></a>获取当前的 VM 配置

若要更改磁盘配置的属性，首先需要获取 JSON 文件中的当前配置。 可以执行以下命令来获取当前配置：`armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>`

将“<<   >>”中的内容替换为自己的数据，包括 JSON 文件应使用的文件名。

输出可能如下所示：

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

接下来，更新 JSON 文件并在名为“log1”的磁盘上启用写入加速器。 可以通过将此属性添加到 JSON 文件中磁盘缓存条目的后面来完成此步骤。

```JSON
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

然后使用以下命令更新现有部署：`armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>`

输出应如下所示。 可以看到，为一个磁盘启用了写入加速器。

```JSON
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "writeAcceleratorEnabled": true,
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"
```

进行这项更改后，写入加速器应会支持该驱动器。
