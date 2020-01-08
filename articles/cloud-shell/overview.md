---
title: Azure Cloud Shell 概述 | Microsoft Docs
description: Azure Cloud Shell 的概述。
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: acdf52022d8498a186ed4cff90284cd6e299d6c0
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240847"
---
# <a name="overview-of-azure-cloud-shell"></a>Azure Cloud Shell 的概述
Azure Cloud Shell 是一种交互式的经过身份验证的、经过身份验证的浏览器访问，用于管理 Azure 资源。
它可以灵活地选择最适合自己工作方式的 shell 体验（Bash 或 PowerShell）。

单击以下图标，前往 shell.azure.com 试用。

[![嵌入启动](https://shell.azure.com/images/launchcloudshell.png "启动 Azure Cloud Shell")](https://shell.azure.com)

尝试从 Azure 门户使用 Cloud Shell 图标。

![门户启动](media/overview/portal-launch-icon.png)

## <a name="features"></a>功能

### <a name="browser-based-shell-experience"></a>基于浏览器的 shell 体验
Cloud Shell 能够访问以执行 Azure 管理任务为宗旨构建的基于浏览器的命令行体验。
利用 Cloud Shell 可以不受限制地以只有云才能提供的方式从本地计算机工作。

### <a name="choice-of-preferred-shell-experience"></a>选择偏好的 shell 体验
用户可以从 shell 下拉列表中选择 Bash 或 PowerShell。

![Cloud Shell 中的 Bash](media/overview/overview-bash-pic.png)

![Cloud Shell 中的 PowerShell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>经身份验证的已配置 Azure 工作站
Cloud Shell 由 Microsoft 管理，因此附带了常用的命令行工具和语言支持。 此外，Cloud Shell 还能够安全地自动执行身份验证以立即通过 Azure CLI 或 Azure PowerShell cmdlet 访问资源。

查看[安装在 Cloud Shell 中的完整工具列表。](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>集成的 Cloud Shell 编辑器
Cloud Shell 提供基于开源 Monaco Editor 的集成图形文本编辑器。 只需通过运行 `code .` 来创建和编辑配置文件，即可借助 Azure CLI 或 Azure PowerShell 进行无缝部署。

[详细了解 Cloud Shell 编辑器](using-cloud-shell-editor.md)。

### <a name="integrated-with-docsmicrosoftcom"></a>已与 docs.microsoft.com 集成

可以直接从 [docs.microsoft.com](https://docs.microsoft.com) 上托管的文档中使用 Cloud Shell。 它已集成在 [Microsoft Learn](https://docs.microsoft.com/learn/)、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 和 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)中 - 单击代码片段中的“试用”按钮即可开启沉浸式 shell 体验。 

### <a name="multiple-access-points"></a>多个访问点
Cloud Shell 是一个灵活的工具，可以通过以下项使用：
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI 文档](https://docs.microsoft.com/cli/azure)
* [Azure PowerShell 文档](https://docs.microsoft.com/powershell/azure/overview)
* [Azure 移动应用](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>连接 Microsoft Azure 文件存储
Cloud Shell 计算机是临时的，但文件通过以下两种方式持久保存：通过磁盘映像，并通过名为的已`clouddrive`装载文件共享。  首次启动时，Cloud Shell 会提示你代表你创建资源组、存储帐户和 Azure 文件共享。 这是一个一次性步骤，将来会针对所有会话自动附加。 单个文件共享可以映射，将由 Cloud Shell 中的 Bash 和 PowerShell 使用。

详细了解如何装载[新的或现有的存储帐户](persisting-shell-storage.md)，或了解[Cloud Shell 中使用的持久性机制](persisting-shell-storage.md#how-cloud-shell-storage-works)。

## <a name="concepts"></a>概念
* Cloud Shell 在按会话按用户提供的临时主机上运行
* Cloud Shell 在 20 分钟没有交互活动后将超时
* Cloud Shell 需要装载 Azure 文件共享
* Cloud Shell 对 Bash 和 PowerShell 使用相同的 Azure 文件共享
* 将针对每个用户帐户为 Cloud Shell 分配一台计算机
* Cloud Shell 使用文件共享中保存的 5-GB 映像持久保存 $HOME
* 在 Bash 中权限是按常规 Linux 用户设置的

详细了解 [Cloud Shell 中的 Bash](features.md) 和 [Cloud Shell 中的 PowerShell](features-powershell.md) 的功能。

## <a name="pricing"></a>定价
托管 Cloud Shell 的计算机是免费的，先决条件是具有已装载的 Azure 文件共享。 将收取常规存储费用。

## <a name="next-steps"></a>后续步骤
[Bash in Cloud Shell 快速入门](quickstart.md) <br>
[Cloud Shell 中的 PowerShell 快速入门](quickstart-powershell.md)
