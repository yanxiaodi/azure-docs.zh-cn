---
title: Azure CLI 脚本 - 更改服务器配置
description: 此示例 CLI 脚本列出了所有可用服务器配置，并更新了 innodb_lock_wait_timeout 的值。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: cda5f3b9d2918a70e1572e331e0f11845187891a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538559"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>使用 Azure CLI 列出和更新用于 MySQL 服务器的 Azure 数据库的配置
此示例 CLI 脚本列出了 Azure 数据库所有适用于 MySQL 服务器的可用配置参数及其允许的值，并将 innodb_lock_wait_timeout  设置为默认值以外的值。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果选择在本地运行 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 通过运行 `az --version` 来查看版本。 请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)，了解如何安装或升级 Azure CLI 的版本。 

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，将管理员用户名和密码更新为你自己的。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | 创建用于托管数据库的 MySQL 服务器。 |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) | 列出用于 MySQL 服务器的 Azure 数据库的配置。 |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) | 更新用于 MySQL 服务器的 Azure 数据库的配置。 |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) | 显示用于 MySQL 服务器的 Azure 数据库的配置。 |
| [az group delete](/cli/azure/group#az-group-delete) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 阅读有关 Azure CLI 的更多信息：[Azure CLI 文档](/cli/azure)。
- 请尝试其他脚本：[用于 MySQL 的 Azure 数据库的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 有关服务器参数的详细信息，请参阅[如何在 Azure 数据库中配置适用于 MySQL 的服务器参数](../howto-server-parameters.md)。
