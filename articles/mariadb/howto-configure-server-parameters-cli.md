---
title: 在 Azure Database for MariaDB 中配置服务参数
description: 本文介绍了如何使用 Azure CLI 命令行实用工具在 Azure Database for MariaDB 中配置服务参数。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 4e0bf45f1c67a5e07d6ed632f6560d094b673c0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61040026"
---
# <a name="customize-server-configuration-parameters-by-using-azure-cli"></a>使用 Azure CLI 自定义服务器配置参数
可以使用 Azure CLI、Azure 命令行实用工具来列出、显示和更新 Azure Database for MariaDB 服务器的配置参数。 在服务器级别会公开引擎配置的一个子集，并可以进行修改。

## <a name="prerequisites"></a>必备组件
若要逐步执行本操作方法指南，需要：
- [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) 命令行实用工具或在浏览器中使用 Azure Cloud Shell。

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>列出 Azure Database for MariaDB 服务器的服务器配置参数
若要列出服务器中的所有可修改参数及其值，请运行 [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) 命令。

可以列出资源组 **myresourcegroup** 下服务器 **mydemoserver.mariadb.database.azure.com** 的服务器配置参数。
```azurecli-interactive
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

有关每个列出参数的定义，请参阅[服务器系统变量](https://mariadb.com/kb/en/library/server-system-variables/)上的 MariaDB 参考部分。

## <a name="show-server-configuration-parameter-details"></a>显示服务器配置参数详细信息
若要显示服务器的某个特定配置参数的详细信息，请运行 [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) 命令。

本示例显示了资源组 **myresourcegroup** 下服务器 **mydemoserver.mariadb.database.azure.com** 的服务器配置参数 **slow\_query\_log** 的详细信息。
```azurecli-interactive
az mariadb server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>修改服务器配置参数值
此外，你还可以修改某个服务器配置参数的值，这会更新 MariaDB 服务器引擎的基础配置值。 若要更新配置，请使用 [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) 命令。 

更新资源组 **myresourcegroup** 下服务器 **mydemoserver.mariadb.database.azure.com** 的服务器配置参数 **slow\_query\_log**。
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```

若要重置配置参数的值，省去可选的 `--value` 参数，服务将应用默认值。 对于上述示例，它将如下所示：
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```

此代码会将 slow\_query\_log  配置重置为默认值 OFF  。 

## <a name="working-with-the-time-zone-parameter"></a>使用时区参数

### <a name="populating-the-time-zone-tables"></a>填充时区表

可以通过从 MariaDB 命令行或 MariaDB Workbench 等工具调用 `az_load_timezone` 存储过程，填充服务器上的时区表。

> [!NOTE]
> 如果是从 MariaDB Workbench 中运行 `az_load_timezone` 命令，可能需要先使用 `SET SQL_SAFE_UPDATES=0;` 关闭安全更新模式。

```sql
CALL mysql.az_load_timezone();
```

要查看可用的时区值，请运行以下命令：

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>设置全局级时区

可以使用 [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) 命令来设置全局级时区。

以下命令将资源组 **myresourcegroup** 下的服务器 **mydemoserver.mariadb.database.azure.com** 的服务器配置参数 **time\_zone** 更新为 **US/Pacific**。

```azurecli-interactive
az mariadb server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>设置会话级时区

可以通过从 MariaDB 命令行或 MariaDB Workbench 等工具运行 `SET time_zone` 命令来设置会话级时区。 以下示例将时区设置为“美国/太平洋”  时区。  

```sql
SET time_zone = 'US/Pacific';
```

若要了解[日期和时间函数](https://mariadb.com/kb/en/library/date-time-functions/)，请参阅 MariaDB 文档。

## <a name="next-steps"></a>后续步骤

- 如何配置 [Azure 门户中的服务器参数](howto-server-parameters.md)
