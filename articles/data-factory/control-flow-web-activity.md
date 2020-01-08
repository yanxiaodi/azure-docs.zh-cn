---
title: Azure 数据工厂中的 Web 活动 | Microsoft Docs
description: 了解如何使用 Web 活动（数据工厂支持的控制流活动之一）从管道调用 REST 终结点。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 73770e559af8a999c17fff5ea1aa6ee53ac17e83
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141587"
---
# <a name="web-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Web 活动
Web 活动可用于从数据工厂管道调用自定义的 REST 终结点。 可以传递数据集和链接服务以供活动使用和访问。

> [!NOTE]
> Web 活动只能调用公开显示的 Url。 专用虚拟网络中承载的 Url 不支持此方法。

## <a name="syntax"></a>语法

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必填
-------- | ----------- | -------------- | --------
name | Web 活动的名称 | String | 是
type | 必须设置为 **WebActivity**。 | String | 是
method | 目标终结点的 Rest API 方法。 | 字符串。 <br/><br/>支持的类型：“GET”、“POST”、“PUT” | 是
url | 目标终结点和路径 | 字符串（或带有 resultType 字符串的表达式）。 如果活动在 1 分钟内未收到终结点的响应，则会超时并显示错误。 | 是
headers | 发送到请求的标头。 例如，若要在请求中设置语言和类型：`"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`。 | 字符串（或带有 resultType 字符串的表达式） | 是，需要内容类型标头。 `"headers":{ "Content-Type":"application/json"}`
正文 | 表示要发送到终结点的有效负载。  | 字符串（或带有 resultType 字符串的表达式）。 <br/><br/>请参阅[请求有效负载架构](#request-payload-schema)部分中的请求有效负载架构。 | POST/PUT 方法所必需。
身份验证 | 用于调用该终结点的身份验证方法。 支持的类型为“Basic”或“ClientCertificate”。 有关详细信息，请参阅[身份验证](#authentication)部分。 如果不需要身份验证，则排除此属性。 | 字符串（或带有 resultType 字符串的表达式） | 否
datasets | 传递给终结点的数据集列表。 | 数据集引用数组。 可以是空数组。 | 是
linkedServices | 传递给终结点的链接服务列表。 | 链接服务引用数组。 可以是空数组。 | 是

> [!NOTE]
> Web 活动调用的 REST 终结点必须返回 JSON 类型的响应。 如果活动在 1 分钟内未收到终结点的响应，则会超时并显示错误。

下表显示了 JSON 上下文的要求：

| 值类型 | 请求正文 | 响应正文 |
|---|---|---|
|JSON 对象 | 支持 | 支持 |
|JSON 阵列 | 支持 <br/>（目前，JSON 数组不作为错误结果使用。 正在运行修补程序。） | 不支持 |
| JSON 值 | 支持 | 不支持 |
| 非 JSON 类型 | 不支持 | 不支持 |
||||

## <a name="authentication"></a>身份验证

### <a name="none"></a>无
如果不需要身份验证，请排除“身份验证”属性。

### <a name="basic"></a>基本
指定用户名和密码以用于基本身份验证。

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>客户端证书
指定 base64 编码的 PFX 文件内容和密码。

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>托管标识

使用数据工厂的托管标识指定要为其请求访问令牌的资源 URI。 若要调用 Azure 资源管理 API，请使用 `https://management.azure.com/`。 有关如何托管标识工作原理的详细信息，请参阅 [Azure 资源概述页面的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

## <a name="request-payload-schema"></a>请求有效负载架构
当使用 POST/PUT 方法时，正文属性表示发送到终结点的有效负载。 可以将链接服务和数据集作为有效负载的一部分进行传递。 下面是有效负载的架构：

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>示例
在此示例中，管道中的 Web 活动调用了 REST 终结点。 它将 Azure SQL 链接服务和 Azure SQL 数据集传递到该终结点。 REST 终结点使用 Azure SQL 连接字符串连接到 Azure SQL Server，并返回 SQL Server 实例的名称。

### <a name="pipeline-definition"></a>管道定义

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>管道参数值

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Web 服务终结点代码

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动：

- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
