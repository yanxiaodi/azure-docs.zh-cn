---
title: 使用 Azure 数据工厂从 MySQL 复制数据 | Microsoft Docs
description: 了解 Azure 数据工厂中可用于将数据从 MySQL 数据库复制到支持用作接收器的数据存储的 MySQL 连接器。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 433160054dd653e1389c3d8c13faadb93782d7c0
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090040"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>使用 Azure 数据工厂从 MySQL 复制数据
> [!div class="op_single_selector" title1="选择在使用数据工厂服务版本："]
> * [版本 1](v1/data-factory-onprem-mysql-connector.md)
> * [当前版本](connector-mysql.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 MySQL 数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

>[!NOTE]
>若要将数据从或复制到[Azure Database for MySQL](../mysql/overview.md)服务，请使用专用[Azure Database for MySQL 连接器](connector-azure-database-for-mysql.md)。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 MySQL 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 MySQL 数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 MySQL 连接器支持 5.6 和5.7 版本的 MySQL。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

集成运行时提供内置 MySQL 驱动程序（从版本 3.7 开始），因此无需手动安装任何驱动程序。

对于低于 3.7 的自承载 IR 版本，需要在集成运行时计算机上安装[适用于 Microsoft Windows 的 MySQL 连接器/Net](https://dev.mysql.com/downloads/connector/net/)（6.6.5 和 6.10.7 之间的版本）。 此32位驱动程序与64位 IR 兼容。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 MySQL 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

MySQL 链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**MySql** | 是 |
| connectionString | 指定连接到 Azure Database for MySQL 实例所需的连接信息。<br/>将此字段标记为 SecureString，以便安全地将其存储在数据工厂中。 还可以将密码放在 Azure 密钥保管库中，并从连接字符串中拉取 `password` 配置。 有关更多详细信息，请参阅以下示例和[将凭据存储在 Azure 密钥保管库中](store-credentials-in-key-vault.md)一文。 | 是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 从[必备组件](#prerequisites)部分了解详细信息。 如果未指定，则使用默认 Azure Integration Runtime。 |否 |

典型的连接字符串为 `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`。 你可以根据自己的情况设置更多属性：

| 属性 | 描述 | 选项 | 必填 |
|:--- |:--- |:--- |:--- |
| SSLMode | 此选项指定驱动程序在连接到 MySQL 时是否使用 SSL 加密和验证。 例如，`SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(Default)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | 否 |
| UseSystemTrustStore | 此选项指定是使用系统信任存储中的 CA 证书还是使用指定 PEM 文件中的 CA 证书。 例如 `UseSystemTrustStore=<0/1>;`| Enabled (1) / Disabled (0) **(Default)** | 否 |

**示例：**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**示例：在 Azure 密钥保管库中存储密码**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

如果使用的是具有以下有效负载的 MySQL 链接服务，它仍然按原样受支持，但建议使用新的版本。

**先前的有效负载：**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 MySQL 数据集支持的属性列表。

若要从 MySQL 复制数据，支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**MySqlTable** | 是 |
| tableName | MySQL 数据库中的表名。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

如果使用`RelationalTable`的是类型化的数据集，则仍支持原样，但建议使用新的数据集。

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 MySQL 源支持的属性列表。

### <a name="mysql-as-source"></a>以 MySQL 作为源

若要从 MySQL 复制数据，复制活动**源**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**MySqlSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM MyTable"`。 | 否（如果指定了数据集中的“tableName”） |

**示例：**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

如果使用`RelationalSource`的是类型化的源，则仍支持原样，但建议使用新的源。

## <a name="data-type-mapping-for-mysql"></a>MySQL 的数据类型映射

从 MySQL 复制数据时，以下映射用于从 MySQL 数据类型映射到 Azure 数据工厂临时数据类型。 若要了解复制活动如何将源架构和数据类型映射到接收器，请参阅[架构和数据类型映射](copy-activity-schema-and-type-mapping.md)。

| MySQL 数据类型 | 数据工厂临时数据类型 |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
