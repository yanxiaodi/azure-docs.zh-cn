---
title: Azure Data Lake Analytics U-SQL 语言入门
description: 了解有关 Azure Data Lake Analytics 中 U-SQL 语言的基础知识。 使用变量编写你的第一个查询、转换行集和聚合数据。
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 8130679dcc519cecd25abf43902c003ad8047df3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672820"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics U-SQL 入门
U-SQL 是一种将声明性 SQL 与命令性 C# 相结合的语言，能够处理任何规模的数据。 通过 U-SQL 的可缩放分布式查询功能，可以跨关系存储（如 Azure SQL 数据库）高效分析其中的数据。 使用 U-SQL，可以通过在读取和插入自定义逻辑和 UDF 时应用架构来处理非结构化数据。 此外，U-SQL 还提供可扩展性，可更精细地控制大规模执行的方式。 

## <a name="learning-resources"></a>学习资源

* [U-SQL 教程](https://aka.ms/usqltutorial)提供大部分 U-SQL 语言的指导性演练。 对于要了解 U-SQL 的所有开发人员，建议阅读本文档。
* 有关 **U-SQL 语言语法**的详细信息，请参阅 [U-SQL 语言参考](https://docs.microsoft.com/u-sql/)。
* 若要了解 **U-SQL 设计理念**，请参阅 Visual Studio 博客文章 [U-SQL（使大数据处理更轻松的语言）简介](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)。

## <a name="prerequisites"></a>先决条件

在完成本文档中的 U-SQL 示例之前，请阅读并完成 [Tutorial：使用适用于 Visual Studio @ no__t 的 Data Lake 工具开发 SQL 脚本。 该教程介绍了将 U-SQL 与针对 Visual Studio 的 Azure Data Lake 工具结合使用的机制。

## <a name="your-first-u-sql-script"></a>第一个 U-SQL 脚本

下面的 U-SQL 脚本十分简单，可供我们探讨 U-SQL 语言的许多方面。

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

此脚本没有任何转换步骤。 它从名为 `SearchLog.tsv` 的源文件进行读取，将其架构化，并将行集写回到名为 SearchLog-first-u-sql.csv 的文件。

请注意 `Duration` 字段中数据类型旁边的问号。 它表示 `Duration` 字段可以为 null。

### <a name="key-concepts"></a>关键概念
* **行集变量**：可以将生成行集的每个查询表达式分配给一个变量。 在脚本中，U-SQL 遵循 T-SQL 变量命名模式（例如 `@searchlog`）。
* **EXTRACT** 关键字从文件中读取数据，并在读取时定义架构。 `Extractors.Tsv` 是用于制表符分隔值文件的内置 U-SQL 提取程序。 可以开发自定义提程序。
* **OUTPUT** 将行集中的数据写入到文件。 `Outputters.Csv()` 是用于创建逗号分隔值文件的内置 U-SQL 输出器。 可以开发自定义输出器。

### <a name="file-paths"></a>文件路径

EXTRACT 和 OUTPUT 语句使用文件路径。 文件路径可以是绝对路径，也可以是相对路径：

以下此绝对文件路径引用名为 `mystore` 的 Data Lake Store 中的文件：

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

以下此文件路径以 `"/"` 开头。 它引用默认 Data Lake Store 帐户中的文件：

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>使用标量变量

也可以使用标量变量简化脚本维护工作。 前面的 U-SQL 脚本也可以按如下方式编写：

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>转换行集

使用 **SELECT** 转换行集：

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

WHERE 子句使用 [C# 布尔表达式](/dotnet/csharp/language-reference/operators/index)。 可以使用 C# 表达式语言执行自己的表达式和函数。 将这些表达式和函数与逻辑“与 (And)”和“或 (Or)”结合，甚至可以执行更复杂的筛选操作。

以下脚本使用 DateTime.Parse() 方法和一个“与”。

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >第二个查询基于第一个行集结果执行操作，这会创建两个筛选器的组合。 还可以重复使用变量名，将从词法上审视这些名称。

## <a name="aggregate-rowsets"></a>聚合行集
U-SQL 提供了熟悉的 ORDER BY、GROUP BY 和聚合。

以下查询查找每个区域的总持续时间，并按顺序显示前五个持续时间。

U-SQL 行集不为下一个查询保留其顺序。 因此，若要对输出进行排序，需要向 OUTPUT 语句添加 ORDER BY：

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

在 SELECT 表达式中，U-SQL ORDER BY 子句需要使用 FETCH 子句。

可使用 U-SQL HAVING 子句将输出限制为满足 HAVING 条件的组：

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

有关高级聚合方案，请参阅有关[聚合、分析和引用函数](/u-sql/built-in-functions)的 U-SQL 参考文档

## <a name="next-steps"></a>后续步骤
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [使用针对 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
