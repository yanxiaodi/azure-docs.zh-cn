---
title: Azure CLI 脚本示例 - 导入 Azure 应用配置存储区 | Microsoft Docs
description: 提供用于导入 Azure 应用配置存储区的信息和示例脚本
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
ms.openlocfilehash: 0df8e19d3c6f0680f1eb1b0157c3bee5c9841e4c
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57575080"
---
# <a name="import-to-an-azure-app-configuration-store"></a>导入 Azure 应用配置存储区

此示例脚本将密钥值导入 Azure 应用配置存储区。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

需要先安装 Azure 应用配置 CLI 扩展，方法是通过执行以下命令：

        az extension add -n appconfig

## <a name="sample-script"></a>示例脚本

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --file ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令导入应用配置存储区。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az appconfig import](/cli/azure/ext/appconfig/appconfig) | 导入应用配置存储区资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可在 [Azure 应用配置文档](../cli-samples.md)中查找其他应用配置 CLI 脚本示例。
