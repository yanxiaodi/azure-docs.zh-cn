---
title: 使用 Azure 数据工厂从 MongoDB 复制数据 | Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 Mongo DB 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 77d0f632c763651004efa46edf027719040f4760
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967483"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>使用 Azure 数据工厂从 MongoDB 复制数据
> [!div class="op_single_selector" title1="选择在使用数据工厂服务版本："]
> * [版本 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [当前版本](connector-mongodb.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 MongoDB 数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

>[!IMPORTANT]
>ADF 发布了一款新的 MongoDB 连接器，与这个基于 ODBC 的实现相比，该连接器提供更好的本机 MongoDB 支持，详情请参阅 [MongoDB 连接器](connector-mongodb.md)一文。 这一旧版 MongoDB 连接器仍支持向后兼容，但是对于任何新的工作负载，请使用新连接器。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 MongoDB 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 MongoDB 连接器支持：

- MongoDB **版本 2.4、2.6、3.0、3.2、3.4 和 3.6**。
- 使用基本或匿名身份验证复制数据。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

集成运行时提供内置 MongoDB 驱动程序，因此从 MongoDB 复制数据时，无需手动安装任何驱动程序。

## <a name="getting-started"></a>开始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 MongoDB 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

MongoDB 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |type 属性必须设置为：MongoDb |是 |
| 服务器 |MongoDB 服务器的 IP 地址或主机名。 |是 |
| port |MongoDB 服务器用于侦听客户端连接的 TCP 端口。 |否（默认值为 27017） |
| databaseName |要访问的 MongoDB 数据库名称。 |是 |
| authenticationType | 用于连接 MongoDB 数据库的身份验证类型。<br/>允许值包括：基本和匿名。 |是 |
| username |用于访问 MongoDB 的用户帐户。 |是（如果使用基本身份验证）。 |
| password |用户密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 |是（如果使用基本身份验证）。 |
| authSource |要用于检查身份验证凭据的 MongoDB 数据库名称。 |否。 对于基本身份验证，默认使用管理员帐户和使用 databaseName 属性指定的数据库。 |
| enableSsl | 指定是否使用 SSL 加密到服务器的连接。 默认值为 false。  | 否 |
| allowSelfSignedServerCert | 指定是否允许来自服务器的自签名证书。 默认值为 false。  | 否 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[必备组件](#prerequisites)部分了解详细信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

**示例：**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集和链接服务](concepts-datasets-linked-services.md)。 MongoDB 数据集支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：MongoDbCollection | 是 |
| collectionName |MongoDB 数据库中集合的名称。 |是 |

**示例：**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 MongoDB 源支持的属性列表。

### <a name="mongodb-as-source"></a>以 MongoDB 作为源

复制活动源部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**MongoDbSource** | 是 |
| query |使用自定义 SQL-92 查询读取数据。 例如：select * from MyTable。 |否（如果指定了数据集中的“collectionName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> 指定 SQL 查询时，请注意 DateTime 的格式。 例如：`SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'`，或使用参数 `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>数据工厂的构架

Azure 数据工厂服务通过使用 MongoDB 集合中**最新的 100 个文档**来推断该集合的架构。 如果这 100 个文档不包含完整架构，则在复制操作期间可能忽略某些列。

## <a name="data-type-mapping-for-mongodb"></a>MongoDB 的数据类型映射

从 MongoDB 复制数据时，以下映射用于从 MongoDB 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| MongoDB 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| Binary |Byte[] |
| Boolean |Boolean |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| Object |重新标准化为平展列，以“_”作为嵌套分隔符 |

> [!NOTE]
> 要了解对使用虚拟表的数组的支持，请参阅[支持使用虚拟表的复杂类型](#support-for-complex-types-using-virtual-tables)一节。
>
> 目前不支持以下 MongoDB 数据类型：DBPointer、JavaScript、最大/最小键、正则表达式、符号、时间戳、未定义。

## <a name="support-for-complex-types-using-virtual-tables"></a>支持使用虚拟表的复杂类型

Azure 数据工厂使用内置的 ODBC 驱动程序连接到 MongoDB 数据库，并从中复制数据。 对于数组或文档间不同类型的对象等复杂类型，该驱动程序会将数据重新标准化到相应虚拟表中。 具体而言，如果表中包含此类列，该驱动程序会生成以下虚拟表：

* **基表**，其中包含与实际表相同的数据（复杂类型列除外）。 基表使用与其所表示的实际表相同的名称。
* 对于每个复杂类型列生成一个**虚拟表**，这会扩展嵌套数据。 使用实际表名称、分隔符“_”和数组或对象的名称，对虚拟表命名。

虚拟表引用实际表中的数据，以使驱动程序能访问非规范化的数据。 通过查询和联接虚拟表，可访问 MongoDB 数组的内容。

### <a name="example"></a>示例

例如，此处 ExampleTable 为 MongoDB 表，其中“发票”列的每个单元格包含对象数组，“评级”列包含标量类型数组。

| _id | 客户名 | 发票 | 服务级别 | 评级 |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price:"1235", discount:"0.2"}] |银色 |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price:"12543", discount:"0.0"}] |金色 |[1,2] |

该驱动程序会生成多个虚拟表来表示此单个表。 第一个虚拟表是名为“ExampleTable”的基表，如下例所示。 基表包含原始表中的所有数据，但已省略数组中的数据，这些数据会在虚拟表中展开。

| _id | 客户名称 | 服务级别 |
| --- | --- | --- |
| 1111 |ABC |银色 |
| 2222 |XYZ |金牌服务 |

下表显示在示例中表示原始数组的虚拟表。 这些表包含以下项：

* 通过 _id 列返回到原始主键列的引用，该列与原始数组的行对应
* 原始数组中数据位置的指示
* 该数组中每个元素展开的数据

**“ExampleTable_Invoices”表：**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | 价格 | 折扣 |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |toaster |456 |0.2 |
| 1111 |1 |124 |oven |1235 |0.2 |
| 2222 |0 |135 |fridge |12543 |0.0 |

**“ExampleTable_Ratings”表：**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md##supported-data-stores-and-formats)。
