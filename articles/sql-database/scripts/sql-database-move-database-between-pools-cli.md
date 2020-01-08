---
title: CLI 示例 - 移动 Azure SQL 数据库 - SQL 弹性池 | Microsoft Docs
description: 在 SQL 弹性池中移动 SQL 数据库的 Azure CLI 示例脚本
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 166643231e9f98cc3b8205bf0e9a8bb949ffbc3b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569876"
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>使用 CLI 在 SQL 弹性池中移动 Azure SQL 数据库

此 Azure CLI 脚本示例创建两个弹性池，将 Azure SQL 数据库从一个 SQL 弹性池移到另一个 SQL 弹性池中，然后将数据库移出弹性池，并转为单一数据库计算大小。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

## <a name="clean-up-deployment"></a>清理部署

使用以下命令删除资源组及其相关的所有资源。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | 创建托管单一数据库和弹性池的 SQL 数据库服务器。 |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create) | 创建弹性池。 |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) | 创建单一数据库或创建弹性池中的数据库。 |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | 更新数据库属性，或者将数据库移入、移出弹性池或在弹性池之间移动。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

其他 SQL 数据库 CLI 脚本示例可以在 [Azure SQL 数据库文档](../sql-database-cli-samples.md)中找到。
