---
title: 查看和使用虚拟机的 Azure 资源管理器模板 | Microsoft Docs
description: 了解如何使用虚拟机的 Azure 资源管理器模板创建其他 VM
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: c14abf3acce0084507a03f3d34fdd59566d88c28
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854278"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建虚拟机 

如果通过 [Azure 门户](https://go.microsoft.com/fwlink/p/?LinkID=525040)在开发测试实验室中创建虚拟机 (VM)，则在保存 VM 前可以查看 Azure 资源管理器模板。 然后可以该模板为基准创建更多具有相同设置的实验室 VM。

本文介绍多 VM 和单 VM 的资源管理器模板比较，并演示在创建 VM 时如何查看和保存模板。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>多 VM 和单 VM 的 Resource Manager 模板比较
可以通过两种方法使用资源管理器模板在开发测试实验室中创建 Vm: 预配 Microsoft.devtestlab/Labs/virtualmachines 资源或设置/virtualmachines 资源。 这两种资源适用于不同的方案，并且需要不同的权限。

- 使用 Microsoft.DevTestLab/labs/virtualmachines 资源类型（在模板中的“资源”属性中声明）的 Resource Manager 模板可以预配单个实验室 VM。 在开发测试实验室虚拟机列表中，每个 VM 显示为单个项：

   ![在开发测试实验室虚拟机列表中将 VM 列为单个项](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   此类型的资源管理器模板可以通过 Azure PowerShell **AzResourceGroupDeployment**命令进行预配, 也可以通过 Azure CLI 命令**az group deployment create**进行设置。 此操作需要管理员权限，因此已分配开发测试实验室用户角色的用户无法执行该部署。 

- 使用 Microsoft.Compute / virtualmachines 资源类型的 Resource Manager 模板可以将多个 VM 预配为开发测试实验室虚拟机列表中的单个环境：

   ![在开发测试实验室虚拟机列表中将 VM 列为单个项](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   相同环境中的 VM 可以一起管理和共享相同的生命周期。 只要管理员以这种方式配置了实验室，已分配开发测试实验室用户角色的用户就可以使用这些模板创建环境。

本文的其余部分将讨论使用 Microsoft.DevTestLab/labs/virtualmachines 的资源管理器模板。 实验室管理员可使用这些模板自动创建实验室 VM（如可认领 VM）或自动生成黄金映像（如映像工厂）。

[创建 Azure 资源管理器模板的最佳做法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)提供许多指导原则和建议，可帮助创建可靠且易用的 Azure 资源管理器模板。

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>查看和保存虚拟机的 Resource Manager 模板
1. 请遵循[在实验室中创建你的第一个 VM](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)中的步骤，开始创建虚拟机。
1. 输入虚拟机所需的信息并添加要用于此 VM 的任何项目。
1. 开关到 "**高级设置**" 选项卡。 
1. 在配置设置窗口的底部，选择“查看 ARM 模板”  。
1. 复制并保存 Resource Manager 模板，以便稍后用于创建另一个虚拟机。

   ![保存以供将来使用的资源管理器模板](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

保存 Resource Manager 模板后，必须先更新模板的参数部分，然后再使用该模板。 可以在实际 Resource Manager 模板之外创建自定义参数的 parameter.json。 

![使用 JSON 文件自定义参数](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

资源管理器模板现在已准备好用来[创建 VM](devtest-lab-create-environment-from-arm.md)。

## <a name="set-expiration-date"></a>设置到期日期
在定型、演示和试验等方案中, 您可能需要创建虚拟机并在固定的持续时间后自动将其删除, 以免产生不必要的成本。 可以通过为 VM 指定**expirationDate**属性, 来创建具有到期日期的实验室 VM。 查看[GitHub 存储库](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration)中的相同资源管理器模板。



### <a name="next-steps"></a>后续步骤
* 了解如何[使用 Resource Manager 模板创建多 VM 环境](devtest-lab-create-environment-from-arm.md)。
* [部署资源管理器模板以创建 VM](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* 若要深入了解适用于开发测试实验室自动化的资源管理器模板快速入门，请参阅[公共开发测试实验室 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)。
