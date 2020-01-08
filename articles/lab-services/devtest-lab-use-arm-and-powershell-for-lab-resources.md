---
title: 将 Azure 资源管理器模板与 PowerShell 配合使用，自动创建或修改实验室 | Microsoft Docs
description: 了解如何将 Azure 资源管理器模板与 PowerShell 配合使用，以在开发测试实验室中自动创建或修改实验室
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: cb5a08730b47cb5df3116aa4a54554ef0ee6f260
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622451"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>使用 Azure 资源管理器模板和 PowerShell 自动创建或修改实验室

开发测试实验室提供许多 Azure 资源管理器模板和 PowerShell 脚本，有助于快速且自动创建新实验室或修改现有实验室，并部署这些资源。

本文有助于指导用户完成使用这些模板和脚本自动创建、修改和部署实验室的过程。 本文还介绍在哪里可以找到关于如何在开发测试实验室中使用 PowerShell 执行一些常见任务的详细信息。

## <a name="step-1-gather-your-templates-and-scripts"></a>步骤 1：收集模板和脚本
可以在公共 [GitHub 存储库](https://github.com/Azure/azure-devtestlab)中找到预制的 [Azure 资源管理器模板](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)和 [PowerShell 脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts)。 按原样使用它们，或对它们进行自定义以满足需求，然后将它们存储在[专用 Git 存储库](devtest-lab-add-artifact-repo.md)中。

## <a name="step-2-modify-your-azure-resource-manager-template"></a>步骤 2：修改 Azure 资源管理器模板
如果之前从未创建过模板，则可以按照[创建第一个 Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)中的步骤进行操作。

此外，[创建 Azure 资源管理器模板的最佳做法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)提供许多指导原则和建议，可帮助创建可靠且易用的 Azure 资源管理器模板。 通常情况下，将使用所提供的方法或示例之一的变体，并根据需要修改模板。

## <a name="step-3-deploy-resources-with-powershell"></a>步骤 3：通过 PowerShell 部署资源
自定义模板和脚本后，请遵循[通过 Resource Manager 模板和 Azure PowerShell 部署资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)的必需步骤操作。 本文提供关于将 Azure PowerShell 与 Azure 资源管理器模板配合使用以将资源部署到 Azure 的常规信息。


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>在开发测试实验室中可以使用 PowerShell 执行的常见任务
使用 PowerShell 可以自动执行其他许多常见任务。 文档的以下部分概述了执行这些任务所需的步骤。

* [使用 PowerShell 基于 VHD 文件创建自定义映像](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [使用 PowerShell 将 VHD 文件上传到实验室的存储帐户](devtest-lab-upload-vhd-using-powershell.md)
* [使用 PowerShell 将外部用户添加到实验室](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [使用 PowerShell 创建实验室自定义角色](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>后续步骤
* 了解如何创建存储自定义模板或脚本的[专用 Git 存储库](devtest-lab-add-artifact-repo.md)。
* 浏览 [Azure 快速入门模板库中的 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates)。
