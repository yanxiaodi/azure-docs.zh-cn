---
title: 使用门户创建适用于 Linux 的共享 Azure 虚拟机映像 |Microsoft Docs
description: 了解如何使用 Azure 门户来创建和共享虚拟机映像。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 8be4890f01ae2c0d893bb7c45f29c6f8178844f9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082114"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>使用 Azure 门户创建共享映像库

[共享映像库](shared-image-galleries.md)大大简化了整个组织中的自定义映像共享。 自定义映像类似于市场映像，不同的是自定义映像的创建者是自己。 自定义映像可用于启动部署任务，例如预加载应用程序、应用程序配置和其他 OS 配置。 

使用共享映像库，你可以在 AAD 租户内在同一区域或跨区域与组织中的其他用户共享自定义 VM 映像。 选择要共享哪些映像，要在哪些区域中共享，以及希望与谁共享它们。 你可以创建多个库，以便可以按逻辑方式对共享映像进行分组。 

库是顶级资源，它提供完全基于角色的访问控制 (RBAC)。 你可以控制映像的版本，并且可以选择将每个映像版本复制到一组不同的 Azure 区域。 库仅适用于托管映像。

共享映像库功能具有多种资源类型。 我们将在本文中使用或生成这些资源类型：

| Resource | 描述|
|----------|------------|
| **托管映像** | 这是基本映像，可以单独使用，也可用于在映像库中创建“映像版本”。 托管映像是从通用 VM 创建的。 托管映像是一种特殊的 VHD 类型，可用于生成多个 VM，并且现在可用于创建共享映像版本。 |
| **映像库** | 与 Azure 市场一样，**映像库**是用于管理和共享映像的存储库，但你可以控制谁有权访问这些映像。 |
| **映像定义** | 映像在库中定义，携带有关该映像及其在内部使用的要求的信息。 这包括了该映像是 Windows 还是 Linux 映像、发行说明以及最低和最高内存要求。 它是某种映像类型的定义。 |
| **映像版本** | 使用库时，将使用**映像版本**来创建 VM。 可根据环境的需要创建多个映像版本。 与托管映像一样，在使用**映像版本**创建 VM 时，将使用映像版本来创建 VM 的新磁盘。 可以多次使用映像版本。 |


## <a name="before-you-begin"></a>开始之前

完成本文中的示例，必须具有现有托管映像。 可以按照[教程：使用 Azure PowerShell 创建 Azure VM 的自定义映像](tutorial-custom-images.md)来创建映像（如有需要）。 如果托管映像包含数据磁盘，则数据磁盘大小不能超过 1 TB。

通过本文进行操作时，请根据需要替换资源组和 VM 名称。

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>根据映像创建 VM

映像版本完成后，可以创建一个或多个新的 VM。 

> [!IMPORTANT]
> 不能使用门户从另一个 Azure 租户中的映像部署 VM。 若要从租户之间共享的映像创建 VM, 必须使用[Azure CLI](shared-images.md#create-a-vm)或[Powershell](../windows/shared-images.md#create-vms-from-an-image)。


本示例在美国东部数据中心的“myResourceGroup”中创建名为“myVMfromImage”的 VM。

1. 在映像版本的页面上, 从页面顶部的菜单中选择 "**创建 VM** "。
1. 对于 "**资源组**", 选择 "**新建**", 然后键入*myResourceGroup*作为名称。
1. 在 "**虚拟机名称**" 中, 键入*myVM*。
1. 对于 "**区域**", 选择 "*美国东部*"。
1. 对于**可用性选项**, 保留默认值 "*无需基础结构冗余*"。
1. 如果从图像版本的页面开始, 则应自动填充**图像**的值。
1. 对于 "**大小**", 请从可用大小列表中选择 VM 大小, 然后单击 "选择"。
1. 在 "**管理员帐户**" 下, 选择 "**密码**" 或 " **SSH 公钥**", 然后输入信息。
1. 如果要允许远程访问 VM, 请在 "**公用入站端口**" 下, 选择 "**允许所选端口**", 然后从下拉选项中选择**SSH (22)** 。 如果你不希望允许远程访问 VM, 请为**公用入站端口**保留 "**无**"。
1. 完成后, 请选择页面底部的 "查看" 和 "**创建**" 按钮。
1. VM 通过验证后, 在页面底部选择 "**创建**" 以开始部署。


## <a name="clean-up-resources"></a>清理资源

当不再需要时，可以删除资源组、虚拟机和所有相关资源。 为此，请选择虚拟机的资源组，选择“删除”，然后确认要删除的资源组的名称。

如果要删除单个资源, 需要按相反的顺序删除它们。 例如, 若要删除映像定义, 需要删除所有从该映像创建的映像版本。

## <a name="next-steps"></a>后续步骤

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [根据映像版本创建 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

有关共享映像库的详细信息，请参阅[概述](shared-image-galleries.md)。 如果遇到问题，请参阅[排查共享映像库问题](troubleshooting-shared-images.md)。

