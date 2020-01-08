---
title: 用于 SQL 数据库的非 1433 端口 | Microsoft 文档
description: 从 ADO.NET 到 Azure SQL 数据库的客户端连接可以绕过代理直接通过 1433 以外的端口与数据库交互。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 04/03/2019
ms.openlocfilehash: a39cfd1981041c807a91a08c198378d238f0846e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568915"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>用于 ADO.NET 4.5 的非 1433 端口

本主题介绍使用 ADO.NET 4.5 或更高版本的客户端的 Azure SQL 数据库连接行为。

> [!IMPORTANT]
> 有关连接体系结构的信息，请参阅 [Azure SQL 数据库连接体系结构](sql-database-connectivity-architecture.md)。
>

## <a name="outside-vs-inside"></a>内部的外部

对于 Azure SQL 数据库的连接，首先必须询问客户端程序是在 Azure 云边界*外部*还是*内部*运行。 以下小节讨论了两种常见方案。

### <a name="outside-client-runs-on-your-desktop-computer"></a>*外部：* 客户端在台式机上运行

端口 1433 是托管 SQL 数据库客户端应用程序的台式机上唯一必须打开的端口。

### <a name="inside-client-runs-on-azure"></a>*内部：* 客户端在 Azure 上运行

如果客户端在 Azure 云边界内部运行，则它使用我们所谓的直接路由来与 SQL 数据库服务器交互。 建立连接后，客户端与数据库之间的进一步交互不涉及到任何 Azure SQL 数据库网关。

顺序如下：

1. ADO.NET 4.5（或更高版本）发起与 Azure 云的简短交互，并接收动态识别的端口号。

   * 动态识别的端口号范围为 11000-11999。
2. 然后，ADO.NET 不通过任何中间件直接连接到 SQL 数据库服务器。
3. 查询直接发送到数据库，结果直接返回到客户端。

确保 Azure 客户端计算机上 11000-11999 的端口范围已保留，供 ADO.NET 4.5 客户端与 SQL 数据库的交互使用。

* 具体而言，范围中的端口必须没有其他任何出站阻塞器。
* 在 Azure VM 上，**高级安全 Windows 防火墙**控制端口设置。
  
  * 可以使用[防火墙的用户界面](https://msdn.microsoft.com/library/cc646023.aspx)为指定 **TCP** 协议以及语法类似于 **11000-11999** 的端口范围添加规则。

## <a name="version-clarifications"></a>版本澄清

本部分澄清引用产品版本的 Moniker。 此外还列出了产品之间的版本配对。

### <a name="adonet"></a>ADO.NET

* ADO.NET 4.0 支持 TDS 7.3 协议，但不支持 7.4。
* ADO.NET 4.5 和更高版本支持 TDS 7.4 协议。

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 或更高版本

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4.2 或更高版本（JDBC 4.0 实际上支持 TDS 7.4，但未实现“重定向”）

## <a name="related-links"></a>相关链接

* ADO.NET 4.6 已于 2015 年 7 月 20 日发布。 可以在[这里](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-net-framework-4-6.aspx)访问 .NET 团队的博客通告。
* ADO.NET 4.5 已于 2012 年 8 月 15 日发布。 可以在[这里](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)访问 .NET 团队的博客通告。
  * 可以在[这里](https://blogs.msdn.com/b/dotnet/archive/20../../announcing-the-net-framework-4-5-1-preview.aspx)访问有关 ADO.NET 4.5.1 的博客文章。

* Microsoft® ODBC Driver 17 for SQL Server® - Windows、Linux 和 macOS https://www.microsoft.com/download/details.aspx?id=56567

* 通过重定向连接到 Azure SQL 数据库 V12 https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [TDS 协议版本列表](https://www.freetds.org/userguide/tdshistory.htm)
* [SQL 数据库开发概述](sql-database-develop-overview.md)
* [Azure SQL 数据库防火墙](sql-database-firewall-configure.md)
* [如何：在 SQL 数据库上配置防火墙设置](sql-database-configure-firewall-settings.md)


