---
title: 自动修补 SQL Server VM（经典）| Microsoft Docs
description: 介绍在 Azure 中运行且使用经典部署模式的 SQL Server 虚拟机的自动修补功能。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 737b2f65-08b9-4f54-b867-e987730265a8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9fabccd477883750c1aecb5493fdb64ddf5ab2c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100298"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>在 Azure 虚拟机（经典）中对 SQL Server 进行自动修补
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-patching.md)
> * [经典](../classic/sql-automated-patching.md)
> 
> 

自动修补为运行 SQL Server 的 Azure 虚拟机建立一个维护时段。 只能在此维护时段内安装自动更新。 对于 SQL Server，这可以确保在数据库的最佳可能时间发生系统更新和任何关联的重新启动。 

> [!IMPORTANT]
> 仅安装标记为“重要”的 Windows 更新。 必须手动安装其他 SQL Server 更新，如累积更新。 

自动修补依赖于 [SQL Server IaaS 代理扩展](../classic/sql-server-agent-extension.md)。

> [!IMPORTANT] 
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 若要查看本文的 Resource Manager 版本，请参阅[在 Azure 虚拟机 Resource Manager 中自动修补 SQL Server](../sql/virtual-machines-windows-sql-automated-patching.md)。

## <a name="prerequisites"></a>先决条件
若要使用自动修补，请考虑以下先决条件：

**操作系统**：

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server 版本**：

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**：

* [安装最新的 Azure PowerShell 命令](/powershell/azure/overview)。

**SQL Server IaaS 扩展**：

* [安装 SQL Server IaaS 扩展](../classic/sql-server-agent-extension.md)。

## <a name="settings"></a>设置
下表描述了可为自动修补配置的选项。 对于经典 VM，必须使用 PowerShell 配置以下设置。

| 设置 | 可能的值 | 描述 |
| --- | --- | --- |
| **自动修补** |启用/禁用（已禁用） |为 Azure 虚拟机启用或禁用自动修补。 |
| **维护计划** |每天、星期一、星期二、星期三、星期四、星期五、星期六、星期日 |为虚拟机下载和安装 Windows、SQL Server 和 Microsoft 更新的计划。 |
| **维护开始时间** |0-24 |更新虚拟机的本地开始时间。 |
| **维护时段持续时间** |30-180 |允许完成更新下载和安装的分钟数。 |
| **修补程序类别** |重要说明 |要下载并安装的更新类别。 |

## <a name="configuration-with-powershell"></a>使用 PowerShell 进行配置
以下示例使用 PowerShell 在现有的 SQL Server VM 上配置自动修补。 **New-AzureVMSqlServerAutoPatchingConfig** 命令可为自动更新配置新的维护时段。

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

下表根据此示例描述了对目标 Azure VM 产生的实际效果：

| 参数 | 效果 |
| --- | --- |
| **DayOfWeek** |每个星期四安装修补程序。 |
| **MaintenanceWindowStartingHour** |在上午 11:00 开始更新。 |
| **MaintenanceWindowDuration** |必须在 120 分钟内安装修补程序。 根据开始时间，修补必须在下午 1:00 之前完成。 |
| **PatchCategory** |此参数的唯一可能设置为“Important”。 |

可能需要花费几分钟来安装和配置 SQL Server IaaS 代理。

若要禁用自动修补，请对 New-AzureVMSqlServerAutoPatchingConfig 运行不带 -Enable 参数的同一个脚本。 与安装一样，可能需要花费几分钟时间来禁用自动修补。

## <a name="next-steps"></a>后续步骤
有关其他可用自动化任务的信息，请参阅 [SQL Server IaaS 代理扩展](../classic/sql-server-agent-extension.md)。

有关在 Azure VM 中运行 SQL Server 的详细信息，请参阅 [Azure 虚拟机中的 SQL Server 概述](../sql/virtual-machines-windows-sql-server-iaas-overview.md)。

