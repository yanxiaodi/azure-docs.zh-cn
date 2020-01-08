---
title: 对 SQL Server 虚拟机（经典）进行自动备份 | Microsoft Docs
description: '介绍在使用 Resource Manager 的 Azure 虚拟机中运行的 SQL Server 的自动备份功能。 '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: da40b635b0fc094275d8d46b8c5ad6d3d90bea24
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101816"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>在 Azure 虚拟机（经典）中对 SQL Server 进行自动备份
> [!div class="op_single_selector"]
> * [资源管理器](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [经典](../classic/sql-automated-backup.md)
> 
> 

自动备份会在运行 SQL Server 2014 Standard 或 Enterprise 的 Azure VM 上，自动为所有现有数据库和新数据库配置[向 Microsoft Azure 的托管备份](https://msdn.microsoft.com/library/dn449496.aspx)。 这样，便可以配置使用持久 Azure Blob 存储的定期数据库备份。 自动备份依赖于 [SQL Server IaaS 代理扩展](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

> [!IMPORTANT] 
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 若要查看本文中的 Resource Manager 版本，请参阅[在 Azure 虚拟机 Resource Manager 中对 SQL Server 进行自动备份](../sql/virtual-machines-windows-sql-automated-backup.md)。

## <a name="prerequisites"></a>先决条件
若要使用自动备份，请考虑以下先决条件：

**操作系统**：

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server 版本**：

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> 资源管理器虚拟机支持 SQL Server 2016 的自动备份。 有关详细信息，请参阅 [SQL Server 2016 Azure 虚拟机的自动备份 v2（资源管理器）](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)。

**数据库配置**：

* 目标数据库必须使用完整恢复模式。

**Azure PowerShell**：

* [安装最新的 Azure PowerShell 命令](/powershell/azure/overview)。

**SQL Server IaaS 扩展**：

* [安装 SQL Server IaaS 扩展](../classic/sql-server-agent-extension.md)。

## <a name="settings"></a>设置
下表描述了可为自动备份配置的选项。 对于经典 VM，必须使用 PowerShell 配置以下设置。

| 设置 | 范围（默认值） | 描述 |
| --- | --- | --- |
| **自动备份** |启用/禁用（已禁用） |为运行 SQL Server 2014 Standard 或 Enterprise 的 Azure VM 启用或禁用自动备份。 |
| **保留期** |1-30 天（30 天） |保留备份的天数。 |
| **存储帐户** |Azure 存储帐户（为指定的 VM 创建的存储帐户） |用于在 Blob 存储中存储自动备份文件的 Azure 存储帐户。 在此位置创建容器，用于存储所有备份文件。 备份文件命名约定包括日期、时间和计算机名称。 |
| **加密** |启用/禁用（已禁用） |启用或禁用加密。 启用加密时，用于还原备份的证书使用相同的命名约定存放在同一 automaticbackup 容器中的指定存储帐户内。 如果密码发生更改，将使用该密码生成新证书，但旧证书在备份之前仍会还原。 |
| **密码** |密码文本（无） |加密密钥的密码。 仅当启用了加密时才需要此设置。 若要还原加密的备份，必须具有创建该备份时使用的正确密码和相关证书。 |
| **备份系统数据库** | 启用/禁用（已禁用） | 完整备份 Master、Model 和 MSDB |
| **配置备份计划** | 手动/自动（自动） | 选择“自动”可根据日志增长自动进行完整备份和日志备份。 选择“手动”可指定完整备份和日志备份的计划。 |

## <a name="configuration-with-powershell"></a>使用 PowerShell 进行配置
在下面的 PowerShell 示例中，为现有 SQL Server 2014 VM 配置了自动备份。 **New-AzureVMSqlServerAutoBackupConfig** 命令会自动备份设置配置为在 $storageaccount 变量指定的 Azure 存储帐户中存储备份。 这些备份将保留 10 天。 **Set-AzureVMSqlServerExtension** 命令使用这些设置更新指定的 Azure VM。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

可能需要花费几分钟来安装和配置 SQL Server IaaS 代理。

若要启用加密，请修改上述脚本，使其将 EnableEncryption 参数连同 CertificatePassword 参数的密码（安全字符串）一起传递。 以下脚本启用上一示例中的自动备份设置，并添加加密。

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

若要禁用自动备份，请对 **New-AzureVMSqlServerAutoBackupConfig** 运行不带 **-Enable** 参数的同一个脚本。 与安装一样，可能需要花费几分钟时间来禁用自动备份。

> [!NOTE]
> 禁用和卸载 SQL Server IaaS 代理不会删除以前配置的托管备份设置。 应该在禁用或卸载 SQL Server IaaS 代理之前禁用自动备份。
> 
> 

## <a name="next-steps"></a>后续步骤
自动备份会在 Azure VM 上配置托管备份。 因此，请务必[查看有关托管备份的文档](https://msdn.microsoft.com/library/dn449496.aspx)，了解其行为和影响。

可以在以下主题中找到针对 Azure VM 上的 SQL Server 的其他备份和还原指导：[Azure 虚拟机中 SQL Server 的备份和还原](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)。

有关其他可用自动化任务的信息，请参阅 [SQL Server IaaS 代理扩展](../classic/sql-server-agent-extension.md)。

有关在 Azure VM 中运行 SQL Server 的详细信息，请参阅 [Azure 虚拟机中的 SQL Server 概述](../sql/virtual-machines-windows-sql-server-iaas-overview.md)。

