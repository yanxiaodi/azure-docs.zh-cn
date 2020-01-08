---
title: 导出并删除数据
titleSuffix: Azure Machine Learning Studio
description: 通过 Azure 门户或经过身份验证的 REST API，可导出和删除 Azure 机器学习工作室存储的产品内数据。 可通过 Azure 隐私门户访问遥测数据。 本文介绍相关实现方法。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 827714fea9618724ef058e1f76dc099f692482bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60750088"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio"></a>从 Azure 机器学习工作室中导出和删除产品内用户数据

可以使用 Azure 门户、工作室界面、PowerShell 和经过验证的 REST API 删除或导出 Azure 机器学习工作室存储的产品内数据。 本文介绍了相关实现方法。 

可通过 Azure 隐私门户访问遥测数据。 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>工作室收集哪些类型的用户数据？

对于此服务，用户数据由获权访问工作区的用户的相关信息以及用户与此服务交互的遥测记录组成。

机器学习工作室中有两种用户数据：
- **个人帐户数据：** 与帐户关联的帐户 ID 和电子邮件地址。
- **客户数据：** 上传以供分析的数据。

## <a name="studio-account-types-and-how-data-is-stored"></a>工作室帐户类型以及数据存储方式

机器学习工作室中有三种帐户。 你拥有的帐户类型决定了如何存储数据以及如何删除或导出数据。

- **来宾工作区**是一个免费的匿名帐户。 无需提供凭据（例如电子邮件地址或密码）即可注册。
    -  来宾工作区到期后，数据将被清除。
    - 来宾用户可以通过 UI、REST API 或 PowerShell 包导出客户数据。
- **免费工作区**是一个使用 Microsoft 帐户凭据（电子邮件地址和密码）登录的免费帐户。
    - 可以导出和删除受数据主体权限 (DSR) 请求影响的个人及客户数据。
    - 可以通过 UI、REST API 或 PowerShell 包导出客户数据。
    - 对于不使用 Azure AD 帐户的免费工作区，可使用隐私门户导出遥测数据。
    - 删除工作区时会删除所有个人客户数据。
- **标准工作区**是使用登录凭据访问的付费帐户。
    - 可以导出和删除受 DSR 请求影响的个人及客户数据。
    - 可以通过 Azure 隐私门户访问数据
    - 可以通过 UI、REST API 或 PowerShell 包导出个人及客户数据
    - 可以在 Azure 门户中删除数据。

## <a name="delete"></a>删除工作室中的工作区数据 

### <a name="delete-individual-assets"></a>删除各项资产

用户可以通过选择工作区中的资产并选择删除按钮来删除这些资产。

![删除机器学习工作室中的资产](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>删除整个工作区

用户也可以删除其整个工作区：
- 付费工作区：通过 Azure 门户进行删除。
- 免费工作区：使用“设置”窗格中的“删除”按钮  。

![删除机器学习工作室中的免费工作区](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>使用 PowerShell 导出工作室数据
可通过 PowerShell 使用命令将 Azure 机器学习工作室中的所有信息导出为可移植格式。 有关信息，请参阅 [Azure 机器学习工作室 PowerShell 模块](powershell-module.md)一文。

## <a name="next-steps"></a>后续步骤

有关介绍 Web 服务和提交计划计费的文档，请参阅 [Azure 机器学习工作室 REST API 参考](https://docs.microsoft.com/rest/api/machinelearning/)。 
