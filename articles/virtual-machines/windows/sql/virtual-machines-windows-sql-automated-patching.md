---
title: 自动修补 SQL Server VM (Resource Manager) | Microsoft Docs
description: 介绍 Azure 中运行的、使用 Resource Manager 的 SQL Server 虚拟机的自动修补功能。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0900dd1809ecb1e93906b57483f334d0f12f6582
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102076"
---
# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Azure 虚拟机中 SQL Server 的自动修补 (Resource Manager)
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-automated-patching.md)
> * [经典](../sqlclassic/virtual-machines-windows-classic-sql-automated-patching.md)

自动修补为运行 SQL Server 的 Azure 虚拟机建立一个维护时段。 只能在此维护时段内安装自动更新。 对于 SQL Server，此限制可以确保在数据库的最佳可能时间发生系统更新和任何关联的重新启动。 

> [!IMPORTANT]
> 仅安装标记为“重要”的 Windows 更新。 必须手动安装其他 SQL Server 更新，如累积更新。 

自动修补依赖于 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。

## <a name="prerequisites"></a>先决条件
若要使用自动修补，请考虑以下先决条件：

**操作系统**：

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server 版本**：

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**：

* 如果打算使用 PowerShell 配置自动修补，请[安装最新的 Azure PowerShell 命令](/powershell/azure/overview)。

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> 自动修补依赖 SQL Server IaaS 代理扩展。 当前的 SQL 虚拟机库映像默认添加此扩展。 有关详细信息，请参阅 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。
> 
> 

## <a name="settings"></a>设置
下表描述了可为自动修补配置的选项。 实际配置步骤根据使用的是 Azure 门户还是 Azure Windows PowerShell 命令而有所不同。

| 设置 | 可能的值 | 描述 |
| --- | --- | --- |
| **自动修补** |启用/禁用（已禁用） |为 Azure 虚拟机启用或禁用自动修补。 |
| **维护计划** |每天、星期一、星期二、星期三、星期四、星期五、星期六、星期日 |为虚拟机下载和安装 Windows、SQL Server 和 Microsoft 更新的计划。 |
| **维护开始时间** |0-24 |更新虚拟机的本地开始时间。 |
| **维护时段持续时间** |30-180 |允许完成更新下载和安装的分钟数。 |
| **修补程序类别** |重要提示 | 要下载并安装的 Windows 更新类别。|

## <a name="configuration-in-the-portal"></a>门户中的配置
可以在预配期间或针对现有的 VM，使用 Azure 门户来配置自动修补。

### <a name="new-vms"></a>新的 VM
在 Resource Manager 部署模型中创建新的 SQL Server 虚拟机时，可以使用 Azure 门户配置自动修补。

在“SQL Server 设置”选项卡中，选择“自动修补”下的“更改配置”。 下面的 Azure 门户屏幕截图显示了“SQL 自动修补”边栏选项卡。

![Azure 门户中的 SQL 自动修补](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

若要了解上下文，请参阅有关[在 Azure 中预配 SQL Server 虚拟机](virtual-machines-windows-portal-sql-server-provision.md)的完整主题。

### <a name="existing-vms"></a>现有 VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

对于现有 SQL Server 虚拟机, 请打开[SQL 虚拟机资源](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource), 然后选择 "**设置**" 下的 "**修补**"。 

![现有 VM 的 SQL 自动修补](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)


完成后，单击“SQL Server 配置”边栏选项卡底部的“确定”按钮保存更改。

首次启用自动修补时，Azure 会在后台配置 SQL Server IaaS 代理。 在此期间，Azure 门户可能不会显示已配置自动修补。 请等待几分钟，以便安装和配置代理。 随后，Azure 门户将反映新设置。

## <a name="configuration-with-powershell"></a>使用 PowerShell 进行配置
预配 SQL VM 后，使用 PowerShell 配置自动修补。

以下示例使用 PowerShell 在现有的 SQL Server VM 上配置自动修补。 **New-AzVMSqlServerAutoPatchingConfig** 命令可为自动更新配置新的维护时段。

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

> [!IMPORTANT]
> 如果尚未安装该扩展，安装该扩展将会重新启动 SQL Server 服务。

下表根据此示例描述了对目标 Azure VM 产生的实际效果：

| 参数 | 效果 |
| --- | --- |
| **DayOfWeek** |每个星期四安装修补程序。 |
| **MaintenanceWindowStartingHour** |在上午 11:00 开始更新。 |
| **MaintenanceWindowsDuration** |必须在 120 分钟内安装修补程序。 根据开始时间，修补必须在下午 1:00 之前完成。 |
| **PatchCategory** |此参数的唯一可能设置为 **Important**。 这会安装标记为“重要”的 Windows 更新；不安装未包含在此类别中的任何 SQL Server 更新。 |

可能需要花费几分钟来安装和配置 SQL Server IaaS 代理。

若要禁用自动修补，请在不使用 **-Enable** 参数的情况下对 **New-AzVMSqlServerAutoPatchingConfig** 运行同一脚本。 缺少 **-Enable** 参数将向该命令发出指示以禁用此功能。

## <a name="next-steps"></a>后续步骤
有关其他可用自动化任务的信息，请参阅 [SQL Server IaaS 代理扩展](virtual-machines-windows-sql-server-agent-extension.md)。

有关在 Azure VM 中运行 SQL Server 的详细信息，请参阅 [Azure 虚拟机中的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)。

