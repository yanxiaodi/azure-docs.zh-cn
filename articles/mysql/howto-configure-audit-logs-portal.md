---
title: 配置和访问审核日志-Azure Database for MySQL
description: 本文介绍如何从 Azure 门户配置和访问 Azure Database for MySQL 中的审核日志。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: 2a2d2a697f0e41fb296c61c01909a814678f8277
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350408"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql-in-the-azure-portal"></a>在 Azure 门户中配置和访问 Azure Database for MySQL 的审核日志

可以从 Azure 门户配置 [Azure Database for MySQL 审核日志](concepts-audit-logs.md)和诊断设置。

> [!IMPORTANT]
> 审核日志功能目前为预览版。

## <a name="prerequisites"></a>先决条件

若要逐步执行本操作方法指南，需要：

- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-audit-logging"></a>配置审核日志记录

启用并配置审核日志记录。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择 Azure Database for MySQL 服务器。

1. 在侧栏的“设置”部分，选择“服务器参数”。
    ![服务器参数](./media/howto-configure-audit-logs-portal/server-parameters.png)

1. 将 **audit_log_enabled** 参数更新为 ON。
    ![启用审核日志](./media/howto-configure-audit-logs-portal/audit-log-enabled.png)

1. 通过更新 **audit_log_events** 参数，选择要记录的[事件类型](concepts-audit-logs.md#configure-audit-logging)。
    ![审核日志事件](./media/howto-configure-audit-logs-portal/audit-log-events.png)

1. 通过更新 **audit_log_exclude_users** 参数添加不进行日志记录的 MySQL 用户。 通过提供 MySQL 用户名来指定用户。
    ![审核日志排除用户](./media/howto-configure-audit-logs-portal/audit-log-exclude-users.png)

1. 更改参数之后，可以单击“保存”。 也可以放弃所做的更改。
    ![保存](./media/howto-configure-audit-logs-portal/save-parameters.png)

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

1. 在侧栏的“监视”部分，选择“诊断设置”。

1. 单击“+ 添加诊断设置”![添加诊断设置](./media/howto-configure-audit-logs-portal/add-diagnostic-setting.png)

1. 提供诊断设置名称。

1. 指定向哪些数据接收器（存储帐户、事件中心和/或 Log Analytics 工作区）发送审核日志。

1. 选择“MySqlAuditLogs”作为日志类型。
![配置诊断设置](./media/howto-configure-audit-logs-portal/configure-diagnostic-setting.png)

1. 配置可以通过管道向其传输审核日志的数据接收器以后，即可单击“保存”。
![保存诊断设置](./media/howto-configure-audit-logs-portal/save-diagnostic-setting.png)

1. 访问审核日志时，可以在配置的数据接收器中浏览它们。 可能需要等待长达 10 分钟的时间这些日志才会出现。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure Database for MySQL 中的[审核日志](concepts-audit-logs.md)。