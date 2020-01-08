---
title: 使用 pgAudit 在 Azure Database for PostgreSQL-单服务器中的审核日志记录
description: Azure Database for PostgreSQL-单服务器中的 pgAudit 审核日志的概念。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 198ab5f567652a76d209168041f305b9da4d0b43
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147173"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL-单服务器中的审核日志记录

Azure Database for PostgreSQL 单服务器中的数据库活动的审核日志记录通过 PostgreSQL 审核扩展： [pgAudit](https://www.pgaudit.org/)提供。 pgAudit 提供详细的会话和/或对象审核日志记录。

> [!NOTE]
> Azure Database for PostgreSQL 上的 pgAudit 处于预览阶段。
> 只能在常规用途和内存优化服务器上启用该扩展。

## <a name="usage-considerations"></a>使用注意事项
默认情况下，pgAudit log 语句使用 Postgres 的标准日志记录工具随常规日志语句一起发出。 在 Azure Database for PostgreSQL 中，可以通过 Azure 门户或 CLI 下载这些 .log 文件。 文件集合的最大存储空间为 1 GB，每个文件最多可达7天（默认值为3天）。 此服务是短期存储选项。

或者，你可以将所有日志配置为发出到 Azure Monitor 的诊断日志服务。 如果启用 Azure Monitor 诊断日志记录，则会根据你的选择，将日志自动发送到 Azure 存储、事件中心和/或 Azure Monitor 日志。

启用 pgAudit 会在服务器上生成大量日志记录，这会对性能和日志存储产生影响。 建议使用 Azure 诊断日志服务，该服务提供更长期的存储选项以及分析和警报功能。 建议关闭标准日志记录，以减少其他日志记录对性能的影响：

   1. 将参数`logging_collector`设置为 OFF。 
   2. 重新启动服务器以应用此更改。

若要了解如何设置到 Azure 存储、事件中心或 Azure Monitor 日志的日志记录，请访问[服务器日志一文](concepts-server-logs.md)的 "诊断日志" 部分。

## <a name="installing-pgaudit"></a>安装 pgAudit

若要安装 pgAudit，需要将其包含在服务器的共享预加载库中。 更改 Postgres 的`shared_preload_libraries`参数需要服务器重启才能生效。 您可以使用[Azure 门户](howto-configure-server-parameters-using-portal.md)、 [Azure CLI](howto-configure-server-parameters-using-cli.md)或[REST API](/rest/api/postgresql/configurations/createorupdate)更改参数。

使用 [Azure 门户](https://portal.azure.com)：

   1. 选择你的 Azure Database for PostgreSQL 服务器。
   2. 在侧栏中选择“服务器参数”。
   3. 搜索 `shared_preload_libraries` 参数。
   4. 选择**pgaudit**。
   5. 重新启动服务器以应用更改。

   6. 使用客户端（如 psql）连接到你的服务器并启用 pgAudit 扩展
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> 如果出现错误，请确认已在保存`shared_preload_libraries`后重新启动服务器。

## <a name="pgaudit-settings"></a>pgAudit 设置

pgAudit 允许配置会话或对象审核日志记录。 [会话审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging)已执行语句的详细日志。 [对象审核日志记录](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging)属于特定关系。 您可以选择设置一种或两种类型的日志记录。 

> [!NOTE]
> pgAudit 设置指定为全局，并且不能在数据库或角色级别指定。

[安装 pgAudit](#installing-pgaudit)后，可以将其参数配置为开始记录。 [PgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings)提供每个参数的定义。 首先测试参数，并确认获取的是预期的行为。

> [!NOTE]
> 如果`pgaudit.log_client`设置为 ON，则会将日志重定向到客户端进程（如 psql），而不是写入到文件。 通常应禁用此设置。

> [!NOTE]
> `pgaudit.log_level`仅当处于打开`pgaudit.log_client`状态时才启用。 此外，在 Azure 门户中，当前有一个 bug `pgaudit.log_level`：显示了一个组合框，表示可以选择多个级别。 但是，只应选择一个级别。 

> [!NOTE]
> 在 Azure Database for PostgreSQL 中`pgaudit.log` ，无法使用 pgAudit 文档`-`中所述的（减号）快捷方式设置。 应该单独指定所有必需的语句类（读取、写入等）。

### <a name="audit-log-format"></a>审核日志格式
每个审核条目都由`AUDIT:`日志行的开头指示。 [PgAudit 文档](https://github.com/pgaudit/pgaudit/blob/master/README.md#format)中详细说明了条目的其余部分的格式。

如果需要任何其他字段来满足审核要求，请使用 Postgres 参数`log_line_prefix`。 `log_line_prefix`是在每个 Postgres 日志行的开头输出的字符串。 例如，以下`log_line_prefix`设置提供时间戳、用户名、数据库名称和进程 ID：

```
t=%m u=%u db=%d pid=[%p]:
```

若要了解有关`log_line_prefix`的详细信息，请访问[PostgreSQL 文档](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX)。

### <a name="getting-started"></a>入门
若要快速入门，请`pgaudit.log`将`WRITE`设置为，并打开日志以查看输出。 


## <a name="next-steps"></a>后续步骤
- [了解如何登录 Azure Database for PostgreSQL](concepts-server-logs.md)
- 了解如何使用[Azure 门户](howto-configure-server-parameters-using-portal.md)、 [Azure CLI](howto-configure-server-parameters-using-cli.md)或[REST API](/rest/api/postgresql/configurations/createorupdate)设置参数。
