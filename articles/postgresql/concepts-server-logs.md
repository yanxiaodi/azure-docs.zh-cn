---
title: Azure Database for PostgreSQL - 单一服务器中的服务器日志
description: 本文介绍了 Azure Database for PostgreSQL - 单一服务器如何生成查询和错误日志，以及配置多长时间的保留期。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: b295ab442e70772a86d6699e1063c7a1c728f1a7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091123"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 单一服务器中的服务器日志
Azure Database for PostgreSQL 生成查询和错误日志。 查询和错误日志可用于识别、排除和修复配置错误和性能不佳问题。 （不包括访问事务日志）。 

## <a name="configure-logging"></a>配置日志记录 
可以使用日志记录服务器参数在服务器上配置日志记录。 在每台新服务器上，**log_checkpoints** 和 **log_connections** 默认开启。 还有一些其他参数，你可以调整它们来满足你的日志记录需求： 

![Azure Database for PostgreSQL - 日志记录参数](./media/concepts-server-logs/log-parameters.png)

有关这些参数的详细信息，请参阅 PostgreSQL 的[错误报告和日志记录](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)文档。 若要了解如何配置 Azure Database for PostgreSQL 参数，请参阅[门户文档](howto-configure-server-parameters-using-portal.md)或 [CLI 文档](howto-configure-server-parameters-using-cli.md)。

## <a name="access-server-logs-through-portal-or-cli"></a>通过门户或 CLI 访问服务器日志
如果已启用了日志，则可以使用 [Azure 门户](howto-configure-server-logs-in-portal.md)、[Azure CLI](howto-configure-server-logs-using-cli.md) 和 Azure REST API 从 Azure Database for PostgreSQL 日志存储访问它们。 每 1 小时或达到 100MB 大小时日志文件会轮换一次，以先达到的限制为准。 可以使用与服务器关联的  **log\_retention\_period**  参数设置此日志存储的保留期。 默认值为 3 天；最大值为 7 天。 必须为服务器分配足够的存储空间来存放日志文件。 （此保留期参数不控制 Azure 诊断日志）。


## <a name="diagnostic-logs"></a>诊断日志
Azure Database for PostgreSQL 集成了 Azure Monitor 诊断日志。 在 PostgreSQL 服务器上启用日志后，可以选择将它们发送到 [Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)、事件中心或 Azure 存储。 

> [!IMPORTANT]
> 服务器日志的此诊断功能仅适用于“常规用途”和“内存优化”的[定价层](concepts-pricing-tiers.md)。

使用 Azure 门户启用诊断日志：

   1. 在门户中，在 Postgres 服务器的导航菜单中转到 "*诊断设置*"。
   2. 选择 "*添加诊断设置*"。
   3. 为此设置命名。 
   4. 选择首选的下游位置（存储帐户、事件中心、log analytics）。 
   5. 选择所需的数据类型。
   6. 保存设置。

下表描述了每个日志中的内容。 包括的字段以及它们的出现顺序可能有所不同，具体取决于你选择的输出终结点。 

|**字段** | **说明** |
|---|---|
| TenantId | 租户 ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | 记录日志时的时间戳 (UTC) |
| Type | 日志的类型。 始终是 `AzureDiagnostics` |
| SubscriptionId | 服务器所属的订阅的 GUID |
| ResourceGroup | 服务器所属的资源组的名称 |
| ResourceProvider | 资源提供程序的名称。 始终是 `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| resourceId | 资源 URI |
| Resource | 服务器的名称 |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | 日志记录级别，例如：LOG, ERROR, NOTICE |
| Message | 主要日志消息 | 
| Domain | 服务器版本，示例：postgres-10 |
| Detail | 辅助日志消息（如果适用） |
| ColumnName | 列名称（如果适用） |
| SchemaName | 架构名称（如果适用） |
| DatatypeName | 数据类型名称（如果适用） |
| LogicalServerName | 服务器的名称 | 
| _ResourceId | 资源 URI |
| 前缀 | 日志行的前缀 |



## <a name="next-steps"></a>后续步骤
- 详细了解如何通过 [Azure 门户](howto-configure-server-logs-in-portal.md)或 [Azure CLI](howto-configure-server-logs-using-cli.md) 访问日志。
- 详细了解 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。
