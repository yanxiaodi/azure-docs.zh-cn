---
title: 使用 Azure 数据工厂从 ServiceNow 复制数据 | Microsoft Docs
description: 了解如何通过在 Azure 数据工厂管道中使用复制活动，将数据从 ServiceNow 复制到支持的接收器数据存储。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: a76baf65b2dc7d0cdb444b79e697930188417748
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71089485"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>使用 Azure 数据工厂从 ServiceNow 复制数据

本文概述了如何使用 Azure 数据工厂中的复制活动从 ServiceNow 复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

## <a name="supported-capabilities"></a>支持的功能

以下活动支持此 ServiceNow 连接器：

- 带有[支持的源或接收器矩阵](copy-activity-overview.md)的[复制活动](copy-activity-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)

可以将数据从 ServiceNow 复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure 数据工厂提供内置的驱动程序用于启用连接，因此无需使用此连接器手动安装任何驱动程序。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

对于特定于 ServiceNow 连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

ServiceNow 链接服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**ServiceNow** | 是 |
| endpoint | ServiceNow 服务器的终结点 (`http://<instance>.service-now.com`)。  | 是 |
| authenticationType | 可使用的身份验证类型。 <br/>允许值包括：**Basic** **OAuth2** | 是 |
| username | 用户名用于连接到 ServiceNow 服务器，进行基本和 OAuth2 身份验证。  | 是 |
| password | 基本和 OAuth2 身份验证的用户名所对应的密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 是 |
| clientId | OAuth2 身份验证的客户端 ID。  | 否 |
| clientSecret | OAuth2 身份验证的客户端密码。 将此字段标记为 SecureString 以安全地将其存储在数据工厂中或[引用存储在 Azure Key Vault 中的机密](store-credentials-in-key-vault.md)。 | 否 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 加密数据源终结点。 默认值为 true。  | 否 |
| useHostVerification | 指定通过 SSL 连接时是否需要服务器证书中的主机名匹配服务器的主机名。 默认值为 true。  | 否 |
| usePeerVerification | 指定通过 SSL 连接时是否要验证服务器的标识。 默认值为 true。  | 否 |

**示例：**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各部分和属性的完整列表，请参阅[数据集](concepts-datasets-linked-services.md)一文。 本部分提供 ServiceNow 数据集支持的属性列表。

要从 ServiceNow 复制数据，请将数据集的 type 属性设置为“ServiceNowObject”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**ServiceNowObject** | 是 |
| tableName | 表名称。 | 否（如果指定了活动源中的“query”） |

**示例**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 ServiceNow 源支持的属性列表。

### <a name="servicenow-as-source"></a>以 ServiceNow 作为源

要从 ServiceNow 复制数据，请将复制活动中的源类型设置为“ServiceNowSource”。 复制活动**source**部分支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 复制活动源的 type 属性必须设置为：**ServiceNowSource** | 是 |
| query | 使用自定义 SQL 查询读取数据。 例如：`"SELECT * FROM Actual.alm_asset"`。 | 否（如果指定了数据集中的“tableName”） |

在查询中指定 ServiceNow 的架构和列时注意以下内容，并且参阅有关复制性能隐含的[性能提示](#performance-tips)。

- **架构：** 在 ServiceNow 查询中将架构指定为 `Actual` 或 `Display`，从而在调用 [ServiceNow restful API](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET) 时可以将其视为参数 `sysparm_display_value`，其值为 true 或 false。 
- **列：** `Actual` 架构下实际值的列名是 `[column name]_value`，而 `Display` 架构下显示值的列名为 `[column name]_display_value`。 请注意，列名需要映射到查询中所使用的架构。

**示例查询：** 
`SELECT col_value FROM Actual.alm_asset`或  
`SELECT col_display_value FROM Display.alm_asset`

**示例：**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>性能提示

### <a name="schema-to-use"></a>要使用的架构

ServiceNow 有 2 个不同的架构，一个是“实际”，返回实际数据；另一个是“显示”，返回数据的显示值。 

如果在有查询中筛选器，请使用“实际”架构，此架构具有更好的复制性能。 在针对“实际”架构进行查询时，ServiceNow 在提取数据时将本机支持筛选器以仅返回筛选的结果集，然而在针对“显示”架构进行查询时，ADF 将检索所有数据并在内部应用筛选器。

### <a name="index"></a>索引

ServiceNow 表索引有助于提高查询性能，请参阅[创建表索引](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html)。

## <a name="lookup-activity-properties"></a>查找活动属性

若要了解有关属性的详细信息，请检查[查找活动](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。
