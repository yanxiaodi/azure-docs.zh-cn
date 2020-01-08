---
title: 从 Azure Application Insights 导出到 SQL | Microsoft Docs
description: 使用流分析将 Application Insights 数据连续导出到 SQL。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mbullwin
ms.openlocfilehash: eecd2a50607fa42562a9ae6a7fb950a253655a45
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65872708"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>演练：使用流分析从 Application Insights 导出到 SQL
本文说明如何使用[连续导出][export]和 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)，将遥测数据从 [Azure Application Insights][start] 移入 Azure SQL 数据库。 

连续导出以 JSON 格式将遥测数据移入 Azure 存储。 我们将使用 Azure 流分析来分析 JSON 对象，并在数据库表中创建行。

（一般而言，连续导出是对应用发送到 Application Insights 的遥测数据自行进行分析的方式。 可以改写此代码示例，以使用导出的遥测数据执行其他操作，例如聚合数据）。

首先，假设读者已有一个想要监视的应用。

本示例将使用页面视图数据，但可以轻松地将此模式沿用到其他数据类型，例如自定义事件和异常。 

## <a name="add-application-insights-to-your-application"></a>将 Application Insights 添加到应用程序
开始操作：

1. [为网页设置 Application Insights](../../azure-monitor/app/javascript.md)。 
   
    （本示例侧重于处理来自客户端浏览器的页面视图数据，但你也可以针对 [Java](../../azure-monitor/app/java-get-started.md) 或 [ASP.NET](../../azure-monitor/app/asp-net.md) 应用的服务器端设置 Application Insights，并处理请求、依赖项和其他服务器遥测数据。）
2. 发布应用，并观察 Application Insights 资源中出现的遥测数据。

## <a name="create-storage-in-azure"></a>在 Azure 中创建存储
连续导出始终将数据输出到 Azure 存储帐户，因此首先需要创建存储。

1. 在 [Azure 门户][portal]上的订阅中创建存储帐户。
   
    ![在 Azure 门户中，依次选择“添加”、“数据”、“存储”。 选择“经典”，并选择“创建”。 为存储提供一个名称。](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. 创建容器
   
    ![在新存储中选择“容器”，单击“容器”磁贴，并单击“添加”](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. 复制存储访问密钥
   
    稍后需要使用它来设置流分析服务的输入。
   
    ![在存储中，依次打开“设置”、“密钥”，并复制主访问密钥](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>开始向 Azure 存储连续导出
1. 在 Azure 门户中，浏览到为应用程序创建的 Application Insights 资源。
   
    ![依次选择“浏览”、“Application Insights”、应用程序](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. 创建连续导出。
   
    ![依次选择“设置”、“连续导出”、“添加”](./media/code-sample-export-sql-stream-analytics/070-export.png)

    选择前面创建的存储帐户：

    ![设置导出目标](./media/code-sample-export-sql-stream-analytics/080-add.png)

    设置想要查看的事件类型：

    ![选择事件类型](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. 让我们累积一些数据。 请休息一下，让其他人先使用该应用程序一段时间。 应用程序中会逐渐传入遥测数据，[指标资源管理器](../../azure-monitor/app/metrics-explorer.md)中会显示统计图表，[诊断搜索](../../azure-monitor/app/diagnostic-search.md)中会显示各个事件。 
   
    此外，数据将导出到存储。 
2. 在门户中检查导出的数据 - 选择“浏览”，选择存储帐户，然后选择“容器”；也可以在 Visual Studio 中检查。   在 Visual Studio 中，请选择“查看”>“Cloud Explorer”，并打开“Azure”>“存储”。  （如果没有此菜单选项，则需要安装 Azure SDK：打开“新建项目”对话框，打开 Visual C# /云/获取用于 .NET 的 Microsoft Azure SDK。）
   
    ![在 Visual Studio 中，依次打开“Server Browser”、“Azure”、“存储”](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    记下派生自应用程序名称和检测密钥的路径名称的共同部分。 

事件以 JSON 格式写入 Blob 文件。 每个文件可能包含一个或多个事件。 因此我们想要读取事件数据，并筛选出所需的字段。 可以针对数据执行各种操作，但我们目前的计划是使用流分析将数据移到 SQL 数据库。 这样做可以轻松运行许多微妙的查询。

## <a name="create-an-azure-sql-database"></a>创建 Azure SQL 数据库
再次在 [Azure 门户][portal]中打开订阅，创建要在其中写入数据的数据库（除非已有新服务器，否则还要创建新服务器）。

![依次选择“新建”、“数据”、“SQL”](./media/code-sample-export-sql-stream-analytics/090-sql.png)

确保数据库服务器允许访问 Azure 服务：

![依次选择“浏览”、“服务器”、服务器、“设置”、“防火墙”、“允许访问 Azure”](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>在 Azure SQL 数据库中创建表
使用偏好的管理工具连接到在上一部分中创建的数据库。 本演练将使用 [SQL Server 管理工具](https://msdn.microsoft.com/ms174173.aspx) (SSMS)。

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

创建新查询，并执行以下 T-SQL：

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

本示例使用页面视图中的数据。 若要查看其他可用的数据，请检查 JSON 输出，并查看[导出数据模型](../../azure-monitor/app/export-data-model.md)。

## <a name="create-an-azure-stream-analytics-instance"></a>创建 Azure 流分析实例
在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 流分析服务，并创建新的流分析作业：

![流分析设置](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

创建新作业后，选择“转到资源”  。

![流分析设置](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>添加新输入

![流分析设置](./media/code-sample-export-sql-stream-analytics/SA004.png)

将此位置设置为从连续导出 Blob 接收输入：

![流分析设置](./media/code-sample-export-sql-stream-analytics/SA0005.png)

现在需要使用存储帐户的主访问密钥（前面已记下此密钥）。 将此密钥设置为存储帐户密钥。

#### <a name="set-path-prefix-pattern"></a>设置路径前缀模式

**请务必将“日期格式”设置为 YYYY-MM-DD（包含短划线）。**

“路径前缀模式”指定流分析在存储中查找输入文件的方式。 需要将它设置为与连续导出存储数据的方式相对应。 设置如下：

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

在本示例中：

* `webapplication27` 是 Application Insights 资源的名称，**全部小写**。 
* `1234...` 是 Application Insights 资源的检测密钥，但**删除了短划线**。 
* `PageViews` 是要分析的数据类型。 可用的类型取决于在连续导出中设置的筛选器。 检查导出的数据以查看其他可用类型，并查看[导出数据模型](../../azure-monitor/app/export-data-model.md)。
* `/{date}/{time}` 是以文本形式写入的模式。

若要获取 Application Insights 资源的名称和 iKey，请在资源的概述页中打开“概要”，或打开“设置”。

> [!TIP]
> 使用示例函数检查是否已正确设置输入路径。 如果检查失败：请检查在所选的示例时间范围内，存储中是否有数据。 编辑输入定义，检查是否已正确设置存储帐户、路径前缀和日期格式。

 
## <a name="set-query"></a>设置查询
打开查询部分：

将默认查询替换为以下内容：

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

请注意，前几个属性是特定于页面视图数据的属性。 其他遥测数据类型的导出具有不同的属性。 请参阅[属性类型和值的详细数据模型参考。](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>设置数据库的输出
选择“SQL”作为输出。

![在流分析中选择“输出”](./media/code-sample-export-sql-stream-analytics/SA006.png)

指定 SQL 数据库。

![填写数据库的详细信息](./media/code-sample-export-sql-stream-analytics/SA007.png)

关闭向导，等待通知输出已设置。

## <a name="start-processing"></a>开始处理
从操作栏启动作业：

![在流分析中单击“开始”](./media/code-sample-export-sql-stream-analytics/SA008.png)

可以选择是要处理从当前时间开始传入的数据，还是处理以前的数据。 如果连续导出已运行了一段时间，则后一种做法将很有效果。

几分钟后，可返回 SQL Server 管理工具监视流入的数据。 例如，使用如下所示的查询：

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>相关文章
* [使用流分析导出到 PowerBI](../../azure-monitor/app/export-power-bi.md )
* [属性类型和值的详细数据模型参考。](../../azure-monitor/app/export-data-model.md)
* [Application Insights 中的连续导出](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

