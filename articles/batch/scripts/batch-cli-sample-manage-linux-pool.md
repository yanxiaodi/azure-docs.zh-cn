---
title: Azure CLI 脚本示例 - Batch 中的 Linux 池 | Microsoft Docs
description: Azure CLI 脚本示例 - 在 Batch 中创建和管理 Linux 池
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: lahugh
ms.openlocfilehash: a0e78ec35ff001b6a4ee59794a002cb3a74e90cb
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698497"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI 示例：在 Azure Batch 中创建和管理 Linux 池

这些脚本演示了 Azure Batch 中一些可用于在 Azure CLI 中创建和管理 Linux 计算节点池的命令。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.20 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>清理部署

运行以下命令以删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | 创建批处理帐户。 |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | 针对指定的批处理帐户进行身份验证，以便进一步进行 CLI 交互。  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | 列出可用节点代理 SKU 和映像信息。  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | 创建计算节点池。  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-resize) | 调整指定池中正在运行的 VM 数目。  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-show) | 显示池的属性。  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-list) | 列出指定池中的所有计算节点。  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-reboot) | 重新启动指定的计算节点。  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-delete) | 从指定的池中删除列出的节点。  |
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。
