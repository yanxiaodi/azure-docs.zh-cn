---
title: 在 Azure 门户中配置和访问 Azure Database for MySQL 的慢查询日志
description: 本文介绍如何从 Azure 门户配置和访问 Azure Database for MySQL 中的慢查询日志。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: b3986c19ec008437f3230b3674ce60d1dfba2024
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703444"
---
# <a name="configure-and-access-slow-query-logs-in-the-azure-portal"></a>在 Azure 门户中配置和访问慢查询日志

可以从 Azure 门户配置、列出并下载 [Azure Database for MySQL 慢查询日志](concepts-server-logs.md)。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="configure-logging"></a>配置日志记录
配置 MySQL 慢查询日志的访问权限。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择 Azure Database for MySQL 服务器。

3. 在侧栏“监视”部分下，选择“服务器日志”。 
   ![选择“服务器日志”，单击以进行配置](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. 若要查看服务器参数，请选择标题“单击此处以启用日志并配置日志参数”。

5. 更改需要调整的参数。 在此会话中所做的更改都突出显示为紫色。 

   更改参数之后，可以单击“保存”。 或者也可以放弃所做的更改。

   ![只需单击“保存”或“放弃”](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. 单击服务器参数页上的“关闭”按钮（X 图标）返回到日志列表。

## <a name="view-list-and-download-logs"></a>查看列表并下载日志
日志记录开始后，可以在“服务器日志”窗格上查看可用慢查询日志列表，并下载单个日志文件。

1. 打开 Azure 门户。

2. 选择 Azure Database for MySQL 服务器。

3. 在侧栏“监视”部分下，选择“服务器日志”。 此页面将显示日志文件列表，如图所示：

   ![日志列表](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > 日志命名约定是 mysql-slow-< your server name>-yyyymmddhh.log。 文件名称中的日期和时间是发布日志的时间。 日志文件每 24 小时或每 7 GB 轮换一次（以先达到的条件为准）。

4. 如果需要，使用“搜索框”可快速缩小范围，找到基于日期/时间的特定日志。 按日志名称进行搜索。

5. 使用表行中日志文件旁边的“下载”按钮（向下箭头图标）下载单个日志文件，如图所示：

   ![单击“下载”图标](./media/howto-configure-server-logs-in-portal/5-download.png)

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

1. 在侧栏的“监视”部分，选择“诊断设置”。

1. 单击“+ 添加诊断设置”![添加诊断设置](./media/howto-configure-server-logs-in-portal/add-diagnostic-setting.png)

1. 提供诊断设置名称。

1. 指定发送慢查询日志的数据接收器（存储帐户、事件中心和/或 Log Analytics 工作区）。

1. 选择 "MySqlSlowLogs" 作为日志类型。
![配置诊断设置](./media/howto-configure-server-logs-in-portal/configure-diagnostic-setting.png)

1. 配置数据接收器以通过管道将慢速查询日志传输到之后，可以单击 "**保存**"。
![保存诊断设置](./media/howto-configure-server-logs-in-portal/save-diagnostic-setting.png)

1. 通过在配置的数据接收器中浏览这些日志来访问慢速查询日志。 可能需要等待长达 10 分钟的时间这些日志才会出现。

## <a name="next-steps"></a>后续步骤
- 若要了解如何以编程方式下载慢查询日志，请参阅[在 CLI 中访问慢查询日志](howto-configure-server-logs-in-cli.md)。
- 详细了解 Azure Database for MySQL 中的[慢查询日志](concepts-server-logs.md)。
- 有关参数定义和 MySQL 日志记录的详细信息，请参阅[日志](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)上的 MySQL 文档。