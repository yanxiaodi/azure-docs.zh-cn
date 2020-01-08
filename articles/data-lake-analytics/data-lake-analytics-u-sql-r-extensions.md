---
title: 在 Azure Data Lake Analytics 中使用 R 扩展 U-SQL 脚本
description: 了解如何使用 Azure Data Lake Analytics 在 U SQL 脚本中运行 R 代码。 嵌入 R 代码内联或从文件引用。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672695"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>在 Azure Data Lake Analytics 中使用 R 代码扩展 U-SQL 脚本

以下示例演示了用于部署 R 代码的基本步骤：
* 使用 `REFERENCE ASSEMBLY` 语句为 U-SQL 脚本启用 R 扩展。
* 使用 `REDUCE` 操作对某个键的输入数据进行分区。
* U-SQL 的 R 扩展包括内置化简器 (`Extension.R.Reducer`)，可在分配给化简器的每个顶点上运行 R 代码。 
* 使用名为 `inputFromUSQL` 的专用命名数据帧和第 1 @no__t 的专用命名数据帧，在数据帧和 R 之间传递数据。输入和输出的标识符名称是固定的（即，用户无法更改输入和输出数据帧标识符的预定义名称）。

## <a name="embedding-r-code-in-the-u-sql-script"></a>在 U-SQL 脚本中嵌入 R 代码

可以使用 `Extension.R.Reducer` 的命令参数在 U-SQL 脚本中内嵌 R 代码。 例如，可以将 R 脚本声明为一个字符串变量，然后将其作为参数传递给 Reducer。


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>将 R 代码保存在一个单独的文件中，然后在 U-SQL 脚本中引用该文件

以下示例展示了更复杂的用法。 在本例中，R 代码部署为是一个 U-SQL 脚本的 RESOURCE。

将此 R 代码保存为一个单独的文件。

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

使用一个 U-SQL 脚本通过 DEPLOY RESOURCE 语句部署该 R 脚本。

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>R 如何与 U-SQL 集成

### <a name="datatypes"></a>数据类型
* U-SQL 中的字符串和数字列按原样在 R 数据框和 U-SQL 之间转换 [支持的类型：`double`、`string`、`bool`、`integer``byte`]。
* U-SQL 不支持 `Factor` 数据类型。
* `byte[]` 必须序列化为 base64 编码的 `string`。
* U-SQL 创建 R 输入数据框或设置化简器参数 `stringsAsFactors: true`后，U-SQL 字符串可以转换为 R 代码中的因素。

### <a name="schemas"></a>架构
* U-SQL 数据集不能有重复的列名称。
* U-SQL 数据集列名称必须是字符串。
* U-SQL 和 R 脚本中的列名称必须相同。
* 只读列不能是输出数据框的一部分。 因为如果只读列是 UDO 输出架构的一部分，这些列会自动重新注入到 U-SQL 表中。

### <a name="functional-limitations"></a>功能限制
* 在同一进程中，R 引擎不能实例化两次。 
* 当前，U-SQL 不支持将 Combiner UDO 用于使用通过 Reducer UDO 生成的分区模型进行预测。 用户可以将分区模型声明为资源，然后在其 R 脚本中使用它们（请参阅示例代码 `ExtR_PredictUsingLMRawStringReducer.usql`）

### <a name="r-versions"></a>R 版本
仅支持 R 3.2.2。

### <a name="standard-r-modules"></a>标准 R 模块

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>输入和输出大小限制
分配给每个顶点的内存量受限。 因为输入和输出 DataFrames 必须存在于 R 代码的内存中，因此输入和输出的总大小不能超过 500 MB。

### <a name="sample-code"></a>代码示例
在安装 U-SQL Advanced Analytics 扩展后，Data Lake Store 帐户中会提供更多的示例代码。 更多示例代码的路径为：`<your_account_address>/usqlext/samples/R`。 

## <a name="deploying-custom-r-modules-with-u-sql"></a>使用 U-SQL 部署自定义 R 模块

首先，创建一个 R 自定义模块并将其压缩为 zip 文件，然后将压缩后的 R 自定义模块文件上传到 ADL 存储。 在示例中，我们将 magittr_1.5.zip 上传到我们使用的 ADLA 帐户的默认 ADLS 帐户的根目录中。 将模块上传到 ADL 存储后，对其进行声明并使用 DEPLOY RESOURCE 使其在 U-SQL 脚本中可用，然后调用 `install.packages` 来安装它。

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>后续步骤
* [Microsoft Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
* [对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数](data-lake-analytics-use-window-functions.md)
