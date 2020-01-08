---
title: 在 Azure PowerShell 中创建 SQL Server 虚拟机（经典）| Microsoft Docs
description: 提供用于创建具有 SQL Server 虚拟机库映像的 Azure VM的步骤和 PowerShell 脚本。 本主题使用经典部署模式。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a4c7c29736cdd80ef7ebe413a377aba630d61858
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101871"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>使用 Azure PowerShell 预配 SQL Server 虚拟机（经典）

本文提供了有关如何通过使用 PowerShell cmdlet 在 Azure 中创建 SQL Server 虚拟机的步骤。

> [!IMPORTANT] 
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

有关此主题中的 Resource Manager 版本，请参阅[使用 Azure PowerShell Resource Manager 预配 SQL Server 虚拟机](../sql/virtual-machines-windows-ps-sql-create.md)。

### <a name="install-and-configure-powershell"></a>安装和配置 PowerShell：
1. 如果没有 Azure 帐户，请访问 [Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
2. [下载和安装最新 Azure PowerShell 命令](/powershell/azure/overview)。
3. 启动 Windows PowerShell，并通过 **Add-AzureAccount** 命令将其连接到 Azure 订阅。

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>确定目标 Azure 区域

将在位于特定 Azure 区域的云服务中托管 SQL Server 虚拟机。 以下步骤可帮助确定用于本教程其余部分的区域、存储帐户和云服务。

1. 确定用于托管 SQL Server VM 的数据中心。 以下 PowerShell 命令显示可用区域名称的列表。

   ```powershell
   (Get-AzureLocation).Name
   ```

2. 确定首选位置后，为该区域设置一个名为 **$dcLocation** 的变量。 例如，以下命令可将区域设置为“美国东部”：

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>设置订阅和存储帐户

1. 确定用于新虚拟机的 Azure 订阅。

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. 将目标 Azure 订阅分配到 **$subscr** 变量。 然后将它设置为当前的 Azure 订阅。

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. 然后，检查现有存储帐户。 以下脚本显示所选区域中存在的所有存储帐户：

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > 如果需要新的存储帐户，首先请使用 New-AzureStorageAccount 命令创建一个全部小写的存储帐户名称，如以下示例所示：`New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. 将目标存储帐户名称分配给 **$staccount**。 然后使用 Set-AzureSubscription 设置订阅和当前存储帐户。

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>选择 SQL Server 虚拟机映像

1. 从库中找出可用的 SQL Server 虚拟机映像的列表。 所有这些映像的 **ImageFamily** 属性都以“SQL”开头。 以下查询显示已预先安装 SQL Server 的可用映像系列。

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. 发现虚拟机映像系列时，该系列中可能有多个已发布的映像。 使用以下脚本查找所选映像系列发布的最新虚拟机映像名称（例如 **Windows Server 2012 R2 上的 SQL Server 2016 RTM Enterprise**）：

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>创建虚拟机

最后，使用 PowerShell 创建虚拟机：

1. 创建一个云服务托管新 VM。 请注意，还可以改用现有云服务。 使用云服务的短名称新建变量 **$svcname**。

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. 指定虚拟机名称和大小。 有关虚拟机大小的详细信息，请参阅 [Azure 的虚拟机大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. 制定本地管理员帐户和密码。

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. 运行以下脚本来创建虚拟机。

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> 有关更多说明和配置选项，请参阅[使用 Azure PowerShell 创建和预配置基于 Windows 的虚拟机](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)中的**生成你的命令集**部分。

## <a name="example-powershell-script"></a>PowerShell 脚本示例

以下脚本提供完整脚本的示例，该脚本在 Windows Server 2012 R2 虚拟机上创建 SQL Server 2016 RTM Enterprise。 如果使用此脚本，必须根据本主题中之前的步骤自定义初始变量。

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>使用远程桌面进行连接

1. 在当前用户的文档文件夹中创建 RDP 文件，以启动这些虚拟机完成安装：

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. 在文档目录中启动 RDP 文件。 使用之前提供的管理员用户名和密码进行连接（例如，如果用户名称是 VMAdmin，请指定“\VMAdmin”作为用户并提供密码）。

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>为远程访问完成 SQL Server 计算机的配置

通过远程桌面登录计算机之后，根据[在 Azure VM 中配置 SQL Server 连接的步骤](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)中的说明配置 SQL Server。

## <a name="next-steps"></a>后续步骤

可以在[虚拟机文档](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)中找到有关使用 PowerShell 预配虚拟机的其他说明。

在许多情况下，下一步是将数据库迁移到此新的 SQL Server VM。 有关数据库迁移指南，请参阅[将数据库迁移到 Azure VM 上的 SQL Server](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)。

如果还希望使用 Azure 门户创建 SQL 虚拟机，请参阅[在 Azure 上预配 SQL Server 虚拟机](../sql/virtual-machines-windows-portal-sql-server-provision.md)。 请注意，本教程演示了如何在门户中使用推荐的 Resource Manager 模型创建 VM（而非使用此 PowerShell 主题中所用的经典模型）。

除了这些资源外，我们还建议查看[与在 Azure 虚拟机中运行 SQL Server 相关的其他主题](../sql/virtual-machines-windows-sql-server-iaas-overview.md)。
