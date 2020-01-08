---
title: 在 Azure Cosmos DB 的用于 MongoDB 的 API 中使用 MongoDB 读取首选项
description: 了解如何在 Azure Cosmos DB 的用于 MongoDB 的 API 中使用 MongoDB 读取首选项
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 8fc66d70b840578bff086519a7b39e5f389a3de3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66479616"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>如何使用 Azure Cosmos DB 的用于 MongoDB 的 API 全局分发读取

本文介绍如何在 Azure Cosmos DB 的用于 MongoDB 的 API 中使用 [MongoDB 读取首选项](https://docs.mongodb.com/manual/core/read-preference/)设置全局分配读取操作。

## <a name="prerequisites"></a>必备组件 
如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

请参阅此[快速入门](tutorial-global-distribution-mongodb.md)，获取有关在全局分布区中使用 Azure 门户设置 Cosmos 帐户然后连接到它的说明。

## <a name="clone-the-sample-application"></a>克隆示例应用程序

打开 git 终端窗口（例如 git bash）并使用 `cd` 切换到工作目录。  

运行下列命令以克隆示例存储库。 根据所需的平台，使用以下示例存储库之一：

1. [.NET 示例应用程序](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS 示例应用程序]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose 示例应用程序](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java 示例应用程序](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot 示例应用程序](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>运行应用程序

根据所用的平台，安装所需的包并启动应用程序。 若要安装依赖项，请遵循示例应用程序存储库中包含的自述文件。 例如，在 NodeJS 示例应用程序中，使用以下命令安装所需的包并启动应用程序。

```bash
cd mean
npm install
node index.js
```
应用程序会尝试连接到 MongoDB 源，但连接会失败，因为连接字符串无效。 遵循自述文件中的步骤更新连接字符串 `url`。 此外，将 `readFromRegion` 更新为 Cosmos 帐户中的读取区域。 以下说明摘自 NodeJS 示例：

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

完成这些步骤后，示例应用程序将会运行并生成以下输出：

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>使用读取首选项模式进行读取

MongoDB 协议提供以下读取首选项模式供客户端使用：

1. PRIMARY
2. PRIMARY_PREFERRED
3. SECONDARY
4. SECONDARY_PREFERRED
5. NEAREST

请参阅详细的 [MongoDB 读取首选项行为](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior)文档，详细了解其中每种读取首选项模式的行为。 在 Cosmos DB 中，primary 映射到 WRITE 区域，secondary 映射到 READ 区域。

根据常见的方案，我们建议使用以下设置：

1. 如果需要**低延迟读取**，可以使用 **NEAREST** 读取首选项模式。 此设置会将读取操作定向到最靠近的可用区域。 请注意，如果最靠近的区域是 WRITE 区域，则这些操作会定向到该区域。
2. 如果需要**读取操作的高可用性和地理分配**（延迟不是一种约束），则可以使用 **SECONDARY PREFERRED** 读取首选项模式。 此设置会将读取操作定向到可用的 READ 区域。 如果没有可用的 READ 区域，请求将定向到 WRITE 区域。

示例应用程序中的以下代码片段演示如何在 NodeJS 中配置 NEAREST 读取首选项：

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

同样，以下代码片段演示如何在 NodeJS 中配置 SECONDARY_PREFERRED 读取首选项：

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

还可以通过在连接字符串 URI 选项中将 `readPreference` 作为参数传递来设置读取首选项：

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

请参阅其他平台（例如 [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) 和 [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)）的相应示例应用程序存储库。

## <a name="read-using-tags"></a>使用标记读取

除了读取首选项模式以外，MongoDB 协议还允许使用标记来定向读取操作。 在 Cosmos DB 的用于 MongoDB 的 API 中，`region` 标记已默认包含为 `isMaster` 响应的一部分：

```json
"tags": {
         "region": "West US"
      }
```

因此，MongoClient 可以结合区域名称使用 `region` 标记将读取操作定向到特定的区域。 对于 Cosmos 帐户，可以 Azure 门户中左侧的“设置”->“全局副本数据”下面找到区域名称  。 此设置可用于实现**读取隔离** - 可让客户端应用程序将读取操作定向到特定的区域。 此设置非常适合用于在后台运行的，并且不属于生产关键型服务的非生产/分析型方案。

示例应用程序中的以下代码片段演示如何在 NodeJS 中使用标记配置读取首选项：

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

请参阅其他平台（例如 [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) 和 [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)）的相应示例应用程序存储库。

本文介绍了如何在 Azure Cosmos DB 的用于 MongoDB 的 API 中使用读取首选项全局分配读取操作。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本文在 Azure 门户中创建的所有资源，步骤如下：

1. 在 Azure 门户的左侧菜单中，单击“资源组”  ，并单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。  

## <a name="next-steps"></a>后续步骤

* [将 MongoDB 数据导入 Azure Cosmos DB](mongodb-migrate.md)
* [使用 Azure Cosmos DB 的用于 MongoDB 的 API 设置全局分布式数据库](tutorial-global-distribution-mongodb.md)
* [使用 Azure Cosmos DB 模拟器在本地进行开发](local-emulator.md)
