---
title: 将 Azure 流分析与 SQL 数据仓库配合使用 | Microsoft 文档
description: 有关在开发解决方案时将 Azure 流分析与 Azure SQL 数据仓库配合使用的技巧。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 94646c41d9894dd00018ff5ca44d76534d35e8c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873267"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>将 Azure 流分析与 SQL 数据仓库配合使用
Azure 流分析是一种完全托管的服务，可以在云中通过流式数据进行低延迟、高度可用、可缩放且复杂的事件处理。 可以阅读 [Azure 流分析简介][Introduction to Azure Stream Analytics]了解基本知识。 然后，可以按照 [Azure 流分析入门][Get started using Azure Stream Analytics]教程，了解如何使用流分析创建端到端解决方案。

在本文中，学习如何使用 Azure SQL 数据仓库数据库作为流分析作业的输出接收器。

## <a name="prerequisites"></a>必备组件
首先，完整执行 [Azure 流分析入门][Get started using Azure Stream Analytics]教程中所述的以下步骤。  

1. 创建事件中心输入
2. 配置并启动事件生成器应用程序
3. 预配流分析作业
4. 指定作业输入和查询

然后，创建 Azure SQL 数据仓库数据库

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>指定作业输出：Azure SQL 数据仓库数据库
### <a name="step-1"></a>步骤 1
在流分析作业中，单击页面顶部的“输出”  ，然后单击“添加”  。

### <a name="step-2"></a>步骤 2
选择 SQL 数据库。

### <a name="step-3"></a>步骤 3
在下一页输入以下值：

* *输出别名*：输入此作业输出的友好名称。
* *订阅*：
  * 如果 SQL 数据仓库数据库与此流分析作业位于同一订阅中，请选择“使用当前订阅中的 SQL 数据库”。
  * 如果数据库在不同的订阅中，请选择“使用其他订阅中的 SQL 数据库”。
* *数据库*：指定目标数据库的名称。
* *服务器名称*：为刚刚指定的数据库指定服务器名称。 可以使用 Azure 门户查找此信息。

![][server-name]

* *用户名*：指定具有数据库写入访问权限的帐户的用户名。
* *密码*：提供指定的用户帐户的密码。
* *Table*：指定数据库中目标表的名称。

![][add-database]

### <a name="step-4"></a>步骤 4
单击相应勾选按钮以添加此作业输出，并确保流分析可以成功连接到数据库。

成功连接到数据库后，门户中会显示通知。 可以单击“测试”以测试与数据库的连接。

## <a name="next-steps"></a>后续步骤
有关集成的概述，请参阅 [SQL 数据仓库集成概述][SQL Data Warehouse integration overview]。

有关更多开发技巧，请参阅 [SQL 数据仓库开发概述][SQL Data Warehouse development overview]。

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: https://azure.microsoft.com/documentation/services/stream-analytics/
