---
title: 从 ODBC 数据存储移动数据 |Microsoft Docs
description: 了解如何使用 Azure 数据工厂从 ODBC 数据存储移动数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 885fb18e6f582caba2e90bbe3f535b9c763aff85
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839344"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>使用 Azure 数据工厂从 ODBC 数据存储移动数据
> [!div class="op_single_selector" title1="选择所使用的数据工厂服务版本："]
> * [版本 1](data-factory-odbc-connector.md)
> * [版本 2（当前版本）](../connector-odbc.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果正在使用当前版本数据工厂服务，请参阅 [V2 中的 ODBC 连接器](../connector-odbc.md)。


本文介绍如何使用 Azure 数据工厂中的复制活动从本地 ODBC 数据存储移动数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。

可以将数据从 ODBC 数据存储复制到任何支持的接收器数据存储。 有关复制活动支持作为接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 数据工厂当前仅支持将数据从 ODBC 数据存储移至其他数据存储，而不支持将数据从其他数据存储移至 ODBC 数据存储。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>启用连接
数据工厂服务支持通过数据管理网关连接到本地 ODBC 源。 请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解数据管理网关和设置网关的分步说明。 使用网关连接到 ODBC 数据存储，即使它由 Azure IaaS VM 托管。

可将网关作为 ODBC 数据存储安装在同一本地计算机或 Azure VM 上。 但是，建议将网关安装在单独的计算机或 Azure IaaS VM 上，以避免资源争用，从而提高性能。 在单独的计算机上安装网关时，该计算机应能访问具有 ODBC 数据存储的计算机。

除了数据管理网关，还需要在网关计算机上安装数据存储的 ODBC 驱动程序。

> [!NOTE]
> 请参阅[网关问题故障排除](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)，了解连接/网关相关问题的故障排除提示。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从 ODBC 数据存储移动数据。

创建管道的最简单方法是使用  复制向导。 有关分步说明，请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

还可以使用以下工具来创建管道：**Visual Studio**， **Azure PowerShell**， **Azure Resource Manager 模板**， **.NET API**，并且**REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建链接服务可将输入和输出数据存储链接到数据工厂  。
2. 创建数据集以表示复制操作的输入和输出数据  。
3. 创建包含复制活动的管道，该活动将一个数据集作为输入，将一个数据集作为输出  。

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。 有关用于从 ODBC 数据存储复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从 ODBC 数据存储复制到 Azure Blob](#json-example-copy-data-from-odbc-data-store-to-azure-blob) 部分。

对于特定于 ODBC 数据存储的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性
下表提供特定于 ODBC 链接服务的 JSON 元素的描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |type 属性必须设置为：**OnPremisesOdbc** |是 |
| connectionString |连接字符串的非访问凭据部分和可选的加密凭据。 请参阅以下部分中的示例。 <br/><br/>可以使用类似 `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` 的模式指定连接字符串，也可以利用在网关计算机上使用 `"DSN=<name of the DSN>;"` 设置的系统 DSN（数据源名称）（仍需要相应地指定链接服务中的凭据部分）。 |是 |
| credential |连接字符串的访问凭据部分，采用特定于驱动程序的属性值格式指定。 示例：`"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`。 |否 |
| authenticationType |用于连接 ODBC 数据存储的身份验证类型。 可能的值包括：Anonymous 和 Basic。 |是 |
| userName |如果使用基本身份验证，指定的用户名。 |否 |
| password |指定为 userName 指定的用户帐户的密码。 |否 |
| gatewayName |数据工厂服务应用于连接到 ODBC 数据存储的网关的名称。 |是 |

### <a name="using-basic-authentication"></a>使用基本身份验证

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>使用包含加密凭据的基本身份验证
可以对使用的凭据进行加密[新建 AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) （Azure PowerShell 1.0 版） cmdlet 或[New-azuredatafactoryencryptvalue](https://msdn.microsoft.com/library/dn834940.aspx) （0.9 版或更早版本的 AzurePowerShell)。

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>使用匿名身份验证

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 **TypeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **RelationalTable** 类型数据集（包括 ODBC 数据集）的 typeProperties 部分具有以下属性

| 属性 | 说明 | 需要 |
| --- | --- | --- |
| tableName |ODBC 数据存储中表的名称。 |是 |

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

在复制活动中，当源属于 **RelationalSource** 类型（包括 ODBC）时，以下属性可用于 typeProperties 部分：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| query |使用自定义查询读取数据。 |SQL 查询字符串。 例如：从 MyTable 中选择 *。 |是 |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON 示例：将数据从 ODBC 数据存储复制到 Azure Blob
此示例提供 JSON 定义，可用于通过使用创建的管道[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)。 它演示了如何将数据从 ODBC 源复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

此示例具有以下数据工厂实体：

1. [OnPremisesOdbc](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [RelationalTable](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. [管道](data-factory-create-pipelines.md)，具有使用 [RelationalSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) 的复制活动。

此示例每隔一小时将数据从 ODBC 数据存储中的查询结果复制到 blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

第一步，设置数据管理网关。 有关说明，请参考[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文。

**ODBC 链接服务**此示例使用基本身份验证。 请参阅 [ODBC 链接服务](#linked-service-properties)部分，了解可用的各种类型的身份验证。

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure 存储链接服务**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**ODBC 输入数据集**

该示例假定已在 ODBC 数据库中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestampcolumn”的列。

设置“external”: ”true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure Blob 输出数据集**

数据每小时向新的 blob 写入一次（frequency：hour，interval：1）。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**管道中使用 ODBC 源 (RelationalSource) 和 Blob 接收器 (BlobSink) 的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **RelationalSource**，将 **sink** 类型设置为 **BlobSink**。 为 **query** 属性指定的 SQL 查询选择复制过去一小时的数据。

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>ODBC 的类型映射
如[数据移动活动](data-factory-data-movement-activities.md)一文所述，复制活动使用以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型
2. 从 .NET 类型转换为本机接收器类型

从 ODBC 数据存储移动数据时，ODBC 数据类型映射到 .NET 类型，如 [ODBC 数据类型映射](https://msdn.microsoft.com/library/cc668763.aspx)主题所述。

## <a name="map-source-to-sink-columns"></a>将源映射到接收器列
要了解如何将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="repeatable-read-from-relational-sources"></a>从关系源进行可重复读取
从关系数据源复制数据时，请注意可重复性，以免发生意外结果。 在 Azure 数据工厂中，可手动重新运行切片。 还可以为数据集配置重试策略，以便在出现故障时重新运行切片。 无论以哪种方式重新运行切片，都需要确保读取相同的数据，而与运行切片的次数无关。 请参阅[从关系源进行可重复读取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)。

## <a name="troubleshoot-connectivity-issues"></a>解决连接问题
若要解决连接问题，请使用“数据管理网关配置管理器”  的“诊断”  选项卡。

1. 启动“数据管理网关配置管理器”  。 可以直接运行 "C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe"（或）搜索“网关”  以查找“Microsoft 数据管理网关”  应用程序的链接，如下图所示。

    ![搜索网关](./media/data-factory-odbc-connector/search-gateway.png)
2. 切换到“诊断”  选项卡。

    ![网关诊断](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. 选择数据存储（链接服务）的“类型”  。
4. 指定“身份验证”  并输入“凭据”  （或）输入用于连接数据存储的“连接字符串”  。
5. 单击“测试连接”  以测试数据存储的连接。

## <a name="performance-and-tuning"></a>性能和优化
若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。
