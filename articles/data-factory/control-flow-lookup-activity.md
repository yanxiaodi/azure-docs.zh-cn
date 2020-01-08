---
title: Azure 数据工厂中的查找活动 | Microsoft Docs
description: 了解如何使用查找活动从外部源查找值。 此输出可进一步由后续活动引用。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 9658987092027b38ab0cab1feb3df4be0a91e350
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141665"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure 数据工厂中的查找活动

查找活动可以从任何 Azure 数据工厂支持的数据源检索数据集。 在以下方案中使用它：
- 动态确定哪些对象在后续活动中工作，而不是针对对象名称进行硬编码。 一些对象示例包括文件和表。

查找活动读取并返回配置文件或表的内容。 它还返回执行查询或存储过程的结果。 查找活动的输出可用于后续复制或转换活动（如果它是单一实例值）。 该输出可用于 ForEach 活动（如果它是属性数组）。

## <a name="supported-capabilities"></a>支持的功能

查找活动支持以下数据源。 查找活动可返回的最大行数是 5,000，最大大小为 2 MB。 目前，查找活动在超时前的最长持续时间为 1 小时。

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>语法

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Type 属性

姓名 | 描述 | type | 必需？
---- | ----------- | ---- | --------
dataset | 为查找提供数据集引用。 从每篇相应的连接器文章的“数据集属性”部分中获取详细信息。 | 键/值对 | 是
source | 包含特定于数据集的源属性，与复制活动源相同。 从每篇相应的连接器文章的“复制活动属性”部分中获取详细信息。 | 键/值对 | 是
firstRowOnly | 指示仅返回第一行还是返回所有行。 | Boolean | 否。 默认值为 `true`。

> [!NOTE]
> 
> * 不支持 **ByteArray** 类型的源列。
> * 数据集定义不支持**结构**。 对于文本格式化文件，使用标头行提供列名。
> * 如果查找源是 JSON 文件，则不支持用于重塑 JSON 对象的 `jsonPathDefinition` 设置。 将检索整个对象。

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>在后续活动中使用查找活动结果

查找结果会返回到活动运行结果的 `output` 节。

* **当 `firstRowOnly` 设置为 `true`（默认值）时**，输出格式如以下代码所示。 查找结果位于固定的 `firstRow` 键下。 若要在后续活动中使用该结果，请使用 `@{activity('MyLookupActivity').output.firstRow.TableName}` 模式。

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **当 `firstRowOnly` 设置为 `false` 时**，输出格式如以下代码所示。 `count` 字段指示返回的记录数。 详细值显示在固定的 `value` 数组下。 在这种情况下，查找活动后跟 [Foreach 活动](control-flow-for-each-activity.md)。 使用 `@activity('MyLookupActivity').output.value` 模式将 `value` 数组传递给 ForEach 活动 `items` 字段。 若要访问 `value` 数组中的元素，请使用以下语法：`@{activity('lookupActivity').output.value[zero based index].propertyname}`。 例如 `@{activity('lookupActivity').output.value[0].tablename}`。

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>复制活动示例
在本示例中，复制活动将 Azure SQL 数据库实例的 SQL 表中的数据复制到 Azure Blob 存储。 SQL 表的名称存储在 Blob 存储的 JSON 文件中。 查找活动在运行时查找表名。 使用此方法动态修改 JSON。 不需要重新部署管道或数据集。 

本示例演示如何只查找第一行。 若要查找所有行并将结果与 ForEach 活动链接，请参阅[使用 Azure 数据工厂批量复制多个表](tutorial-bulk-copy.md)中的示例。

### <a name="pipeline"></a>管道
此管道包含两个活动：查找和复制。 

- 查找活动配置为使用 **LookupDataset**，该项引用 Azure Blob 存储中的一个位置。 查找活动在此位置从 JSON 文件读取 SQL 表名称。 
- 复制活动使用查找活动的输出，即 SQL 表的名称。 **SourceDataset** 中的 **tableName** 属性配置为使用查找活动的输出。 复制活动将数据从 SQL 表复制到 Azure Blob 存储中的一个位置。 该位置由 **SinkDataset** 属性指定。 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>查找数据集
**查找**数据集是指由 **AzureStorageLinkedService** 类型指定的 Azure 存储查找文件夹中的 **sourcetable.json** 文件。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>复制活动的**源**数据集
**源**数据集使用查找活动的输出，即 SQL 表名称。 复制活动将数据从此 SQL 表复制到 Azure Blob 存储中的一个位置。 该位置由**接收器**数据集指定。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>复制活动的**接收器**数据集
复制活动将数据从 SQL 表复制到 Azure 存储中 **csv** 文件夹下的 **filebylookup.csv** 文件。 该文件由 **AzureStorageLinkedService** 属性指定。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure 存储链接服务
此存储帐户包含 JSON 文件和 SQL 表名称。 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL 数据库链接服务
此 Azure SQL 数据库实例包含要复制到 Blob 存储的数据。 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>对象集

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>对象数组

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>限制和解决方法

以下是 Lookup 活动的一些限制以及建议的解决方法。

| 限制 | 解决方法 |
|---|---|
| Lookup 活动最多有 5,000 行，最大大小为 2 MB。 | 设计一个外部管道对内部管道进行迭代的两级管道，该管道会检索不超过最大行数或大小的数据。 |
| | |

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动： 

- [执行管道活动](control-flow-execute-pipeline-activity.md)
- [ForEach 活动](control-flow-for-each-activity.md)
- [GetMetadata 活动](control-flow-get-metadata-activity.md)
- [Web 活动](control-flow-web-activity.md)
