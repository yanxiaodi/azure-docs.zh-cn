---
title: Azure PowerShell 脚本示例 - 根据前缀删除容器 | Microsoft Docs
description: 根据容器名称前缀删除 Azure 存储 blob 容器。
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: e0c41ed1fc2cdb7559d36a7056ebb7b9a6ecc186
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242764"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>根据容器名称前缀删除容器

此脚本根据容器名称的前缀删除 Azure Blob 存储中的容器。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令，删除资源组、其余容器和所有相关资源。

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令根据容器名称前缀删除容器。 表中的每一项均链接到命令特定的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | 获取资源组或订阅中的指定存储帐户或所有存储帐户。 |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | 列出与存储帐户关联的存储容器。 |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | 删除指定的存储容器。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

有关其他存储 PowerShell 脚本示例，可参阅 [Azure Blob 存储的 PowerShell 示例](../blobs/storage-samples-blobs-powershell.md)。
