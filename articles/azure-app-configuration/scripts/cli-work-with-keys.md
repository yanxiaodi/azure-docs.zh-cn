---
title: Azure CLI 脚本示例 - 处理 Azure 应用配置存储区中的密钥值 | Microsoft Docs
description: 提供有关如何处理 Azure 应用配置存储区中的密钥值的信息
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9288ea08da6335dd29e7a15a9bc871b76c1ce7e9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438425"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>处理 Azure 应用配置存储区中的密钥值

此示例脚本在 Azure 应用配置存储区中创建新密钥值，列出所有现有密钥值，更新新创建的密钥的值，最后删除它。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

需要先安装 Azure 应用配置 CLI 扩展，方法是通过执行以下命令：

        az extension add -n appconfig

## <a name="sample-script"></a>示例脚本

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令对应用配置存储区中的密钥值执行操作。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az appconfig kv set](/cli/azure/ext/appconfig/appconfig) | 创建或更新密钥值。 |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig) | 列出应用配置存储区中的密钥值。 |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig) | 删除密钥值。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可在 [Azure 应用配置文档](../cli-samples.md)中查找其他应用配置 CLI 脚本示例。
