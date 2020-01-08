---
title: Azure Database for MySQL 的连接库
description: 本文列出了客户端程序连接到 Azure Database for MySQL 时可以使用的每个库或驱动程序。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 6ce0f2c761ede7d326f52f4d93d7f1b0bfa98cb2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525552"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Azure Database for MySQL 的连接库
本文列出了客户端程序连接到 Azure Database for MySQL 时可以使用的每个库或驱动程序。

## <a name="client-interfaces"></a>客户端接口
MySQL 提供标准数据库驱动程序连接，以将 MySQL 与符合行业标准 ODBC 和 JDBC 的应用程序和工具配合使用。 适用于 ODBC 或 JDBC 的任何系统都可以使用 MySQL。

| **语言** | 平台  | 其他资源  | **下载** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows、Linux | [MySQL native driver for PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/)（适用于 PHP 的 MySQL 本机驱动器 - mysqlnd） | [下载](https://secure.php.net/downloads.php) |
| ODBC | Windows、Linux、Mac OS X 和 Unix 平台 | [MySQL Connector/ODBC Developer Guide](https://dev.mysql.com/doc/connector-odbc/en/)（MySQL 连接器/ODBC 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [MySQL Connector/Net Developer Guide](https://dev.mysql.com/doc/connector-net/en/)（MySQL 连接器/Net 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | 平台独立 | [MySQL Connector/J 5.1 Developer Guide](https://dev.mysql.com/doc/connector-j/5.1/en/)（MySQL 连接器/J 5.1 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows、Linux、Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [下载](https://github.com/sidorares/node-mysql2) |
| Python | Windows、Linux、Mac OS X | [MySQL Connector/Python Developer Guide](https://dev.mysql.com/doc/connector-python/en/)（MySQL 连接器/Python 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows、Linux、Mac OS X | [MySQL Connector/C++ Developer Guide](https://dev.mysql.com/doc/connector-cpp/en/)（MySQL 连接器/C++ 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows、Linux、Mac OS X | [MySQL Connector/C Developer Guide](https://dev.mysql.com/doc/connector-c/en/)（MySQL 连接器/C 开发人员指南） | [下载](https://dev.mysql.com/downloads/connector/c/)
| Perl | Windows、Linux、Mac OS X 和 Unix 平台 | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [下载](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>后续步骤
阅读这些快速入门，了解如何使用所选语言连接和查询 Azure Database for MySQL：

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

