---
title: Azure Cosmos DB 表 API .NET SDK 和资源
description: 了解有关 Azure Cosmos DB 表 API 的全部信息，包括发布日期、停用日期和各版本之间进行的更改。
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: 5e98c40384207c77b4ea7e9557a7d1ebebd95e47
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058588"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB 表 .NET API：下载和发行说明

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET 标准](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 下载**|[NuGet](https://aka.ms/acdbtablenuget)|
|**快速入门**|[Azure Cosmos DB：使用 .NET 和表 API 生成应用](create-table-dotnet.md)|
|**教程**|[Azure Cosmos DB：在 .NET 中使用表 API 进行开发](tutorial-develop-table-dotnet.md)|
|**当前受支持的框架**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) 目前处于维护模式，不久将被弃用。 请升级到新的 .NET Standard 库 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) 来继续获取表 API 支持的最新功能。

> 如果已在预览期间创建表 API 帐户，请[新建表 API 帐户](create-table-dotnet.md#create-a-database-account)，这样才能使用正式版表 API SDK。
>

## <a name="release-notes"></a>发行说明

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Bug 修复

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Bug 修复

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* 添加了多区域写入支持
* 修复了 NuGet 包对 Microsoft.Azure.DocumentDB、Microsoft.OData.Core、Microsoft.OData.Edm、Microsoft.Spatial 的依赖性

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* 修复了 NuGet 包对 Microsoft.Azure.Storage.Common 和 Microsoft.Azure.DocumentDB 的依赖关系。
* 修复了配置 JsonConvert.DefaultSettings 时表序列化的 Bug。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 针对直接模式下格式不正确的 ETAG 添加了验证。
* 修复了网管模式下的 LINQ 查询 Bug。
* 同步 API 现于 SynchronizationContext 的线程池上运行。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* 将 TableQueryMaxItemCount、TableQueryEnableScan、TableQueryMaxDegreeOfParallelism 和 TableQueryContinuationTokenLimitInKb 添加到 TableRequestOptions
* Bug 修复

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* 正式发布版

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview

* 初始预览版

## <a name="release-and-retirement-dates"></a>发布日期和停用日期

Microsoft 至少会在停用 SDK 前提前 12 个月发出通知，以便顺利转换为更高版本/受支持版本。

`Microsoft.Azure.CosmosDB.Table`该库目前仅可用于 .NET Framework，并处于维护模式，即将弃用。 新特性和功能以及优化仅添加到 .NET Standard 库[Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)中, 因此建议升级到 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)。

[Windowsazure.storage-windowsazure.storage-premiumtable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview)预览版包已弃用。 WindowsAzure.Storage-PremiumTable SDK 将在 2018 年 11 月 15 日停用，到时将不允许向已停用的 SDK 发出请求。 

使用已停用的 SDK 对 Azure Cosmos DB 发出的任何请求都会遭服务拒绝。
<br/>

| Version | 发布日期 | 停用日期 |
| --- | --- | --- |
| [2.1.2](#2.1.2) |2019年9月16日| |
| [2.1.0](#2.1.0) |2019 年 1 月 22 日|2020年4月01日 |
| [2.0.0](#2.0.0) |2018 年 9 月 26 日|2020年3月01日 |
| [1.1.3](#1.1.3) |2018 年 7 月 17 日|2019年12月01日 |
| [1.1.1](#1.1.1) |2018 年 3 月 26 日|2019年12月01日 |
| [1.1.0](#1.1.0) |2018 年 2 月 21 日|2019年12月01日 |
| [1.0.0](#1.0.0) |2017 年 11 月 15 日|2019年11月15日 |
| 0.9.0-preview |2017 年 11 月 11 日 |2019年11月11日 |

## <a name="troubleshooting"></a>疑难解答

如果在尝试使用 Microsoft.Azure.CosmosDB.Table NuGet 包时看到以下错误： 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

可以使用下列两种方法之一解决此问题：

* 使用包管理器控制台安装 Microsoft.Azure.CosmosDB.Table 包及其依赖项。 为此，请在解决方案的包管理器控制台中键入以下代码。 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* 使用首选 NuGet 包管理工具先安装 Microsoft.Azure.Storage.Common NuGet 包，再安装 Microsoft.Azure.CosmosDB.Table。

## <a name="faq"></a>常见问题

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>请参阅

若要了解有关 Azure Cosmos DB 表 API 的详细信息，请参阅 [Azure Cosmos DB 表 API 简介](table-introduction.md)。 
