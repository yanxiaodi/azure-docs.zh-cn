---
title: 在 Azure 中为可缩放的应用程序创建 VM 和存储帐户 | Microsoft Docs
description: 了解如何使用 Azure Blob 存储来部署用于运行可缩放的应用程序的 VM
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 49689585b3f52bbee300ff68d2c2653f5e7ff8d0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699061"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>为可缩放的应用程序创建虚拟机和存储帐户

本教程是一个系列中的第一部分。 本教程演示如何部署使用 Azure 存储帐户上传和下载大量随机数据的应用程序。 完成后，会有一个控制台应用程序在虚拟机上运行，用于将大量数据上传和下载到存储帐户。

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建存储帐户
> * 创建虚拟机
> * 配置自定义脚本扩展

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 Az 版本 0.7 或更高版本。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>创建存储帐户
 
此示例将 50 个大文件上传到 Azure 存储帐户中的 blob 容器。 存储帐户提供唯一的命名空间来存储和访问 Azure 存储数据对象。 使用 [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount) 命令在创建的资源组中创建存储帐户。

在以下命令中，请将 `<blob_storage_account>` 占位符替换成自己的 Blob 存储帐户的全局唯一名称。

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

创建虚拟机配置。 此配置包括部署虚拟机时使用的设置，例如虚拟机映像、大小和身份验证配置。 运行此步骤时，会提示输入凭据。 输入的值将配置为用于虚拟机的用户名和密码。

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 创建虚拟机。

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>部署配置

在本教程中，必须在虚拟机上安装一些必备组件。 自定义脚本扩展用于运行完成以下任务的 PowerShell 脚本：

> [!div class="checklist"]
> * 安装 .NET Core 2.0
> * 安装 chocolatey
> * 安装 GIT
> * 克隆示例存储库
> * 还原 NuGet 包
> * 使用随机数据创建 50 个 1 GB 的文件

运行以下 cmdlet，完成虚拟机的配置。 完成此步骤需要 5-15 分钟。

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>后续步骤

本系列的第一部分中介绍了如何创建存储帐户、部署虚拟机以及如何为虚拟机配置所需的必备组件，例如如何：

> [!div class="checklist"]
> * 创建存储帐户
> * 创建虚拟机
> * 配置自定义脚本扩展

进入本系列的第二部分，使用指数重试和并行方式将大量数据上传到存储帐户。

> [!div class="nextstepaction"]
> [将大量大文件以并行方式上传到存储帐户](storage-blob-scalable-app-upload-files.md)
