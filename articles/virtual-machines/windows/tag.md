---
title: 如何在 Azure 中标记 Windows VM 资源 | Microsoft Docs
description: 了解如何标记使用 Resource Manager 部署模型在 Azure 中创建的 Windows 虚拟机。
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 8270d17d998b27a067eb91a517a7c5fdfd23becd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101849"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>如何在 Azure 中标记 Windows 虚拟机
本文介绍在 Azure 中通过 Resource Manager 部署模型标记 Windows 虚拟机的不同方式。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 15 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。 请注意，只有通过 Resource Manager 部署模型创建的资源支持标记。 如果想要标记 Linux 虚拟机，请参阅[如何在 Azure 中标记 Linux 虚拟机](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>使用 PowerShell 进行标记
若要通过 PowerShell 创建、添加和删除标记，首先需要 [使用 Azure Resource Manager 设置 PowerShell 环境][PowerShell environment with Azure Resource Manager]。 一旦完成安装后，可以在创建时或创建资源之后通过 PowerShell 将标记放置在计算、网络和存储资源中。 本文章将重点说明查看/编辑虚拟机上放置的标记。

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

首先，通过 `Get-AzVM`cmdlet 导航到虚拟机。

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

如果虚拟机已包含标记，那么会在资源中看到所有标记：

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

如果想要通过 PowerShell 添加标记，则可以使用 `Set-AzResource` 命令。 请注意，通过 PowerShell 更新时，标记会作为整体进行更新。 因此，如果要向已具有标记的资源添加标记，则需要包括想要在资源中放置的所有标记。 下面是如何通过 PowerShell Cmdlet 将其他标记添加到资源的示例。

第一个 cmdlet 使用 `Get-AzResource` 和 `Tags` 属性将 *MyTestVM* 中放置的所有标记放置到 *$tags* 变量中。

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

第二个命令显示给定变量的标记。

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

第三个命令将其他标记添加到 *$tags* 变量。 请注意，使用 **+=** 将新的键/值对追加到 *$tags* 列表。

        PS C:\> $tags += @{Location="MyLocation"}

第四个命令将 *$tags* 变量中定义的标记放置到给定资源中。 在本示例中，该资源为 MyTestVM。

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

第五个命令显示资源上的所有标记。 可以看到，*Location* 现在定义为值为 *MyLocation* 的标记。

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

若要了解有关通过 PowerShell 标记的详细信息，请查看 [Azure 资源 Cmdlet][Azure Resource Cmdlets]。

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述][Azure Resource Manager Overview]和 [使用标记来组织 Azure 资源][Using Tags to organize your Azure Resources]。
* 要了解标记如何帮助你管理 Azure 资源的使用，请参阅 [Understanding your Azure Bill][Understanding your Azure Bill]（了解 Azure 帐单）和 [Gain insights into your Microsoft Azure resource consumption][Gain insights into your Microsoft Azure resource consumption]（深入了解 Microsoft Azure 资源消耗）。

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
