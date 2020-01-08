---
title: 通过 PHP 连接到 Azure Database for MySQL
description: 本快速入门提供了多个 PHP 代码示例，你可以使用它来连接到 Azure Database for MySQL 并查询其中的数据。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 76d721ca102ae0affeba23c46d5da9fd44743f5b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608760"
---
# <a name="azure-database-for-mysql-use-php-to-connect-and-query-data"></a>Azure Database for MySQL：使用 PHP 连接和查询数据
本快速入门演示了如何使用 [PHP](https://secure.php.net/manual/intro-whatis.php) 应用程序连接到 Azure Database for MySQL。 同时还介绍了如何使用 SQL 语句在数据库中查询、插入、更新和删除数据。 本主题假设你熟悉如何使用 PHP 进行开发，但不太熟悉 Azure Database for MySQL 的用法。

## <a name="prerequisites"></a>先决条件
此快速入门使用以下任意指南中创建的资源作为起点：
- [使用 Azure 门户创建用于 MySQL 服务器的 Azure 数据库](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [使用 Azure CLI 创建用于 MySQL 服务器的 Azure 数据库](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-php"></a>安装 PHP
在自己的服务器上安装 PHP，或者创建包括 PHP 的 Azure [Web 应用](../app-service/overview.md)。

### <a name="macos"></a>MacOS
- 下载 [PHP 7.1.4 版](https://secure.php.net/downloads.php)。
- 安装 PHP 并参阅 [PHP 手册](https://secure.php.net/manual/install.macosx.php)了解进一步的配置。

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- 下载 [PHP 7.1.4 非线程安全 (x64) 版本](https://secure.php.net/downloads.php)。
- 安装 PHP 并参阅 [PHP 手册](https://secure.php.net/manual/install.unix.php)了解进一步的配置。

### <a name="windows"></a>Windows
- 下载 [PHP 7.1.4 非线程安全 (x64) 版本](https://windows.php.net/download#php-7.1)。
- 安装 PHP 并参阅 [PHP 手册](https://secure.php.net/manual/install.windows.php)了解进一步的配置。

## <a name="get-connection-information"></a>获取连接信息
获取连接到 Azure Database for MySQL 所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户的左侧菜单中，单击“所有资源”，然后搜索已创建的服务器（例如 mydemoserver）。
3. 单击服务器名称。
4. 从服务器的“概览”面板中记下“服务器名称”和“服务器管理员登录名”。 如果忘记了密码，也可通过此面板来重置密码。
 ![Azure Database for MySQL 服务器名称](./media/connect-php/1_server-overview-name-login.png)

## <a name="connect-and-create-a-table"></a>进行连接并创建表
使用以下代码进行连接，通过 CREATE TABLE SQL 语句创建表。 

代码使用 PHP 中包括的 **MySQL 改进的扩展** (mysqli) 类。 代码调用 [mysqli_init](https://secure.php.net/manual/mysqli.init.php) 和 [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) 方法连接到 MySQL。 然后，代码调用 [mysqli_query](https://secure.php.net/manual/mysqli.query.php) 方法来运行查询。 然后，代码调用 [mysqli_close](https://secure.php.net/manual/mysqli.close.php) 方法来关闭连接。

将 host、username、password 和 db_name 参数替换为你自己的值。 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>插入数据
使用以下代码进行连接，并使用 INSERT SQL 语句插入数据。

代码使用 PHP 中包括的 **MySQL 改进的扩展** (mysqli) 类。 代码使用 [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) 方法来创建已准备的 insert 语句，然后使用 [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) 方法绑定每个已插入列值的参数。 代码使用 [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) 方法运行语句，然后使用 [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) 方法关闭语句。

将 host、username、password 和 db_name 参数替换为你自己的值。 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>读取数据
使用以下代码进行连接，并使用 SELECT SQL 语句读取数据。  代码使用 PHP 中包括的 **MySQL 改进的扩展** (mysqli) 类。 代码使用 [mysqli_query](https://secure.php.net/manual/mysqli.query.php) 方法执行 SQL 查询，并使用 [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) 方法提取生成的行。

将 host、username、password 和 db_name 参数替换为你自己的值。 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>更新数据
使用以下代码进行连接，并使用 UPDATE SQL 语句更新数据。

代码使用 PHP 中包括的 **MySQL 改进的扩展** (mysqli) 类。 代码使用 [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) 方法来创建已准备的 update 语句，然后使用 [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) 方法绑定每个已更新列值的参数。 代码使用 [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) 方法运行语句，然后使用 [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) 方法关闭语句。

将 host、username、password 和 db_name 参数替换为你自己的值。 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>删除数据
使用以下代码进行连接，并使用 DELETE SQL 语句读取数据。 

代码使用 PHP 中包括的 **MySQL 改进的扩展** (mysqli) 类。 代码使用 [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) 方法来创建已准备的 delete 语句，然后使用 [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) 方法绑定语句中的 where 子句的参数。 代码使用 [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) 方法运行语句，然后使用 [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) 方法关闭语句。

将 host、username、password 和 db_name 参数替换为你自己的值。 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [通过 SSL 连接到 Azure Database for MySQL](howto-configure-ssl.md)
