---
title: Azure PowerShell 脚本示例 - 更新 RDP 用户名和密码 | Microsoft Docs
description: Azure PowerShell 脚本示例 - 更新特定节点类型的所有 Service Fabric 群集节点的 RDP 用户名和密码。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 2618d9100c81cdb35b4a3a82436e94068a9f9bf0
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035529"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>更新群集中 VM 的管理员用户名和密码

Service Fabric 群集中的每个[节点类型](../service-fabric-cluster-nodetypes.md)是一个虚拟机规模集。 此示例脚本更新特定节点类型中群集虚拟机的管理员用户名和密码。  将 VMAccessAgent 扩展添加到规模集，因为管理员密码是不可修改的规模集属性。  用户名和密码更改将应用到规模集中的所有节点。 根据需要自定义参数。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令：表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | 获取群集节点类型（虚拟机规模集）的属性。   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| 将扩展添加到虚拟机规模集。|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|将虚拟机规模集的状态更新为 VMSS 对象的状态。|

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Azure Powershell 示例。
