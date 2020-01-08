---
title: Azure Functions 的 Azure 表存储绑定
description: 了解如何在 Azure Functions 中使用 Azure 表存储绑定。
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions，函数，事件处理，动态计算，无服务体系结构
ms.service: azure-functions
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 464c1a8ab27f6615fdffd8efa6ab20d75e10a7c1
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71171186"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions 的 Azure 表存储绑定

本文介绍如何在 Azure Functions 中使用 Azure 表存储绑定。 Azure Functions 支持 Azure 表存储使用输入和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>包 - Functions 1.x

[Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 包 2.x 版中提供了表存储绑定。 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>包 - Functions 2.x

[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 包 3.x 版中提供了表存储绑定。 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>输入

使用 Azure 表存储输入绑定读取 Azure 存储帐户中的表。

## <a name="input---example"></a>输入 - 示例

参阅语言特定的示例：

* [C# 读取一个实体](#input---c-example---one-entity)
* [C# 绑定到 IQueryable](#input---c-example---iqueryable)
* [C# 绑定到 CloudTable](#input---c-example---cloudtable)
* [C# 脚本读取一个实体](#input---c-script-example---one-entity)
* [C# 脚本绑定到 IQueryable](#input---c-script-example---iqueryable)
* [C# 脚本绑定到 CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>输入 - C# 示例 - 一个实体

以下示例演示读取单个表行的 [C# 函数](functions-dotnet-class-library.md)。 

行键值“{queueTrigger}”指示行键来自队列消息字符串。

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="input---c-example---iqueryable"></a>输入 - C# 示例 - IQueryable

以下示例演示读取多个表行的 [C# 函数](functions-dotnet-class-library.md)。 请注意，`MyPoco` 类派生自 `TableEntity`。

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="input---c-example---cloudtable"></a>输入 - C# 示例 - CloudTable

[Functions v2 运行时](functions-versions.md)不支持 `IQueryable`。 一种替代方法是使用 `CloudTable` 方法参数通过 Azure 存储 SDK 来读取表。 下面是一个查询 Azure Functions 日志表的 2.x 函数示例：

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

有关如何使用 CloudTable 的详细信息，请参阅 [Azure 表存储入门](../cosmos-db/table-storage-how-to-use-dotnet.md)。

如果尝试绑定到 `CloudTable` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

### <a name="input---c-script-example---one-entity"></a>输入 - C# 脚本示例 - 一个实体

以下示例演示 *function.json* 文件中的一个表输入绑定以及使用该绑定的 [C# 脚本](functions-reference-csharp.md)代码。 该函数使用队列触发器来读取单个表行。 

*function.json* 文件指定 `partitionKey` 和 `rowKey`。 `rowKey` 值“{queueTrigger}”指示行键来自队列消息字符串。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[配置](#input---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---iqueryable"></a>输入 - C# 脚本示例 - IQueryable

以下示例演示 *function.json* 文件中的一个表输入绑定以及使用该绑定的 [C# 脚本](functions-reference-csharp.md)代码。 该函数读取队列消息中指定的分区键的实体。

function.json 文件如下所示：

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[配置](#input---configuration)部分解释了这些属性。

C# 脚本代码添加对 Azure 存储 SDK 的引用，以便实体类型可以从 `TableEntity` 派生。

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="input---c-script-example---cloudtable"></a>输入 - C# 脚本示例 - CloudTable

[Functions v2 运行时](functions-versions.md)不支持 `IQueryable`。 一种替代方法是使用 `CloudTable` 方法参数通过 Azure 存储 SDK 来读取表。 下面是一个查询 Azure Functions 日志表的 2.x 函数示例：

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

有关如何使用 CloudTable 的详细信息，请参阅 [Azure 表存储入门](../cosmos-db/table-storage-how-to-use-dotnet.md)。

如果尝试绑定到 `CloudTable` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

### <a name="input---f-example"></a>输入 - F# 示例

以下示例演示 *function.json* 文件中的一个表输入绑定以及使用该绑定的 [F# 脚本](functions-reference-fsharp.md)代码。 该函数使用队列触发器来读取单个表行。 

*function.json* 文件指定 `partitionKey` 和 `rowKey`。 `rowKey` 值“{queueTrigger}”指示行键来自队列消息字符串。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[配置](#input---configuration)部分解释了这些属性。

F# 代码如下所示：

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>输入 - JavaScript 示例

以下示例演示了 function.json 文件中的表输入绑定以及使用该绑定的 [JavaScript 代码](functions-reference-node.md)。 该函数使用队列触发器来读取单个表行。 

*function.json* 文件指定 `partitionKey` 和 `rowKey`。 `rowKey` 值“{queueTrigger}”指示行键来自队列消息字符串。

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

[配置](#input---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

### <a name="input---java-example"></a>输入 - Java 示例

以下示例显示了 HTTP 触发的函数，该函数返回表存储中指定分区中项的总计数。

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>输入 - 特性
 
在 [C# 类库](functions-dotnet-class-library.md)中，请使用以下属性来配置表输入绑定：

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  该特性的构造函数采用表名称、分区键和行键。 可对函数的 out 参数或返回值使用该特性，如以下示例中所示：

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  有关完整示例，请参阅“输入 - C#”示例。

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  提供另一种方式来指定要使用的存储帐户。 构造函数采用包含存储连接字符串的应用设置的名称。 可以在参数、方法或类级别应用该特性。 以下示例演示类级别和方法级别：

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

要使用的存储帐户按以下顺序确定：

* `Table` 特性的 `Connection` 属性。
* 作为 `Table` 特性应用到同一参数的 `StorageAccount` 特性。
* 应用到函数的 `StorageAccount` 特性。
* 应用到类的 `StorageAccount` 特性。
* 函数应用的默认存储帐户（“AzureWebJobsStorage”应用设置）。

## <a name="input---java-annotations"></a>输入 - Java 注释

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将来自表存储的参数使用 `@TableInput` 注释。  可以将此注释与本机 Java 类型、POJO 或使用了 Optional\<T> 的可为 null 的值一起使用。 

## <a name="input---configuration"></a>输入 - 配置

下表解释了在 *function.json* 文件和 `Table` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**type** | 不适用 | 必须设置为 `table`。 在 Azure 门户中创建绑定时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建绑定时，会自动设置此属性。 |
|**name** | 不适用 | 表示函数代码中的表或实体的变量的名称。 | 
|**tableName** | **TableName** | 表的名称。| 
|**partitionKey** | **PartitionKey** |可选。 要读取的表实体的分区键。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**rowKey** |**RowKey** | 可选。 要读取的表实体的行键。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**take** |**Take** | 可选。 要在 JavaScript 中读取的最大实体数。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**filter** |**筛选器** | 可选。 JavaScript 中的表输入的 OData 筛选表达式。 有关如何使用此属性的指导，请参阅[用法](#input---usage)部分。| 
|**连接** |**Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>输入 - 用法

表存储输入绑定支持以下方案：

* **在 C# 或 C# 脚本中读取一行**

  设置 `partitionKey` 和 `rowKey`。 使用方法参数 `T <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 `T` 通常是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 此方案中不使用 `filter` 和 `take` 属性。 

* **在 C# 或 C# 脚本中读取一行或多行**

  使用方法参数 `IQueryable<T> <paramName>` 访问表数据。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 `T` 必须是实现 `ITableEntity` 或派生自 `TableEntity` 的类型。 可以使用 `IQueryable` 方法执行任何所需的筛选。 此方案中不使用 `partitionKey`、`rowKey`、`filter` 和 `take` 属性。  

  > [!NOTE]
  > [Functions v2 运行时](functions-versions.md)不支持 `IQueryable`。 一种替代方法是[使用 CloudTable paramName 方法参数](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable)通过 Azure 存储 SDK 来读取表。 如果尝试绑定到 `CloudTable` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

* **在 JavaScript 中读取一行或多行**

  设置 `filter` 和 `take` 属性。 不要设置 `partitionKey` 或 `rowKey`。 使用 `context.bindings.<BINDING_NAME>` 访问输入一个或多个输入表实体。 反序列化的对象具有 `RowKey` 和 `PartitionKey` 属性。

## <a name="output"></a>Output

使用 Azure 表存储输出绑定读取将实体写入 Azure 存储帐户中的表。

> [!NOTE]
> 此输出绑定不支持更新现有实体。 请使用 [Azure 存储 SDK](https://docs.microsoft.com/azure/cosmos-db/tutorial-develop-table-dotnet#delete-an-entity) 中的 `TableOperation.Replace` 操作来更新现有实体。   

## <a name="output---example"></a>输出 - 示例

参阅语言特定的示例：

* [C#](#output---c-example)
* [C# 脚本 (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>输出 - C# 示例

以下示例演示使用 HTTP 触发器写入单个表行的 [C# 函数](functions-dotnet-class-library.md)。 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>输出 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个表输出绑定以及使用该绑定的 [C# 脚本](functions-reference-csharp.md)代码。 该函数写入多个表实体。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[配置](#output---configuration)部分解释了这些属性。

C# 脚本代码如下所示：

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

### <a name="output---f-example"></a>输出 - F# 示例

以下示例演示 *function.json* 文件中的一个表输出绑定以及使用该绑定的 [F# 脚本](functions-reference-fsharp.md)代码。 该函数写入多个表实体。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[配置](#output---configuration)部分解释了这些属性。

F# 代码如下所示：

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>输出 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个表输出绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数写入多个表实体。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[配置](#output---configuration)部分解释了这些属性。

JavaScript 代码如下所示：

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

## <a name="output---attributes"></a>输出 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)。

该特性的构造函数采用表名称。 可对函数的 `out` 参数或返回值使用该特性，如以下示例中所示：

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

可以设置 `Connection` 属性来指定要使用的存储帐户，如以下示例中所示：

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

有关完整示例，请参阅[输出 - C# 示例](#output---c-example)。

可以使用 `StorageAccount` 特性在类、方法或参数级别指定存储帐户。 有关详细信息，请参阅[输入 - 特性](#input---attributes)。

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 function.json 文件和 `Table` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**type** | 不适用 | 必须设置为 `table`。 在 Azure 门户中创建绑定时，会自动设置此属性。|
|**direction** | 不适用 | 必须设置为 `out`。 在 Azure 门户中创建绑定时，会自动设置此属性。 |
|**name** | 不适用 | 在函数代码中使用的、表示表或实体的变量名称。 设置为 `$return` 可引用函数返回值。| 
|**tableName** |**TableName** | 表的名称。| 
|**partitionKey** |**PartitionKey** | 要写入的表实体的分区键。 有关如何使用此属性的指导，请参阅[用法部分](#output---usage)。| 
|**rowKey** |**RowKey** | 要写入的表实体的行键。 有关如何使用此属性的指导，请参阅[用法部分](#output---usage)。| 
|**连接** |**Connection** | 包含要用于此绑定的存储连接字符串的应用设置的名称。 如果应用设置名称以“AzureWebJobs”开始，则只能在此处指定该名称的余下部分。 例如，如果将 `connection` 设置为“MyStorage”，函数运行时将会查找名为“AzureWebJobsMyStorage”的应用设置。 如果将 `connection` 留空，函数运行时将使用名为 `AzureWebJobsStorage` 的应用设置中的默认存储连接字符串。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>输出 - 用法

表存储输出绑定支持以下方案：

* **以任何语言编写一行**

  在 C# 和 C# 脚本中，可以使用 `out T paramName` 等方法参数或函数返回值访问输出表实体。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 如果 *function.json* 文件或 `Table` 特性提供了分区键和行键，则 `T` 可以是任何可序列化类型。 否则，`T` 必须是包含 `PartitionKey` 和 `RowKey` 属性的类型。 在此方案中，`T` 通常实现 `ITableEntity` 或派生自 `TableEntity`，但不一定非要这样。

* **用 C# 或 C# 脚本写入一行或多行**

  在 C# 和 C# 脚本中，可以使用方法参数 `ICollector<T> paramName` 或 `IAsyncCollector<T> paramName` 访问输出表实体。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 `T` 指定要添加的实体的架构。 通常，`T` 派生自 `TableEntity` 或实现 `ITableEntity`，但不一定非要这样。 此方案不使用 *function.json* 中的分区键和行键值，也不使用 `Table` 特性构造函数。

  一种替代方法是使用 `CloudTable` 方法参数通过 Azure 存储 SDK 来写入表。 如果尝试绑定到 `CloudTable` 并收到错误消息，请确保已引用[正确的存储 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。 有关绑定到 `CloudTable` 的代码的示例，请参阅本文前面的 [C#](#input---c-example---cloudtable) 或 [C# 脚本](#input---c-script-example---cloudtable)的输入绑定示例。

* **在 JavaScript 中写入一行或多行**

  在 JavaScript 函数中，可以使用 `context.bindings.<BINDING_NAME>` 访问表输出。

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 参考 |
|---|---|
| 表 | [表错误代码](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob、表、队列 | [存储错误代码](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob、表、队列 | [故障排除](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
