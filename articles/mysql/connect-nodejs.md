---
title: 通过 Node.js 连接到 Azure Database for MySQL
description: 本快速入门提供多个 Node.js 代码示例，使用这些示例可连接到适用于 MySQL 的 Azure 数据库并查询其中的数据。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/21/2018
ms.openlocfilehash: ad022f6ac9cebbe92cdca3a4b368524d828a9cbb
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931563"
---
# <a name="azure-database-for-mysql-use-nodejs-to-connect-and-query-data"></a>Azure Database for MySQL：使用 Node.js 连接和查询数据
本快速入门演示如何在 Windows、Ubuntu Linux 和 Mac 平台中使用 [Node.js](https://nodejs.org/) 连接到适用于 MySQL 的 Azure 数据库。 同时还介绍了如何使用 SQL 语句在数据库中查询、插入、更新和删除数据。 本主题假设你熟悉如何使用 Node.js 进行开发，但不太熟悉 Azure Database for MySQL 的用法。

## <a name="prerequisites"></a>先决条件
此快速入门使用以下任意指南中创建的资源作为起点：
- [使用 Azure 门户创建用于 MySQL 服务器的 Azure 数据库](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [使用 Azure CLI 创建用于 MySQL 服务器的 Azure 数据库](./quickstart-create-mysql-server-database-using-azure-cli.md)

还需要：
- 安装 [Node.js](https://nodejs.org) 运行时。
- 安装 [mysql](https://www.npmjs.com/package/mysql) 包，以便从 Node.js 应用程序连接到 MySQL。 

## <a name="install-nodejs-and-the-mysql-connector"></a>安装 Node.js 和 MySQL 连接器
根据自己的平台，按照相应部分中的说明安装 Node.js。 使用 npm 将 mysql 包及其依赖项安装到项目文件夹中。

### <a name="windows"></a>**Windows**
1. 请访问 [Node.js 下载页](https://nodejs.org/en/download/)，然后选择所需的 Windows 安装程序选项。
2. 创建本地项目文件夹，例如 `nodejsmysql`。 
3. 打开命令提示符，然后将目录更改为项目文件夹，例如 `cd c:\nodejsmysql\`
4. 运行 NPM 工具，将 mysql 库安装到项目文件夹中。

   ```cmd
   cd c:\nodejsmysql\
   "C:\Program Files\nodejs\npm" install mysql
   "C:\Program Files\nodejs\npm" list
   ```

5. 通过检查 `npm list` 输出文本来验证安装。 随着新修补程序的发布，版本号可能会变化。

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
1. 运行以下命令安装 **Node.js** 和 **npm**（适用于 Node.js 的包管理器）。

   ```bash
   sudo apt-get install -y nodejs npm
   ```

2. 运行以下命令以创建项目文件夹 `mysqlnodejs`，并在该文件夹中安装 mysql 包。

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```
3. 通过检查 npm list 输出文本来验证安装。 随着新修补程序的发布，版本号可能会变化。

### <a name="mac-os"></a>**Mac OS**
1. 输入以下命令安装 **brew**（适用于 Mac OS X 和 **Node.js** 的易用程序包管理器）。

   ```bash
   ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
   brew install node
   ```
2. 运行以下命令以创建项目文件夹 `mysqlnodejs`，并在该文件夹中安装 mysql 包。

   ```bash
   mkdir nodejsmysql
   cd nodejsmysql
   npm install --save mysql
   npm list
   ```

3. 通过检查 `npm list` 输出文本来验证安装。 随着新修补程序的发布，版本号可能会变化。

## <a name="get-connection-information"></a>获取连接信息
获取连接到 Azure Database for MySQL 所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户的左侧菜单中，选择“所有资源”  ，然后搜索已创建的服务器（例如 mydemoserver  ）。
3. 选择服务器名称。
4. 从服务器的“概览”面板中记下“服务器名称”和“服务器管理员登录名”。    如果忘记了密码，也可通过此面板来重置密码。
 ![Azure Database for MySQL 服务器名称](./media/connect-nodejs/1_server-overview-name-login.png)

## <a name="running-the-javascript-code-in-nodejs"></a>在 Node.js 中运行 JavaScript 代码
1. 将 JavaScript 代码粘贴到文本文件中，然后使用文件扩展名 .js 将其保存到项目文件夹中（例如 C:\nodejsmysql\createtable.js 或 /home/username/nodejsmysql/createtable.js）。
2. 打开命令提示符或 bash shell，然后将目录更改为项目文件夹 `cd nodejsmysql`。
3. 若要运行应用程序，请输入 node 命令并后接文件名，例如 `node createtable.js`。
4. 在 Windows 上，如果 Node 应用程序不在环境变量路径中，则你可能需要使用完整路径来启动 Node 应用程序，例如 `"C:\Program Files\nodejs\node.exe" createtable.js`

## <a name="connect-create-table-and-insert-data"></a>进行连接，创建表，然后插入数据
通过以下代码进行连接，然后使用 CREATE TABLE  和 INSERT INTO  SQL 语句加载数据。

[mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) 方法用于与 MySQL 服务器对接。 [connect()](https://github.com/mysqljs/mysql#establishing-connections) 函数用于与服务器建立连接。 [query()](https://github.com/mysqljs/mysql#performing-queries) 函数用于针对 MySQL 数据库执行 SQL 查询。 

将 `host`、`user`、`password`、`database` 参数替换为你在创建服务器和数据库时指定的值。

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
    if (err) { 
        console.log("!!! Cannot connect !!! Error:");
        throw err;
    }
    else
    {
       console.log("Connection established.");
           queryDatabase();
    }   
});

function queryDatabase(){
       conn.query('DROP TABLE IF EXISTS inventory;', function (err, results, fields) { 
            if (err) throw err; 
            console.log('Dropped inventory table if existed.');
        })
       conn.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);', 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Created inventory table.');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['banana', 150], 
            function (err, results, fields) {
                if (err) throw err;
            else console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['orange', 154], 
            function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.query('INSERT INTO inventory (name, quantity) VALUES (?, ?);', ['apple', 100], 
        function (err, results, fields) {
                if (err) throw err;
            console.log('Inserted ' + results.affectedRows + ' row(s).');
        })
       conn.end(function (err) { 
        if (err) throw err;
        else  console.log('Done.') 
        });
};
```

## <a name="read-data"></a>读取数据
使用以下代码进行连接，并使用 SELECT  SQL 语句读取数据。 

[mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) 方法用于与 MySQL 服务器对接。 [connect()](https://github.com/mysqljs/mysql#establishing-connections) 方法用于与服务器建立连接。 [query()](https://github.com/mysqljs/mysql#performing-queries) 方法用于针对 MySQL 数据库执行 SQL 查询。 结果数组用于保存查询结果。

将 `host`、`user`、`password`、`database` 参数替换为你在创建服务器和数据库时指定的值。

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            readData();
        }   
    });

function readData(){
        conn.query('SELECT * FROM inventory', 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Selected ' + results.length + ' row(s).');
                for (i = 0; i < results.length; i++) {
                    console.log('Row: ' + JSON.stringify(results[i]));
                }
                console.log('Done.');
            })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Closing connection.') 
        });
};
```

## <a name="update-data"></a>更新数据
使用以下代码进行连接，并使用 UPDATE  SQL 语句读取数据。 

[mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) 方法用于与 MySQL 服务器对接。 [connect()](https://github.com/mysqljs/mysql#establishing-connections) 方法用于与服务器建立连接。 [query()](https://github.com/mysqljs/mysql#performing-queries) 方法用于针对 MySQL 数据库执行 SQL 查询。 

将 `host`、`user`、`password`、`database` 参数替换为你在创建服务器和数据库时指定的值。

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            updateData();
        }   
    });

function updateData(){
       conn.query('UPDATE inventory SET quantity = ? WHERE name = ?', [200, 'banana'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Updated ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="delete-data"></a>删除数据
使用以下代码进行连接，并使用 DELETE  SQL 语句读取数据。 

[mysql.createConnection()](https://github.com/mysqljs/mysql#establishing-connections) 方法用于与 MySQL 服务器对接。 [connect()](https://github.com/mysqljs/mysql#establishing-connections) 方法用于与服务器建立连接。 [query()](https://github.com/mysqljs/mysql#performing-queries) 方法用于针对 MySQL 数据库执行 SQL 查询。 

将 `host`、`user`、`password`、`database` 参数替换为你在创建服务器和数据库时指定的值。

```javascript
const mysql = require('mysql');

var config =
{
    host: 'mydemoserver.mysql.database.azure.com',
    user: 'myadmin@mydemoserver',
    password: 'your_password',
    database: 'quickstartdb',
    port: 3306,
    ssl: true
};

const conn = new mysql.createConnection(config);

conn.connect(
    function (err) { 
        if (err) { 
            console.log("!!! Cannot connect !!! Error:");
            throw err;
        }
        else {
            console.log("Connection established.");
            deleteData();
        }   
    });

function deleteData(){
       conn.query('DELETE FROM inventory WHERE name = ?', ['orange'], 
            function (err, results, fields) {
                if (err) throw err;
                else console.log('Deleted ' + results.affectedRows + ' row(s).');
        })
       conn.end(
           function (err) { 
                if (err) throw err;
                else  console.log('Done.') 
        });
};
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用导出和导入功能迁移数据库](./concepts-migrate-import-export.md)
