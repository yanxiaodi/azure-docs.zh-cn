---
title: 针对 1 TB 数据集使用 Azure HDInsight Hadoop 群集 - Team Data Science Process
description: 对于采用 HDInsight Hadoop 群集的端到端方案，使用 Team Data Science Process 来构建和部署使用大型 (1 TB) 公开可用数据集的模型
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ac627907e3f595ef59edc606f34fd27353e4c577
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60305589"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Team Data Science Process 的工作原理 - 针对 1 TB 数据集使用 Azure HDInsight Hadoop 群集

本演练演示如何利用 [Azure HDInsight Hadoop 群集](https://azure.microsoft.com/services/hdinsight/)在端到端方案中使用 Team Data Science Process 来存储、浏览并描绘工程师数据特征，从其中一个公用 [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) 数据集下载示例数据。 其中使用了 Azure 机器学习在这些数据上构建二进制分类模型。 此外，还演示如何将这些模型之一发布为 Web 服务。

也可以使用 IPython Notebook 来完成本演练中介绍的任务。 想要尝试此方法的用户应该咨询[使用 Hive ODBC 连接的 Criteo 演练](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)主题。

## <a name="dataset"></a>Criteo 数据集说明
Criteo 数据是一个单击预测数据集，包含约 370 GB 的 gzip 压缩 TSV 文件（约 1.3 TB 未压缩），包含超过 43 亿条记录。 它取自 [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) 提供的 24 天的单击数据。 为了方便数据科学家，我们已解压缩数据以便用于试验。

此数据集中的每个记录包含 40 列：

* 第一列是标签列，该列指示用户是否单击“添加”  （值 1）或未单击（值 0）
* 接下来的 13 列是数值列，并且
* 最后的 26 列是分类列

这些列是匿名的，并且使用一系列枚举的名称：“Col1”（表示标签列）到“Col40”（表示最后一个分类列）。

以下是此数据集中两个观测（行）的前 20 列的摘要：

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

在此数据集的数值列和分类列中都有缺失值。 本文介绍一种处理缺失值的简单方法。 将缺失值存储到 Hive 表中时，将浏览数据的其他详细信息。

**定义**:*点击率(CTR)：* 这是数据的点击数的百分比。 在此 Criteo 数据集中，CTR 约为 3.3% 或 0.033。

## <a name="mltasks"></a>预测任务示例
本演练中涉及两个示例预测问题：

1. **二元分类**：预测用户是否单击了添加：

   * 分类 0：无点击
   * 分类 1：单击
2. **回归**：预测来自用户功能的广告点击概率。

## <a name="setup"></a>为数据科学设置 HDInsight Hadoop 群集
**注意：** 这通常是**管理员**任务。

通过三个步骤设置 Azure Data Science 环境，以构建具有 HDInsight 群集的预测分析解决方案：

1. [创建存储帐户](../../storage/common/storage-quickstart-create-account.md)：此存储帐户用于在 Azure Blob 存储中存储数据。 HDInsight 群集中使用的数据存储在此处。
2. [自定义用于数据科学的 Azure HDInsight Hadoop 群集](customize-hadoop-cluster.md)：此步骤将创建一个在所有节点上都安装有 64 位 Anaconda Python 2.7 的 Azure HDInsight Hadoop 群集。 自定义 HDInsight 群集时，要完成两个重要步骤（本主题中有所描述）。

   * 必须在创建 HDInsight 群集时将其与在步骤 1 中创建的存储帐户相链接。 此存储帐户用于访问可在群集中处理的数据。
   * 必须在创建群集的头节点后启用远程访问。 记住在此处指定的远程访问凭据（与创建时为群集指定的远程访问凭据不同）：需要这些凭据才能完成以下过程。
3. [创建 Azure 机器学习工作室工作区](../studio/create-workspace.md)：此 Azure 机器学习工作区用于在 HDInsight 群集上进行初始数据浏览和缩小取样后构建机器学习模型。

## <a name="getdata"></a>从公共源获取和使用数据
可以通过单击链接、接受使用条款并提供名称来访问 [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) 数据集。 此处显示的内容的快照如下：

![接受 Criteo 条款](./media/hive-criteo-walkthrough/hLxfI2E.png)

单击“继续下载”  ，详细了解数据集及其可用性。

数据驻留在公共 [Azure Blob 存储](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)位置：wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/。 “wasb”表示 Azure Blob 存储位置。

1. 此公共 blob 存储中的数据由已解压缩数据的三个子文件夹组成。

   1. 子文件夹 raw/count/  包含前 21 天的数据 - 从第\_00天到第\_20天
   2. 子文件夹 raw/train/  由一天的数据组成，即第\_21 天
   3. 子文件夹 raw/test/  由两天的数据组成，第\_22 天和第\_23 天
2. 对于那些想要以原始 gzip 数据开始的用户，也可以在主文件夹 raw/  中作为 day_NN.gz 使用这些文件，其中 NN 值从 00 到 23。

另一种访问、浏览和建模不需要任何本地下载的数据的方法会在本演示的后续部分中创建 Hive 表时进行介绍。

## <a name="login"></a>登录到群集头节点
若要登录到集群的头节点，请使用 [Azure 门户](https://ms.portal.azure.com)找到该集群。 单击左侧的 HDInsight 大象图标，并双击群集名称。 导航到“配置”  选项卡，双击页面底部的 CONNECT 图标，并在出现提示时输入远程访问凭据。 转到群集的头节点。

以下是首次登录到群集头节点的典型示例：

![登录到群集](./media/hive-criteo-walkthrough/Yys9Vvm.png)

左侧是“Hadoop 命令行”，这是进行数据浏览的主力。 可以看到两个有用的 URL -“Hadoop Yarn 状态”和“Hadoop 名称节点”。 Yarn 状态 URL 显示作业进度，名称节点 URL 提供有关群集配置的详细信息。

现在设置并准备开始第一部分的演练：使用 Hive 进行数据挖掘，并为 Azure 机器学习准备数据。

## <a name="hive-db-tables"></a>创建 Hive 数据库和表
要为我们的 Criteo 数据集创建 Hive 表，请在头节点的桌面上打开 ***Hadoop 命令行***，并通过输入命令输入 Hive 目录

    cd %hive_home%\bin

> [!NOTE]
> 从 Hive bin/ 目录提示符运行此演练中的所有 Hive 命令。 这会自动处理任何路径问题。 我们将术语“Hive 目录提示符”、“Hive bin/ 目录提示符”和“Hadoop 命令行”互换使用。
>
> [!NOTE]
> 若要执行任何 Hive 查询，可以使用以下命令：
>
>

        cd %hive_home%\bin
        hive

Hive REPL 出现“hive>”符号后，只需剪切并粘贴查询即可执行。

以下代码创建一个数据库“criteo”，并生成 4 个表：

* 一个用于生成在第\_00 天至第\_20 天构建的计数的表  ，
* 一个用于在第\_21 天构建的定型数据集的表  ，以及
* 两个分别用于在第\_22 天和第\_23 天构建的测试数据集的表  。

因为其中某一天是节假日，所以我们将测试数据集分为两类不同的表。 目标是确定模型是否可以检测节假日和非节假日之间的点击率差异。

为方便起见，此处显示脚本 [ sample_hive_create_criteo_database_and_tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)：

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

所有这些表都是外部的，因此可以简单地指向其 Azure Blob 存储 (wasb) 位置。

**有两种可以执行任何 Hive 查询的方法：**

1. **使用 Hive REPL 命令行**：首先发出“hive”命令，然后在 Hive REPL 命令行中复制并粘贴查询。 为此，请执行以下操作：

        cd %hive_home%\bin
        hive

     在 REPL 命令行，剪切并粘贴查询以执行它。
2. **将查询保存到文件并执行命令**：第二种方法是将查询保存到 .hql 文件 ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql))，并发出以下命令来执行查询：

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>确认数据库和表的创建
接下来，使用 Hive bin/ 目录提示符中的以下命令来确认数据库的创建：

        hive -e "show databases;"

会得到：

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

这确认了新数据库“criteo”的创建。

若要查看所创建的表，只需从 Hive bin/ 目录提示符中输入命令：

        hive -e "show tables in criteo;"

应会看到以下输出：

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a>Hive 中的数据浏览
现已准备好在 Hive 中做一些基本的数据浏览。 首先统计训练和测试数据表中的示例数目。

### <a name="number-of-train-examples"></a>定型示例数
[sample_hive_count_train_table_examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) 的内容如下所示：

        SELECT COUNT(*) FROM criteo.criteo_train;

这会生成：

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

或者，也可以从 Hive bin/ 目录提示符发出以下命令：

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>两个测试数据集中的测试示例数
现在统计两个测试数据集中的示例数目。 [sample_hive_count_criteo_test_day_22_table_examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) 的内容如下：

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

这会生成：

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

像往常一样，也可以通过发出命令从 Hive bin/ 目录提示符调用脚本：

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

最后，检查基于第 \_23 天的测试数据集中的测试示例数目。

执行此操作的命令与刚才显示的命令类似（请参阅 [sample_hive_count_criteo_test_day_23_examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)）：

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

会得到：

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>定型数据集中的标签分布
定型数据集中的标签分布是很有趣的。 为了看到这一点，我们显示 [sample_hive_criteo_label_distribution_train_table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql) 的内容：

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

这会产生标签分布：

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

请注意，正标签的百分比约为3.3%（与原始数据集一致）。

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>定型数据集中某些数值变量的直方图分布
可以使用 Hive 的本地“histogram\_numeric”函数来找出数值变量的分布。 以下是 [sample_hive_criteo_histogram_numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql) 的内容：

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

这会生成以下内容：

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

LATERAL VIEW - Hive 服务中的 explode 组合用于生成类似 SQL 的输出，而不是通常的列表。 请注意，在该表中，第一列对应于量化中心，第二列对应于量化频率。

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>定型数据集中一些数值变量的近似百分位数
有关数值变量的另一个有趣之处是近似百分位数的计算。 Hive 的本地“percentile \_approx”能为我们完成这些操作。 [sample_hive_criteo_approximate_percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) 的内容为：

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

这会生成：

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

百分位数的分布与任何数值变量的直方图分布密切相关。

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>查找定型数据集中某些分类列的唯一值的数量
继续数据浏览，我们发现某些分类列所采用的唯一值的数量。 为此，显示 [sample_hive_criteo_unique_values_categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql) 的内容：

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

这会生成：

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

请注意 Col15 有 19M 个唯一值！ 使用类似“one-hot encoding”的 naive 技术来编码这种高维分类变量是不可行的。 特别是，我们解释和演示了一中功能强大且可靠的技术，称为[使用计数学习](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx)，以有效地解决这个问题。

最后，查看一些其他分类列的唯一值的数量来结束本小节。 [sample_hive_criteo_unique_values_multiple_categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) 的内容为：

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

这会生成：

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

我们再次看到，除了 Col20，所有其他列都有许多唯一的值。

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>定型数据集中分类变量对的共同计数

分类变量对的共同计数也很有趣。 可以使用 [sample_hive_criteo_paired_categorical_counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql) 中的代码来确定：

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

按照它们的出现顺序逆序排列，在此情况下查看其中前 15 个。 会得到：

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a>对 Azure 机器学习的数据集进行下采样
浏览数据集并演示我们如何对任何变量（包括组合）进行这种类型的浏览，现在对数据集进行取样，以便可以在 Azure 机器学习中生成模型。 回想一下，我们关注的问题是：给定一组示例属性（Col2 - Col40 的特征值），预测 Col1 是 0（未单击）还是 1（单击）。

为了将训练和测试数据集降至原始大小的 1%，我们使用 Hive 的原生 RAND() 函数。 下一个脚本，[sample_hive_criteo_downsample_train_dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) 对定型数据集执行此操作：

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

这会生成：

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

脚本 [sample_hive_criteo_downsample_test_day_22_dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) 用于测试数据，第\_22 天：

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

这会生成：

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


最后，脚本 [sample_hive_criteo_downsample_test_day_23_dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) 用于测试数据，第\_23 天：

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

这会生成：

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

有此结果，我们准备使用取样缩小训练和测试数据集在 Azure 机器学习中构建模型。

在继续进行 Azure 机器学习之前，还有最后一个重要的组成部分，该部分涉及计数表。 下一小节将更详细地讨论计数表。

## <a name="count"></a>关于计数表的简要讨论
正如我们所看到的，有几个分类变量具有非常高的维度。 在演练中，我们提出了一种名为[使用计数学习](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx)的有效的技术，以高效、可靠的方式对这些变量进行编码。 有关此技术的详细信息，请参阅提供的链接。

[!NOTE]
>本演练重点讲解如何使用计数表来生成高维分类特征的紧凑表示形式。 这不是编码分类特征的唯一方法；有关其他技术的详细信息，感兴趣的用户可以查看 [one-hot 编码](https://en.wikipedia.org/wiki/One-hot)和[特性哈希](https://en.wikipedia.org/wiki/Feature_hashing)。
>

若要在计数数据上构建计数表，需要使用文件夹 raw/count 中的数据。 在建模部分中，我们向用户展示了如何从零开始构建这些计数表，或者使用预建的计数表来进行浏览。 在下文中，当我们提到“预建计数表”时，指的是使用提供的计数表。 下一节提供了有关如何访问这些表的详细说明。

## <a name="aml"></a>使用 Azure 机器学习构建模型
我们在 Azure 机器学习中的模型构建过程遵循以下步骤：

1. [将数据从 Hive 表中获取到 Azure 机器学习](#step1)
2. [创建实验： 清理数据并使其具有计数表的功能](#step2)
3. [生成、训练和评分模型](#step3)
4. [评估模型](#step4)
5. [将模型发布为 Web 服务](#step5)

现已准备好在 Azure 机器学习工作室中构建模型。 我们的取样缩小数据将作为 Hive 表保存在群集中。 使用 Azure 机器学习的“导入数据”  模块来读取此数据。 在下方提供访问此群集的存储帐户的凭据。

### <a name="step1"></a> 步骤 1：使用导入数据模块将数据从 Hive 表中导入到 Azure 机器学习中，并选择它用于机器学习实验
首先，选择“+新建”   -> “实验”   -> 空白实验  。 然后，从左上角的“搜索”  框中搜索“导入数据”。 将“导入数据”  模块拖放到实验画布（屏幕中间部分），以使用模块进行数据访问。

这是从 Hive 表获取数据时“导入数据”  的样子：

![导入数据获取数据](./media/hive-criteo-walkthrough/i3zRaoj.png)

对于“导入数据”  模块，图形中提供的参数值仅是需要提供的值类型的示例。 以下是有关如何填写“导入数据”  模块的参数集的常规指导。

1. 为“数据源”  选择“Hive 查询”
2. 在“Hive 数据库查询”  框中，一个简单的SELECT * FROM <\_数据集\_名称.\_表\_名称> - 就足够了。
3. **Hcatalog 服务器 URI**：如果群集是“abc”，则此项就是： https://abc.azurehdinsight.net
4. **Hadoop 用户帐户名称**：调试群集时选择的用户名。 （不是远程访问用户名！）
5. **Hadoop 用户帐户密码**：调试群集时选择的用户名的密码。 （不是远程访问密码！）
6. **输出数据的位置**：选择“Azure”
7. **Azure 存储帐户名称**：与群集关联的存储帐户
8. **Azure 存储帐户密钥**：与群集关联的存储帐户的密钥。
9. **Azure 容器名称**：如果群集名称为“abc”，则通常只是简单的“abc”。

“导入数据”  完成获取数据后（会在模块上看到绿色对勾），（使用选择的名称）将此数据另存为数据集。 将显示为：

![导入数据保存数据](./media/hive-criteo-walkthrough/oxM73Np.png)

右键单击“导入数据”  模块的输出端口。 这会显示“另存为数据集”  选项和“可视化”  选项。 如果单击“可视化”  选项，会显示 100 行数据，以及对某些摘要统计信息有用的右侧面板。 要保存数据，只需选择“另存为数据集”  ，并按照说明操作即可。

若要选择用于机器学习实验的保存数据集，请使用下图中显示的“搜索”  框来定位数据集。 然后只需输入给定数据集的部分名称即可访问该数据集，并将其拖动到主面板上。 将其放在主面板上，并选择用于机器学习建模。

![在主面板上拖动数据集](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> 对定型和测试数据集都执行此操作。 此外，请记住使用为此目的提供的数据库名称和表名称。 图中使用的值仅用于说明目的。\*\*
>
>

### <a name="step2"></a> 步骤 2：在 Azure 机器学习中创建一个简单实验，以预测单击/无单击
我们的 Azure 机器学习工作室试验如下所示：

![机器学习实验](./media/hive-criteo-walkthrough/xRpVfrY.png)

现在，检查一下此试验的关键组件。 将保存的训练和测试数据集拖到试验画布上。

#### <a name="clean-missing-data"></a>清理缺失数据
“清除缺失数据”  模块执行其名称所表示的功能：按用户指定的方式清除缺失的数据。 看一下此模块，我们看到：

![清理缺失数据](./media/hive-criteo-walkthrough/0ycXod6.png)

此处已选择将所有缺失值替换为 0。 还有其他选项，可以通过查看模块中的下拉列表看到这些选项。

#### <a name="feature-engineering-on-the-data"></a>对数据进行特性工程
对于大型数据集的某些分类特征，可能有数百万个唯一值。 使用诸如 one-hot 编码之类的 naive 方法来表示这种高维分类特征是完全不可行的。 本演练演示了如何使用计数功能使用内置的 Azure 机器学习模块来生成这些高维分类变量的紧凑表示形式。 最终结果是更小的模型大小、更快的定型时间和与使用其他技术类似的性能指标。

##### <a name="building-counting-transforms"></a>构建计数转换
若要构建计数功能，可使用 Azure 机器学习中提供的“构建计数转换”  模块。 模块如下所示：

![“生成计数转换”模块属性](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![“生成计数转换”模块](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> 在“计数列”框中，输入要执行计数的列。  通常，要输入是（正如所提到的）高维分类列。 请记住，Criteo 数据集有 26 个分类列：从 Col15 到 Col40。 此处对所有分类列进行计数，并给出其指数（从 15 到 40，用逗号分隔，如图所示）。
>

若要在 MapReduce 模式下使用模块（适用于大型数据集），则需要访问 HDInsight Hadoop 群集（用于功能浏览的群集也可以重复使用于此目的）及其凭据。 前面的图说明了填充值的样式（将提供的值替换为与自己的用例相关的值）。

![模块参数](./media/hive-criteo-walkthrough/05IqySf.png)

上图展示了如何输入 blob 位置。 此位置含有为构建计数表保留的数据。

此模块完成运行后，可以右键单击模块并选择“另存为转换”  选项保存转换以便稍后使用：

![“另存为转换”选项](./media/hive-criteo-walkthrough/IcVgvHR.png)

在上面显示的实验体系结构中，数据集“ytransform2”正好与保存的计数转换相对应。 对于本试验的其余部分，假设读者对某些数据使用“构建计数转换”  模块来生成计数，并可以使用这些计数来在训练和测试数据集上生成计数功能。

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>选择要将哪些计数功能作为定型和测试数据集的一部分
计数转换准备就绪后，用户可以使用“修改计数表参数”  模块选择要包括在其训练和测试数据集中的功能。 为了完整，此处展示了该模块。 但为了简单起见，在试验中并没有使用它。

![修改计数表参数](./media/hive-criteo-walkthrough/PfCHkVg.png)

在此情况下，可以看出，已使用对数几率并忽略了退避列。 还可以设置参数，例如垃圾桶阈值、要添加的用于平滑处理的伪先验示例数以及是否使用 Laplacian 噪声。 所有这些参数都是高级功能，需要注意的是，对于还不熟悉此类功能生成的用户而言，使用默认值会是一个很好的选择。

##### <a name="data-transformation-before-generating-the-count-features"></a>生成计数功能前的数据转换
现在关注的重点是在实际生成计数功能之前转换训练和测试数据。 请注意，在对数据应用计数转换之前，要使用两个“执行 R 脚本”  模块。

![“执行 R 脚本”模块](./media/hive-criteo-walkthrough/aF59wbc.png)

下面是第一个 R 脚本：

![第一个 R 脚本](./media/hive-criteo-walkthrough/3hkIoMx.png)

此 R 脚本将列重命名为“Col1”到“Col40”。 这是因为计数转换需要此格式的名称。

第二个 R 脚本通过对负类进行缩小取样来平衡正类和负类（分别是类 1和 0）之间的分布。 R 脚本会在此处演示如何执行此操作：

![第二个 R 脚本](./media/hive-criteo-walkthrough/91wvcwN.png)

在这一简单的 R 脚本中，使用了“pos\_neg\_ratio”设置正类和负类之间的平衡量。 这是十分重要的，因为改进类不平衡通常对类分布存在偏差的分类问题的性能有很大帮助（回想一下，在本例中，有 3.3% 的正类和 96.7% 的负类）。

##### <a name="applying-the-count-transformation-on-our-data"></a>对我们的数据应用计数转换
最后，可以使用“应用转换”  模块将计数转换应用于训练和测试数据集。 此模块将保存的计数转换作为一个输入，将定型或测试数据集作为另一个输入，并返回具有计数功能的数据。 如下所示：

![“应用转换”模块](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>计数功能样式概要
在本示例中看一下计数功能的样式是有所帮助的。 下面是该样式的概要：

![计数功能](./media/hive-criteo-walkthrough/FO1nNfw.png)

在此概要中，展示了之前进行计数的列，除了任何相关的回退外，还得到了计数和对数几率。

现在，准备使用这些转换的数据集构建 Azure 机器学习模型。 下一部分将演示如何执行此操作。

### <a name="step3"></a> 步骤 3：生成、训练模型并对其进行评分

#### <a name="choice-of-learner"></a>选择学习者
首先需要选择一个学习者。 使用双类提升的决策树来作为学习者。 以下是此学习者的默认选项：

![双类提升决策树参数](./media/hive-criteo-walkthrough/bH3ST2z.png)

对于试验，请选择默认值。 请注意，默认值通常有意义，并且是获得性能的快速基线的有效方法。 如果选择一旦有基线，则可以通过扫描参数来提高性能。

#### <a name="train-the-model"></a>训练模型
对于训练，只需调用“训练模型”  模块。 它的两个输入是二类提升的决策树学习者和我们的定型数据集。 如下所示：

![“定型模型”模块](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>为模型评分
获取训练的模型后，可对测试数据集进行评分，并评估其性能。 可以使用下图中显示的“评分模型”  模块以及“评估模型”  模块来完成此操作：

![“评分模型”模块](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> 步骤 4：评估模型
最后，应分析模型性能。 通常，对于两类（二进制）分类问题，一种有效地度量值为 AUC。 为了将其可视化，请将“评分模型”  模块连接到“评估模型”  模块。 在“评估模型”  模块上单击“可视化”  将生成如下图所示的图形：

![评估模块 BDT 模型](./media/hive-criteo-walkthrough/0Tl0cdg.png)

在二进制（或二类）分类问题中，预测准确性的有效度量值是曲线下面积 (AUC)。 下一部分使用此模型在测试数据集上显示结果。 为此，右键单击“评估模型”  模块的输出端口，并单击“可视化”  。

![可视化评估模型模块](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> 步骤 5：将模型发布为 Web 服务
将 Azure 机器学习模型以最小误差发布为 Web 服务的能力是使其广泛可用的一个有价值的功能。 完成后，任何人都可以使用其需要预测的输入数据调用 Web 服务，并且 Web 服务使用模型返回这些预测。

为此，请先将训练模型另存为“训练模型”对象。 通过右键单击“定型模型”  模块并使用“另存为定型模型”  选项来完成此操作。

接下来，为 Web 服务创建输入和输出端口：

* 输入端口接收与所需要预测的数据相同的形式的数据
* 输出端口返回评分标签和关联概率。

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>为输入端口选择少量几行数据
使用“应用 SQL 转换”  模块可以方便地选择仅 10 行作为输入端口数据。 使用此处显示的 SQL 查询为输入端口选择仅这几行数据：

![输入端口数据](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Web 服务
现在可以运行一个小试验，用于发布我们的 Web 服务。

#### <a name="generate-input-data-for-webservice"></a>为 webservice 生成输入数据
首先，由于计数表很大，因此我们采用几行测试数据，并从中生成带有计数功能的输出数据。 这可以作为 webservice 的输入数据格式。 如下所示：

![创建 BDT 输入数据](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> 对于输入数据格式，请使用 **Count Featurizer** 模块的 OUTPUT。 此实验完成运行后，将 **Count Featurizer** 模块的输出另存为数据集。 此数据集用于 webservice 中的输入数据。
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>发布 webservice 的评分实验
首先，我们演示一下这种情况。 基本结构为“评分模型”  模块，该模块接受训练模型对象和在前面步骤中使用 **Count Featurizer** 模块生成的几行输入数据。 使用“在数据集中选择列”来投影出评分标签和评分概率。

![在数据集中选择列](./media/hive-criteo-walkthrough/kRHrIbe.png)

请注意如何将“在数据集中选择列”  模块用于从数据集中“过滤”数据。 将显示以下内容：

![使用“在数据集中选择列”进行过滤](./media/hive-criteo-walkthrough/oVUJC9K.png)

若要获取蓝色输入和输出端口，只需单击右下角的“准备 webservice”  。 运行此实验会允许发布 Web 服务：单击右下角的“发布 Web 服务”  图标，如下所示：

![发布 Web 服务](./media/hive-criteo-walkthrough/WO0nens.png)

Web 服务发布后，会重定向到一个如下所示的页面：

![Web 服务仪表板](./media/hive-criteo-walkthrough/YKzxAA5.png)

请注意在左侧 Web 服务的两个链接：

* “请求/响应”  服务（或 RRS）适用于单个预测，本次研讨会中已使用该服务。
* “BATCH 执行”  服务 (BES) 适用于 batch 预测，该服务要求用于进行预测的输入数据驻留在 Azure Blob 存储中。

单击“请求/响应”  链接，进入一个页面，该页面提供了 C#、python 和 R 中的固有代码。此代码轻松地可用于调用 Web 服务。 请注意，此页面上的 API 密钥需要用于身份验证。

可以方便地将此 python 代码复制到 IPython Notebook 中的一个新单元格。

下面是一段带有正确 API 密钥的 python 代码。

![Python 代码](./media/hive-criteo-walkthrough/f8N4L4g.png)

请注意，默认 API 密钥已替换为 Web 服务的 API 密钥。 在 IPython Notebook 中此单元格上单击“运行”  将生成以下响应：

![IPython 响应](./media/hive-criteo-walkthrough/KSxmia2.png)

对于所询问的两个测试示例（在 JSON 框架的 python 脚本中），会以“评分标签，评分概率”的形式得到返回答案。 在本例中，已选择固有代码提供的默认值（所有数值列为 0，所有分类列为字符串“value”）。

本演练现展示完如何使用 Azure 机器学习处理大规模数据集。 我们从 1 TB 的数据开始，构建了一个预测模型，并在云中将其部署为 Web 服务。

