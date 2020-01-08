---
title: 如何使用用于移动应用的 Node.js 后端服务器 SDK | Microsoft Docs
description: 了解如何使用适用于 Azure 应用服务移动应用的 Node.js 后端服务器 SDK。
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 6eaaeba8a36bcba8134d605889185fb8827dd05c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851193"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>如何使用移动应用 Node.js SDK

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

本文提供详细的信息和示例，说明如何在 Azure 应用服务的移动应用功能中使用 Node.js 后端。

## <a name="Introduction"></a>介绍

使用移动应用可将移动优化的数据访问 Web API 添加到 Web 应用程序。 提供的移动应用 SDK 适用于 ASP.NET 和 Node.js Web 应用程序。 此 SDK 提供以下操作：

* 数据访问的表操作（读取、插入、更新、删除）
* 自定义 API 操作

这两种操作都可用于 Azure 应用服务允许的所有标识提供者之间的身份验证。 这些提供者包括 Facebook、Twitter、Google 和 Microsoft 等社交标识提供者，以及用于企业标识的 Azure Active Directory。

可以在 [在 GitHub 上的示例目录]中找到每种用例的示例。

## <a name="supported-platforms"></a>受支持的平台

移动应用 Node.js SDK 支持 Node 的当前 LTS 版本及更高版本。 目前，最新 LTS 版本为 Node v4.5.0。 其他 Node 版本可能有效，但不受支持。

Azure 移动应用 Node.js SDK 支持两个数据库驱动程序： 

* node-mssqll 驱动程序支持 Azure SQL 数据库和本地 SQL Server 实例。  
* sqlite3 驱动程序仅支持单个实例上的 SQLite 数据库。

### <a name="howto-cmdline-basicapp"></a>使用命令行创建基本 Node.js 后端

每个移动应用 Node.js 后端都以 ExpressJS 应用程序的形式启动。 在适用于 Node.js 的 Web 服务框架中，ExpressJS 最广为使用。 可按以下方式创建基本的 [Express] 应用程序：

1. 在命令窗口或 PowerShell 窗口中，为项目创建目录：

        mkdir basicapp

1. 运行 `npm init` 初始化包结构：

        cd basicapp
        npm init

   `npm init` 命令将提出一系列问题以初始化项目。 查看示例输出：

   ![npm init 输出][0]

1. 从 npm 存储库安装 `express` 和 `azure-mobile-apps` 库：

        npm install --save express azure-mobile-apps

1. 创建 app.js 文件，实现基本移动服务器：

    ```javascript
    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define a TodoItem table.
    mobile.tables.add('TodoItem');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
    ```

此应用程序创建具有单个终结点 (`/tables/TodoItem`) 的移动优化 Web API，让用户使用动态架构访问基础 SQL 数据存储，而无需经过身份验证。 它适用于以下客户端库快速入门：

* [Android 客户端快速入门]
* [Apache Cordova 客户端快速入门]
* [iOS 客户端快速入门]
* [Windows 应用商店客户端快速入门]
* [Xamarin.iOS 客户端快速入门]
* [Xamarin.Android 客户端快速入门]
* [Xamarin.Forms 客户端快速入门]

可以在 [GitHub 上的 basicapp 示例]中找到此基本应用程序的代码。

### <a name="howto-vs2015-basicapp"></a>使用 Visual Studio 2015 创建 Node.js 后端

Visual Studio 2015 需要使用一个扩展在 IDE 中开发 Node.js 应用程序。 首先，请安装[用于 Visual Studio 的 Node.js 工具 1.1]。 完成安装后，创建 Express 4.x 应用程序：

1. 打开“新建项目”对话框（从“文件” > “新建” > “项目”）。
1. 展开“模板” > “JavaScript” > “Node.js”。
1. 选择“基本 Azure Node.js Express 4 应用程序”。
1. 填写项目名称。 选择“确定”。

   ![Visual Studio 2015 中的“新建项目”][1]
1. 右键单击“npm”节点，选择“安装新的 npm 包”。
1. 创建第一个 Node.js 应用程序时，可能需要刷新 npm 目录。 根据需要选择“刷新”。
1. 在搜索框中输入 **azure-mobile-apps**。 选择 **azure-mobile-apps 2.0.0** 包，然后选择“安装包”。

   ![安装新的 npm 包][2]
1. 选择“关闭”。
1. 打开 app.js 文件，添加对移动应用 SDK 的支持。 在库 `require` 语句底部的第 6 行，添加以下代码：

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    在其他 `app.use` 语句之后大约第 27 行，添加以下代码：

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    保存该文件。

1. 在本地运行应用程序 (API 在上`http://localhost:3000`提供) 或发布到 Azure。

### <a name="create-node-backend-portal"></a>使用 Azure 门户创建 Node.js 后端

可以在 [Azure 门户]中直接创建移动应用后端。 可以完成以下步骤，或根据[创建移动应用](app-service-mobile-ios-get-started.md)教程同时创建客户端和服务器。 本教程包含以下说明的简化版本，最适合用于概念认证项目。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

返回“开始使用”窗格，在“创建表 API”下面，选择“Node.js”作为后端语言。
选中“我已了解此操作会覆盖所有站点内容”框，并选择“创建 TodoItem 表”。

### <a name="download-quickstart"></a>使用 Git 下载 Node.js 后端快速入门代码项目

使用门户的“快速启动”窗格创建 Node.js 移动应用后端时，系统将创建 Node.js 项目并将其部署到站点。 可以在门户中添加表和 API，以及编辑 Node.js 后端的代码文件。 还可以使用多种部署工具下载后端项目，以便添加或修改表和 API，并重新发布项目。 有关详细信息，请参阅 [Azure 应用服务部署指南]。

以下过程使用 Git 存储库下载快速入门项目代码：

1. 安装 Git（如果尚未安装）。 安装 Git 所需的步骤因操作系统的不同而异。 有关操作系统特定的分发和安装指南，请参阅[安装 Git](https://git-scm.com/book/en/Getting-Started-Installing-Git)。
2. 若要启用后端站点的 GIT 存储库，请参阅[准备存储库](../app-service/deploy-local-git.md#prepare-your-repository)。 记下部署用户名和密码。
3. 在移动应用后端的窗格中，记下“Git 克隆 URL”设置。
4. 使用 Git 克隆 URL 执行 `git clone` 命令。 根据需要输入密码，如以下示例所示：

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. 浏览到本地目录（在上述示例中为 `/todolist`），可以看到项目文件已下载。 在 `/tables` 目录中找到 todoitem.json 文件。 此文件定义表上的权限。 另外，在同一目录中找到 todoitem.js 文件。 该文件定义表的 CRUD 操作脚本。
6. 更改项目文件之后，请运行以下命令添加、提交更改，然后将更改上传到站点：

        $ git commit -m "updated the table script"
        $ git push origin master

   将新文件添加到项目时，必须先运行 `git add .` 命令。

每次将一组新的提交内容推送到站点时，将重新发布站点。

### <a name="howto-publish-to-azure"></a>将 Node.js 后端发布到 Azure

Microsoft Azure 提供了许多将移动应用 Node.js 后端发布到 Azure 服务的机制。 这些机制包括已集成到 Visual Studio 的部署工具、命令行工具，以及基于源代码管理的持续部署选项。 有关详细信息，请参阅 [Azure 应用服务部署指南]。

Azure 应用服务提供有关 Node.js 应用程序的具体建议，请在发布后端之前查看：

* 如何[指定 Node 版本]
* 如何[使用 Node 模块]

### <a name="howto-enable-homepage"></a>启用应用程序的主页

许多应用程序是 Web 和移动应用的组合。 可以使用 ExpressJS 框架组合两个分面。 但有时，我们可能只想要实现移动接口。 移动接口用于提供主页，确保应用服务已启动并在运行。 可以提供自己的主页，或启用临时主页。 若要启用临时主页，请使用以下代码来实例化移动应用：

```javascript
var mobile = azureMobileApps({ homePage: true });
```

如果想要让此选项仅在本地开发时可供使用，可以将此设置添加到 azureMobile.js 文件。

## <a name="TableOperations"></a>表操作

azure-mobile-apps Node.js Server SDK 提供将存储在 Azure SQL 数据库中的表公开为 Web API 的机制。 它提供五个操作：

| 操作 | 描述 |
| --- | --- |
| GET /tables/*tablename* |获取表中的所有记录。 |
| GET /tables/*tablename*/:id |获取表中的特定记录。 |
| POST /tables/*tablename* |在表中创建记录。 |
| PATCH /tables/*tablename*/:id |在表中更新记录。 |
| DELETE /tables/*tablename*/:id |删除表中的记录。 |

此 Web API 支持 [OData]，并扩展表架构以支持[脱机数据同步]。

### <a name="howto-dynamicschema"></a>使用动态架构定义表

表必须先经过定义才能使用。 可以使用静态架构来定义表（在架构中定义列），或者动态定义表（SDK 根据传入的请求控制架构）。 此外，可以通过将 JavaScript 代码添加到定义，来控制 Web API 的特定层面。

根据最佳做法，应在 `tables` 目录中的 JavaScript 文件内定义每个表，并使用 `tables.import()` 方法导入表。 扩展 basic-app 示例后，调整 app.js 文件：

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Define the database schema that is exposed.
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined.
mobile.tables.initialize().then(function () {
    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
});
```

在 ./tables/TodoItem.js 中定义表：

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

表默认使用动态架构。 若要全局关闭动态架构，请在 Azure 门户中将 `MS_DynamicSchema` 应用设置指定为 false。

可以在 [GitHub 上的 todo 示例]中找到完整示例。

### <a name="howto-staticschema"></a>使用静态架构定义表

可以将列显式定义为通过 Web API 公开。 azure-mobile-apps Node.js SDK 自动将脱机数据同步所需的任何其他列添加到所提供的列表。 例如，快速入门客户端应用程序需要包含两个列的表：`text`（字符串）和 `complete`（布尔值）。  
可以在表定义 JavaScript 文件中（位于 `tables` 目录中）定义该表，如下所示：

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

module.exports = table;
```

如果以静态方式定义表，则还必须调用 `tables.initialize()` 方法，在启动时创建数据库架构。 `tables.initialize()` 方法返回 [Promise]，使 Web 服务不会在数据库初始化之前处理请求。

### <a name="howto-sqlexpress-setup"></a>使用 SQL Server Express 作为本地计算机上的开发数据存储

移动应用 Node.js SDK 提供三种现成可用的数据提供选项：

* 使用**内存**驱动程序提供非持久性示例存储。
* 使用 **mssql** 驱动程序提供可供开发使用的 SQL Server Express 数据存储。
* 使用 **mssql** 驱动程序提供可供生产使用的 Azure SQL 数据库数据存储。

移动应用 Node.js SDK 利用 [mssql Node.js 包]来建立和使用 SQL Server Express 与 SQL 数据库的连接。 若要使用此包，需要在 SQL Server Express 实例上启用 TCP 连接。

> [!TIP]
> 内存驱动程序不提供完整的测试工具集。 若要在本地测试后端，建议使用 SQL Server Express 数据存储和 mssql 驱动程序。

1. 下载并安装 [Microsoft SQL Server 2014 Express]。 请务必安装 SQL Server 2014 Express with Tools 版。 除非确实需要 64 位支持，否则请使用 32 位版本，因为它在运行时消耗的内存更少。
1. 运行 SQL Server 2014 配置管理器：

   a. 在树菜单中，展开“SQL Server 网络配置”节点。

   b. 选择“SQLEXPRESS 的协议”。

   c. 右键单击“TCP/IP”，并选择“启用”。 在弹出对话框中选择“确定”。

   d. 右键单击“TCP/IP”，并选择“属性”。

   e. 选择“IP 地址”选项卡。

   f. 找到“IPAll”节点。 在“TCP 端口”字段中输入 **1433**。

      ![配置 SQL Server Express 的 TCP/IP][3]

   g. 选择“确定”。 在弹出对话框中选择“确定”。

   h. 在树菜单中，选择“SQL Server 服务”。

   i. 右键单击 **SQL Server (SQLEXPRESS)** ，并选择“重启”。

   j. 关闭 SQL Server 2014 配置管理器。

1. 运行 SQL Server 2014 Management Studio 并连接到本地 SQL Server Express 实例：

   1. 在对象资源管理器中右键单击实例，并选择“属性”。
   1. 选择“安全性”页。
   1. 确保已选择“SQL Server 和 Windows 身份验证模式”。
   1. 选择“确定”。

      ![配置 SQL Server Express 身份验证][4]
   1. 在对象资源管理器中展开“安全性” > “登录”。
   1. 右键单击“登录”，并选择“新建登录名”。
   1. 输入登录名。 选择“SQL Server 身份验证”。 输入密码，并在“确认密码”中输入相同的密码。 密码必须符合 Windows 复杂性要求。
   1. 选择“确定”。

      ![向 SQL Server Express 添加新用户][5]
   1. 右键单击新登录名并选择“属性”。
   1. 选择“服务器角色”页。
   1. 选中 **dbcreator** 服务器角色旁边的复选框。
   1. 选择“确定”。
   1. 关闭 SQL Server 2015 Management Studio。

请务必记下选择的用户名和密码。 可能需要根据数据库要求分配其他服务器角色或权限。

Node.js 应用程序将读取 `SQLCONNSTR_MS_TableConnectionString` 环境变量，以读取此数据库的连接字符串。 可以在环境中设置此变量。 例如，可以使用 PowerShell 设置此环境变量：

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

通过 TCP/IP 连接访问数据库。 提供连接的用户名和密码。

### <a name="howto-config-localdev"></a>配置项目以进行本地开发

移动应用从本地文件系统读取名为 *azureMobile.js* 的 JavaScript 文件。 不要使用此文件在生产环境中配置移动应用 SDK。 请改用 [Azure 门户]中的“应用设置”。

azureMobile.js 文件应导出配置对象。 最常见的设置如下：

* 数据库设置
* 诊断日志记录设置
* 备用 CORS 设置

以下示例 **azureMobile.js** 文件实现上述数据库设置：

```javascript
module.exports = {
    cors: {
        origins: [ 'localhost' ]
    },
    data: {
        provider: 'mssql',
        server: '127.0.0.1',
        database: 'mytestdatabase',
        user: 'azuremobile',
        password: 'T3stPa55word'
    },
    logging: {
        level: 'verbose'
    }
};
```

建议将 **azureMobile.js** 添加到 **.gitignore** 文件（或其他源代码管理 ignore 文件），防止将密码存储在云中。 始终在 [Azure 门户]中的“应用设置”内配置生产设置。

### <a name="howto-appsettings"></a>配置移动应用的应用设置

azureMobile.js 文件中的大多数设置在 [Azure 门户]中都有对等的应用设置。 使用以下列表在“应用设置”中配置应用：

| 应用设置 | azureMobile.js 设置 | 描述 | 有效值 |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |应用的名称 |string |
| **MS_MobileLoggingLevel** |logging.level |要记录的消息的最小日志级别 |error、warning、info、verbose、debug、silly |
| **MS_DebugMode** |调试 |启用或禁用调试模式 |true、false |
| **MS_TableSchema** |data.schema |SQL 表的默认架构名称 |字符串（默认值：dbo） |
| **MS_DynamicSchema** |data.dynamicSchema |启用或禁用调试模式 |true、false |
| **MS_DisableVersionHeader** |版本（设置为 undefined） |禁用 X-ZUMO-Server-Version 标头 |true、false |
| **MS_SkipVersionCheck** |skipversioncheck |禁用客户端 API 版本检查 |true、false |

若要指定某项应用设置，请执行以下操作：

1. 登录到 [Azure 门户]。
1. 选择“所有资源”或“应用服务”，并选择移动应用的名称。
1. 默认会打开“设置”窗格。 如果没有打开，请选择“设置”。
1. 在“常规”菜单中，选择“应用程序设置”。
1. 滚动到“应用设置”部分。
1. 如果该应用设置已存在，请选择其值进行编辑。
   如果该应用设置不存在，请在“键”框中输入“应用设置”，在“值”框中输入值。
1. 选择**保存**。

更改大多数应用设置后都需要重新启动服务。

### <a name="howto-use-sqlazure"></a>使用 SQL 数据库作为生产数据存储

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

无论使用哪种 Azure 应用服务应用程序类型，将 Azure SQL 数据库用作数据存储的过程都是相同的。 如果尚未这样做，请根据以下步骤创建移动应用后端：

1. 登录到 [Azure 门户]。
1. 在窗口左上方，选择“+新建”按钮 >“Web + 移动”>“移动应用”，并为移动应用后端提供名称。
1. 在“资源组”框中，输入与应用相同的名称。
1. 系统将选择默认应用服务计划。 若要更改应用服务计划：

   a. 选择“应用服务计划” > “+ 新建”。

   b. 为新应用服务计划命名并选择适当的位置。

   c. 选择适当的服务定价层。 选择“全部查看”以查看其他定价选项，例如“免费”和“共享”。

   d. 单击**选择**按钮。

   e. 返回“应用服务计划”窗格，选择“确定”。
1. 选择“创建”。

预配移动应用后端可能需要几分钟时间。 预配移动应用后端后，门户将打开移动应用后端的“设置”窗格。

可以选择将现有的 SQL 数据库连接到移动应用后端，或创建新的 SQL 数据库。 在本部分中，我们将创建 SQL 数据库。

> [!NOTE]
> 如果在与移动应用后端相同的位置已有一个数据库，则可以选择“使用现有数据库”，并选择该数据库。 不建议使用位于不同位置的数据库，因为延迟更高。

1. 在新移动应用后端中，选择“设置” > “移动应用” > “数据” > “+添加”。
1. 在“添加数据连接”窗格中，选择“SQL 数据库 - 配置所需的设置” > “创建新数据库”。 在“名称”框中输入新数据库的名称。
1. 选择“服务器”。 在“新建服务器”窗格中的“服务器名称”框内输入唯一的服务器名称，并提供合适的服务器管理员登录名和密码。 请确保选中“允许 Azure 服务访问服务器”。 选择“确定”。

   ![创建 Azure SQL 数据库][6]
1. 在“新建数据库”窗格中，选择“确定”。
1. 返回“添加数据连接”窗格，选择“连接字符串”，并输入创建数据库时提供的登录名与密码。 如果使用现有数据库，请提供该数据库的登录凭据。 选择“确定”。
1. 再次返回“添加数据连接”窗格，选择“确定”创建数据库。

<!--- END OF ALTERNATE INCLUDE -->

创建数据库可能需要几分钟时间。 在“通知”区域中监视部署进度。 在数据库成功部署之前，请不要继续操作。 部署数据库后，会在移动应用后端的应用设置中创建 SQL 数据库实例的连接字符串。 可以在“设置” > “应用程序设置” > “连接字符串”中查看此应用设置。

### <a name="howto-tables-auth"></a>要求在访问表时进行身份验证

若要对 `tables` 终结点使用应用服务身份验证，必须先在 [Azure 门户]中配置应用服务身份验证。 有关详细信息，请参阅要使用的标识提供者的配置指南：

* [配置 Azure Active Directory 身份验证]
* [配置 Facebook 身份验证]
* [配置 Google 身份验证]
* [配置 Microsoft 身份验证]
* [配置 Twitter 身份验证]

每个表都有一个访问属性用于控制对表的访问。 以下示例显示了以静态方式定义的、要求身份验证的表。

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

访问属性可接受三个值中的一个：

* *anonymous* 表示允许客户端应用程序未经身份验证即可读取数据。
* *authenticated* 表示客户端应用程序必须随请求发送有效的身份验证令牌。
* *disabled* 表示此表当前已禁用。

如果未定义访问属性，则允许未经身份验证的访问。

### <a name="howto-tables-getidentity"></a>对表使用身份验证声明
可以设置不同的声明，在设置身份验证时将请求这些声明。 这些声明通常无法通过 `context.user` 对象获取。 但是，可以使用 `context.user.getIdentity()` 方法来检索它们。 `getIdentity()` 方法返回可解析成某个对象的 Promise。 该对象由身份验证方法（`facebook`、`google`、`twitter`、`microsoftaccount` 或 `aad`）进行键控。

例如，如果设置 Microsoft 帐户身份验证并请求电子邮件地址声明，可以使用以下表控制器将电子邮件地址添加到记录：

```javascript
var azureMobileApps = require('azure-mobile-apps');

// Create a new table definition.
var table = azureMobileApps.table();

table.columns = {
    "emailAddress": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;
table.access = 'authenticated';

/**
* Limit the context query to those records with the authenticated user email address
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function queryContextForEmail(context) {
    return context.user.getIdentity().then((data) => {
        context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
        return context.execute();
    });
}

/**
* Adds the email address from the claims to the context item - used for
* insert operations
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function addEmailToContext(context) {
    return context.user.getIdentity().then((data) => {
        context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
        return context.execute();
    });
}

// Configure specific code when the client does a request.
// READ: only return records that belong to the authenticated user.
table.read(queryContextForEmail);

// CREATE: add or overwrite the userId based on the authenticated user.
table.insert(addEmailToContext);

// UPDATE: only allow updating of records that belong to the authenticated user.
table.update(queryContextForEmail);

// DELETE: only allow deletion of records that belong to the authenticated user.
table.delete(queryContextForEmail);

module.exports = table;
```

若要查看哪些声明可用，请使用 Web 浏览器查看站点的 `/.auth/me` 终结点。

### <a name="howto-tables-disabled"></a>禁用对特定表操作的访问

除了出现在表上以外，访问属性还可用于控制单个操作。 共有四项操作：

* `read` 是对表运行的 RESTful GET 操作。
* `insert` 是对表运行的 RESTful POST 操作。
* `update` 是对表运行的 RESTful PATCH 操作。
* `delete` 是对表运行的 RESTful DELETE 操作。

例如，若要提供未经身份验证的只读表：

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>调整与表操作配合使用的查询

表操作的常见要求是提供受限制的数据视图。 例如，可以提供标有已经过身份验证的用户 ID 的表，以便只有你能够读取或更新自己的记录。 以下表定义提供此功能：

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table.
table.columns = {
    "userId": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table.
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved.
table.read(function (context) {
    context.query.where({ userId: context.user.id });
    return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user.
table.insert(function (context) {
    context.item.userId = context.user.id;
    return context.execute();
});

module.exports = table;
```

正常运行查询的操作包含一个可以使用 `where` 子句进行调整的查询属性。 该查询属性是一个 [QueryJS] 对象，用于将 OData 查询转换成数据后端可以处理的某种形式。 在简单的相等性比较方案中（如上例），可以使用映射。 还可以添加特定的 SQL 子句：

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>在表中配置软删除

软删除并不实际删除记录。 它将已删除的列设置为 true，将记录标记为已在数据库中删除。 移动应用 SDK 自动从结果中删除已软删除的记录，除非 Mobile Client SDK 使用 `IncludeDeleted()`。 若要为表配置软删除，请在表定义文件中设置 `softDelete` 属性：

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Turn on soft delete.
table.softDelete = true;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

应该建立记录删除机制：客户端应用程序、Web 作业、Azure 函数或自定义 API。

### <a name="howto-tables-seeding"></a>在数据库中植入数据

创建新应用程序时，可能需要在表中植入数据。 可在表定义 JavaScript 文件中实现此目的，如下所示：

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};
table.seed = [
    { text: 'Example 1', complete: false },
    { text: 'Example 2', complete: true }
];

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

仅当表是由移动应用 SDK 创建时，才能植入数据。 如果表已在数据库中，则不会在表中注入任何数据。 如果打开了动态架构，将从植入的数据推断架构。

建议显式调用 `tables.initialize()` 方法，在服务开始运行时创建表。

### <a name="Swagger"></a>启用 Swagger 支持
移动应用随附内置的 [Swagger] 支持。 若要启用 Swagger 支持，请先安装 swagger-ui 作为依赖项：

    npm install --save swagger-ui

然后，可以在移动应用构造函数中启用 Swagger 支持：

```javascript
var mobile = azureMobileApps({ swagger: true });
```

可能只想要在开发版本中启用 Swagger 支持。 为此，可以使用 `NODE_ENV` 应用设置：

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

`swagger` 终结点位于 http://*你的站点*.azurewebsites.net/swagger。 可以通过 `/swagger/ui` 终结点访问 Swagger UI。 如果选择要求在整个应用程序中进行身份验证，Swagger 将生成错误。 为获得最佳效果，请在“Azure 应用服务身份验证/授权”设置中选择允许未经身份验证的请求通过，并使用 `table.access` 属性控制身份验证。

如果希望只在本地进行开发时才使用 Swagger 支持，则也可以将 Swagger 选项添加到 azureMobile.js 文件中。

## <a name="a-namepushpush-notifications"></a><a name="push"/>推送通知

移动应用与 Azure 通知中心集成，因此，我们可以跨所有主要平台向数百万台设备发送有针对性的推送通知。 使用通知中心可将推送通知发送到 iOS、Android 和 Windows 设备。 若要详细了解通知中心的所有功能，请参阅[通知中心概述](../notification-hubs/notification-hubs-push-notification-overview.md)。

### <a name="send-push"></a>发送推送通知

以下代码演示如何使用 `push` 对象向已注册的 iOS 设备发送广播推送通知：

```javascript
// Create an APNS payload.
var payload = '{"aps": {"alert": "This is an APNS payload."}}';

// Only do the push if configured.
if (context.push) {
    // Send a push notification by using APNS.
    context.push.apns.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

通过从客户端创建模板推送注册，可以改为向所有受支持平台上的设备发送模板推送消息。 以下代码演示如何发送模板通知：

```javascript
// Define the template payload.
var payload = '{"messageParam": "This is a template payload."}';

// Only do the push if configured.
if (context.push) {
    // Send a template notification.
    context.push.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

### <a name="push-user"></a>使用标记将推送通知发送到经过身份验证的用户
当经过身份验证的用户注册推送通知时，用户 ID 标记会自动添加到注册中。 使用此标记可以向特定用户注册的所有设备发送推送通知。 以下代码获取发出请求的用户的 SID，并将模板推送通知发送到该用户的每个设备注册：

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

在注册来自经过身份验证客户端的推送通知时，请确保在尝试注册之前身份验证已完成。

## <a name="CustomAPI"></a>自定义 API

### <a name="howto-customapi-basic"></a>定义自定义 API

除了通过 `/tables` 终结点的数据访问 API 以外，移动应用还可提供自定义 API 覆盖范围。 自定义 API 以类似于表定义的方法定义，可访问所有相同的功能，包括身份验证。

若要将应用服务身份验证与自定义 API 配合使用，必须先在 [Azure 门户]中配置应用服务身份验证。 有关详细信息，请参阅要使用的标识提供者的配置指南：

* [配置 Azure Active Directory 身份验证]
* [配置 Facebook 身份验证]
* [配置 Google 身份验证]
* [配置 Microsoft 身份验证]
* [配置 Twitter 身份验证]

定义自定义 API 的方法与表 API 大致相同：

1. 创建 `api` 目录。
1. 在 `api` 目录中创建 API 定义 JavaScript 文件。
1. 使用 import 方法导入 `api` 目录。

下面是根据前面使用的基本应用示例所做的原型 API 定义：

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

让我们使用一个通过 `Date.now()` 方法返回服务器日期的示例 API。 下面是 api/date.js 文件：

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

每个参数是标准的 RESTful 谓词之一：GET、POST、PATCH 或 DELETE。 此方法是发送所需输出的标准 [ExpressJS 中间件]函数。

### <a name="howto-customapi-auth"></a>要求在访问自定义 API 时进行身份验证

Azure 移动应用 SDK 对 `tables` 终结点和自定义 API 使用相同的方式实现身份验证。 若要在前一部分开发的 API 中添加身份验证，请添加 `access` 属性：

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

也可以指定对特定操作的身份验证：

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

对于要求身份验证的自定义 API，必须使用与 `tables` 终结点相同的令牌。

### <a name="howto-customapi-auth"></a>处理大型文件上传

移动应用 SDK 使用[正文分析器中间件](https://github.com/expressjs/body-parser)来接受和解码提交件的正文内容。 可以将正文分析器预先配置为接受大型文件上传：

```javascript
var express = require('express'),
    bodyParser = require('body-parser'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Set up large body content handling.
app.use(bodyParser.json({ limit: '50mb' }));
app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP.
app.listen(process.env.PORT || 3000);
```

该文件在传输之前是以 Base-64 编码的。 此编码会增加实际上传的大小（因此必须考虑该大小）。

### <a name="howto-customapi-sql"></a>执行自定义 SQL 语句

移动应用 SDK 允许通过请求对象访问整个上下文。 可以轻松针对定义的数据提供程序执行参数化的 SQL 语句：

```javascript
var api = {
    get: function (request, response, next) {
        // Check for parameters. If not there, pass on to a later API call.
        if (typeof request.params.completed === 'undefined')
            return next();

        // Define the query. Anything that the mssql
        // driver can handle is allowed.
        var query = {
            sql: 'UPDATE TodoItem SET complete=@completed',
            parameters: [{
                completed: request.params.completed
            }]
        };

        // Execute the query. The context for Mobile Apps is available through
        // request.azureMobile. The data object contains the configured data provider.
        request.azureMobile.data.execute(query)
        .then(function (results) {
            response.json(results);
        });
    }
};

api.get.access = 'authenticated';
module.exports = api;
```

## <a name="Debugging"></a>调试

### <a name="howto-diagnostic-logs"></a>对移动应用进行调试、诊断和故障排除

Azure 应用服务提供多种适用于 Node.js 应用程序的调试和故障排除方法。
若要开始针对 Node.js 移动应用后端进行故障排除，请参阅以下文章：

* [监视 Azure 应用服务]
* [在 Azure 应用服务中启用诊断日志记录]
* [在 Visual Studio 中对 Azure 应用服务进行故障排除]

Node.js 应用程序可访问各种诊断日志工具。 在内部，移动应用 Node.js SDK 使用 [Winston] 进行诊断日志记录。 启用调试模式，或者在 [Azure 门户]中将 `MS_DebugMode` 应用设置指定为 true，即可自动启用日志记录。 生成的日志显示在 [Azure 门户]上的诊断日志中。

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Android 客户端快速入门]: app-service-mobile-android-get-started.md
[Apache Cordova 客户端快速入门]: app-service-mobile-cordova-get-started.md
[iOS 客户端快速入门]: app-service-mobile-ios-get-started.md
[Xamarin.iOS 客户端快速入门]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android 客户端快速入门]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms 客户端快速入门]: app-service-mobile-xamarin-forms-get-started.md
[Windows 应用商店客户端快速入门]: app-service-mobile-windows-store-dotnet-get-started.md
[脱机数据同步]: app-service-mobile-offline-data-sync.md
[配置 Azure Active Directory 身份验证]: ../app-service/configure-authentication-provider-aad.md
[配置 Facebook 身份验证]: ../app-service/configure-authentication-provider-facebook.md
[配置 Google 身份验证]: ../app-service/configure-authentication-provider-google.md
[配置 Microsoft 身份验证]: ../app-service/configure-authentication-provider-microsoft.md
[配置 Twitter 身份验证]: ../app-service/configure-authentication-provider-twitter.md
[Azure 应用服务部署指南]: ../app-service/deploy-local-git.md
[监视 Azure 应用服务]: ../app-service/web-sites-monitor.md
[在 Azure 应用服务中启用诊断日志记录]: ../app-service/troubleshoot-diagnostic-logs.md
[在 Visual Studio 中对 Azure 应用服务进行故障排除]: ../app-service/troubleshoot-dotnet-visual-studio.md
[指定 Node 版本]: ../nodejs-specify-node-version-azure-apps.md
[使用 Node 模块]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure 门户]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[GitHub 上的 basicapp 示例]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[GitHub 上的 todo 示例]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[在 GitHub 上的示例目录]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[用于 Visual Studio 的 Node.js 工具 1.1]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js 包]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS 中间件]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
