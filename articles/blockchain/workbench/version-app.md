---
title: Azure 区块链工作台预览版中的区块链应用程序版本控制
description: 如何在 Azure 区块链工作台预览版中使用应用程序版本。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 33ecb6dea48117edd876b90eda54768785f25c20
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70843875"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Azure 区块链工作台预览版应用程序版本控制

你可以创建和使用 Azure 区块链工作台预览应用的多个版本。 如果上传了同一应用程序的多个版本，则会提供版本历史记录并且用户可以选择要使用哪个版本。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* Blockchain Workbench 部署。 有关部署的详细信息，请参阅 [Azure Blockchain Workbench 部署](deploy.md)
* Blockchain Workbench 中部署的区块链应用程序。 请参阅 [在 Azure Blockchain Workbench 中创建区块链应用程序](create-app.md)

## <a name="add-an-app-version"></a>添加应用版本

若要添加新版本，请将新的配置和智能合同文件上传到 Blockchain Workbench。

1. 在 Web 浏览器中，导航到 Blockchain Workbench 的 Web 地址。 例如 `https://{workbench URL}.azurewebsites.net/`，有关如何查找 Blockchain Workbench Web 地址的信息，请参阅 [Blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. 以 [Blockchain Workbench 管理员](manage-users.md#manage-blockchain-workbench-administrators)身份登录。
3. 选择要更新为另一版本的区块链应用程序。
4. 选择“添加版本”。 此时将显示“添加版本”窗格。
5. 选择新的版本合同配置与合同代码文件进行上传。 系统会自动验证该配置文件。 请在部署应用程序之前修复任何验证错误。
6. 选择“添加版本”来添加新的区块链应用程序版本。

    ![添加新版本](media/version-app/add-version.png)

部署区块链应用程序可能需要几分钟时间。 在部署完成后，刷新应用程序页面。 选择应用程序并选择“版本历史记录”按钮会显示应用程序的版本历史记录。

> [!IMPORTANT]
> 应用程序的以前版本处于禁用状态。 你可以单独重新启用过去的版本。
>
> 如果在新版本中对应用程序角色进行了更改，则你可能需要将成员重新添加到应用程序角色。

## <a name="using-app-versions"></a>使用应用版本

默认情况下，Blockchain Workbench 中使用应用程序的最新已启用版本。 如果希望使用应用程序的以前版本，则需要先从应用程序页面中选择版本。

1. 在 Blockchain Workbench 应用程序部分中，选中包含要使用的合同的应用程序复选框。 如果启用了以前的版本，则版本历史记录按钮可用。
2. 选择“版本历史记录”按钮。
3. 在版本历史记录窗格中，通过选择“修改日期”列中的链接来选择应用程序的版本。

    ![选择以前的版本](media/version-app/use-version.png)

    可以创建新合同，也可以对以前的版本合同采取操作。 应用程序的版本显示在应用程序名称后面，并且会显示关于较旧版本的一个警告。

## <a name="next-steps"></a>后续步骤

* [Azure Blockchain Workbench 故障排除](troubleshooting.md)
