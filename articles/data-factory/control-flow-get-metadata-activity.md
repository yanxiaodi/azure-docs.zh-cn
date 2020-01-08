---
title: 在 Azure 数据工厂中获取元数据活动 |Microsoft Docs
description: 了解如何使用数据工厂管道中的 "获取元数据" 活动。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 081d7219407decac5dd36a06f289436aa0da627b
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061547"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>在 Azure 数据工厂中获取元数据活动

你可以使用 "获取元数据" 活动来检索 Azure 数据工厂中任何数据的元数据。 您可以在以下情况下使用此活动:

- 验证任何数据的元数据。
- 数据准备就绪/可用时触发管道。

控制流中有以下功能：

- 您可以使用条件表达式中的 Get Metadata 活动的输出来执行验证。
- 满足条件时, 可以通过 Do Do Until 循环来触发管道。

## <a name="capabilities"></a>功能

获取元数据活动将数据集作为输入, 并返回作为输出的元数据信息。 目前支持以下连接器和相应的可检索元数据。 返回的元数据的最大大小为 1 MB。

>[!NOTE]
>如果在自承载集成运行时上运行 Get Metadata 活动, 则在3.6 版或更高版本上支持最新功能。

### <a name="supported-connectors"></a>受支持的连接器

**文件存储**

| 连接器/元数据 | itemName<br>（文件/文件夹） | itemType<br>（文件/文件夹） | 大小<br>（文件） | 已创建<br>（文件/文件夹） | lastModified<br>（文件/文件夹） |childItems<br>（文件夹） |contentMD5<br>（文件） | 结构<br/>（文件） | columnCount<br>（文件） | 存在<br>（文件/文件夹） |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| [Amazon S3](connector-amazon-simple-storage-service.md) | √/√ | √/√ | √ | x/x | √/√* | √ | X | √ | √ | √/√* |
| [Google Cloud Storage](connector-google-cloud-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | X | √ | √ | √/√* |
| [Azure Blob 存储](connector-azure-blob-storage.md) | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | √/√ | √/√ | √ | x/x | √/√ | √ | X | √ | √ | √/√ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | √/√ | √/√ | √ | x/x | √/√ | √ | X | √ | √ | √/√ |
| [Azure 文件](connector-azure-file-storage.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | X | √ | √ | √/√ |
| [文件系统](connector-file-system.md) | √/√ | √/√ | √ | √/√ | √/√ | √ | X | √ | √ | √/√ |
| [SFTP](connector-sftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | X | √ | √ | √/√ |
| [FTP](connector-ftp.md) | √/√ | √/√ | √ | x/x | √/√ | √ | X | √ | √ | √/√ |

- 对于 Amazon S3 和 Google 云存储, `lastModified`适用于 bucket 和密钥, 但不适用于虚拟文件夹, 并且`exists`适用于 bucket 和密钥, 但不适用于前缀或虚拟文件夹。
- 对于 Azure Blob 存储, `lastModified`适用于容器和 Blob, 但不适用于虚拟文件夹。

**关系数据库**

| 连接器/元数据 | 结构 | columnCount | 存在 |
|:--- |:--- |:--- |:--- |
| [Azure SQL 数据库](connector-azure-sql-database.md) | √ | √ | √ |
| [Azure SQL 数据库托管实例](connector-azure-sql-database-managed-instance.md) | √ | √ | √ |
| [Azure SQL 数据仓库](connector-azure-sql-data-warehouse.md) | √ | √ | √ |
| [SQL Server](connector-sql-server.md) | √ | √ | √ |

### <a name="metadata-options"></a>元数据选项

您可以在 "获取元数据活动字段" 列表中指定以下元数据类型以检索相应的信息:

| 元数据类型 | 描述 |
|:--- |:--- |
| itemName | 文件或文件夹的名称。 |
| itemType | 文件或文件夹的类型。 返回的值`File`为`Folder`或。 |
| 大小 | 文件大小 (以字节为单位)。 仅适用于文件。 |
| 已创建 | 文件或文件夹的创建日期时间。 |
| lastModified | 文件或文件夹的上次修改日期时间。 |
| childItems | 给定文件夹中的子文件夹和文件的列表。 仅适用于文件夹。 返回的值是每个子项的名称和类型的列表。 |
| contentMD5 | 文件的 MD5。 仅适用于文件。 |
| 结构 | 文件或关系数据库表的数据结构。 返回的值是列名称和列类型的列表。 |
| columnCount | 文件或关系表中的列数。 |
| 存在| 文件、文件夹或表是否存在。 请注意, `exists`如果在 "获取元数据字段" 列表中指定, 即使文件、文件夹或表不存在, 活动也不会失败。 相反, `exists: false`在输出中返回。 |

>[!TIP]
>如果要验证文件、文件夹或表是否存在, 请在 "获取元`exists`数据活动字段" 列表中指定。 然后, 你可以在`exists: true/false`活动输出中检查结果。 如果`exists`未在字段列表中指定, 则获取元数据活动将失败 (如果找不到该对象)。

>[!NOTE]
>当从文件存储区中获取元数据`modifiedDatetimeStart`并`modifiedDatetimeEnd`配置或`childItems`时, 中的输出将只包含给定路径中的文件, 该文件在指定范围内具有最后修改时间。 在中, 不包括子文件夹中的项。

## <a name="syntax"></a>语法

**获取元数据活动**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**Dataset**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>Type 属性

目前, 获取元数据活动可返回以下类型的元数据信息:

属性 | 说明 | 必填
-------- | ----------- | --------
fieldList | 所需的元数据信息的类型。 有关支持的元数据的详细信息, 请参阅本文的[元数据选项](#metadata-options)部分。 | 是 
dataset | 要由 Get Metadata 活动检索其元数据的引用数据集。 有关支持的连接器的信息, 请参阅[功能](#capabilities)部分。 有关数据集语法的详细信息, 请参阅特定的连接器主题。 | 是
formatSettings | 使用格式类型数据集时应用。 | 否
storeSettings | 使用格式类型数据集时应用。 | 否

## <a name="sample-output"></a>示例输出

获取元数据结果显示在活动输出中。 下面是两个显示大量元数据选项的示例。 若要在后续活动中使用结果, 请使用此模式`@{activity('MyGetMetadataActivity').output.itemName}`:。

### <a name="get-a-files-metadata"></a>获取文件的元数据

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>获取文件夹的元数据

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤
了解数据工厂支持的其他控制流活动:

- [执行管道活动](control-flow-execute-pipeline-activity.md)
- [ForEach 活动](control-flow-for-each-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
