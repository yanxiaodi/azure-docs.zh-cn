---
title: Azure CLI 脚本 - 更改服务器配置
description: 此示例 CLI 脚本列出所有可用的服务器配置选项，并更新某个选项的值。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 6b5a855c8db5cb87f313e14c42396ae70b407e61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "66122085"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>使用 Azure CLI 列出和更新 Azure Database for PostgreSQL 服务器的配置
此示例 CLI 脚本列出所有 Azure Database for PostgreSQL 服务器的可用配置参数及其允许的值，并将 log_retention_days  设置为默认值以外的值。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

如果选择在本地运行 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 通过运行 `az --version` 来查看版本。 请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)，了解如何安装或升级 Azure CLI 的版本。 

## <a name="sample-script"></a>示例脚本
在此示例脚本中，编辑突出显示的行，将管理员用户名和密码更新为你自己的。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，请使用以下命令删除资源组以及与其关联的所有资源。 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/change-server-configurations/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>脚本说明
此脚本使用下表中列出的命令：

| **命令** | **说明** |
|---|---|
| [az group create](/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az postgres server create](/cli/azure/postgres/server) | 创建托管数据库的 PostgreSQL 服务器。 |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration) | 列出 Azure Database for PostgreSQL 服务器的配置。 |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration) | 更新 Azure Database for PostgreSQL 服务器的配置。 |
| [az postgres server configuration show](/cli/azure/postgres/server/configuration) | 显示 Azure Database for PostgreSQL 服务器的配置。 |
| [az group delete](/cli/azure/group) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤
- 阅读有关 Azure CLI 的更多信息：[Azure CLI 文档](/cli/azure)。
- 请尝试其他脚本：[用于 PostgreSQL 的 Azure 数据库的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 有关服务器参数的详细信息，请参阅[如何在 Azure 门户中配置服务器参数](../howto-configure-server-parameters-using-portal.md)。
