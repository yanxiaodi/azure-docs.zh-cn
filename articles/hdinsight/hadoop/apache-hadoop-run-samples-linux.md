---
title: 在 HDInsight 上运行 Apache Hadoop MapReduce 示例 - Azure
description: 开始使用 HDInsight 随附的 jar 文件中的 MapReduce 示例。 使用 SSH 连接到群集，然后使用 Hadoop 命令运行示例作业。
keywords: hadoop 示例 jar,hadoop 示例 jar,hadoop mapreduce 示例,mapreduce 示例
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: hrasheed
ms.openlocfilehash: f0251e3926c569b45ebebcd18b98df5af4564443
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64706665"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>运行 HDInsight 随附的 MapReduce 示例

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

了解如何在 HDInsight 上运行 Apache Hadoop 随附的 MapReduce 示例。

## <a name="prerequisites"></a>必备组件

* HDInsight 中的 Apache Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="the-mapreduce-examples"></a>MapReduce 示例

**位置**：示例位于 `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar` 处的 HDInsight 群集上。

**内容**：下面的示例都包含在该存档文件中：

* `aggregatewordcount`：一种基于聚合的 MapReduce 程序，用于计算输入文件中的单词数。
* `aggregatewordhist`：一种基于聚合的 MapReduce 程序，用于计算输入文件中单词的直方图。
* `bbp`：一种 MapReduce 程序，使用 Bailey-Borwein-Plouffe 来计算 Pi 的精确位数。
* `dbcount`：一个作业示例，用于计算存储在数据库中的 pageview 日志数。
* `distbbp`：一种 MapReduce 程序，使用 BBP 类型的公式来计算 Pi 的精确位数。
* `grep`：一种 MapReduce 程序，用于计算输入中某个正则表达式的匹配数。
* `join`：一种作业，可以将经过排序且等分的数据集联接起来。
* `multifilewc`：一种作业，可计算多个文件中的单词数。
* `pentomino`：一种 MapReduce 平铺排列程序，用于查找五格拼板问题的解决方案。
* `pi`：一种 MapReduce 程序，可使用拟蒙特卡罗法估算 Pi 值。
* `randomtextwriter`：一种 MapReduce 程序，可以为每个节点写入 10 GB 的随机文本数据。
* `randomwriter`：一种 MapReduce 程序，可以为每个节点写入 10 GB 的随机数据。
* `secondarysort`：一个示例，用于定义化简阶段的次级排序。
* `sort`：一种 MapReduce 程序，用于对随机写入器所写入的数据进行排序。
* `sudoku`：数独解算器。
* `teragen`：为 terasort 生成数据。
* `terasort`：运行 terasort。
* `teravalidate`：检查 terasort 的结果。
* `wordcount`：一种 MapReduce 程序，用于计算输入文件中的单词数。
* `wordmean`：一种 MapReduce 程序，用于计算输入文件中单词的平均长度。
* `wordmedian`：一种 MapReduce 程序，用于计算输入文件中单词的中间长度。
* `wordstandarddeviation`：一种 MapReduce 程序，用于计算输入文件中单词长度的标准差。

**源代码**：这些示例的源代码包含在 `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples` 处的 HDInsight 群集上。

## <a name="run-the-wordcount-example"></a>运行 wordcount 示例

1. 使用 SSH 连接到 HDInsight。 替换为`CLUSTER`与群集的名称，然后输入以下命令：

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. 在 `username@#######:~$` 提示符下，使用以下命令列出示例：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    此命令将生成本文档前一节的示例列表。

3. 使用以下命令来获取有关特定示例的帮助。 在本例中为 **wordcount** 示例：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    会收到以下消息：

        Usage: wordcount <in> [<in>...] <out>

    此消息表示可以为源文档提供多个输入路径。 最后的路径是存储输出（源文档中的单词计数）的位置。

4. 使用以下命令以便在笔记本的 Leonardo da Vinci 提供作为示例数据与你的群集中的所有单词进行计数：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    将从 `/example/data/gutenberg/davinci.txt` 读取此作业的输入。 此示例的输出存储于 `/example/data/davinciwordcount` 中。 两个路径皆位于群集的默认存储，而不是本地文件系统。

   > [!NOTE]  
   > 如字数统计示例帮助中所述，还可以指定多个输入文件。 例如，`hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` 会计算 davinci.txt 和 ulysses.txt 中单词的数目。

5. 作业完成后，使用以下命令查看输出：

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    此命令将连接通过该作业生成的所有输出文件。 它将输出显示到控制台。 输出与以下文本类似：

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    每一行表示一个单词，并显示它在输入数据中的出现次数。

## <a name="the-sudoku-example"></a>数独示例

[数独](https://en.wikipedia.org/wiki/Sudoku)是由九个 3x3 网格组成的逻辑拼图。 网格中的某些单元格包含数字，其他单元格为空白，游戏目的是找出空白单元格。 上一链接包含更多有关此拼图的信息，不过此示例的目的是找出空白单元格。 因此，我们的输入应该是采用以下格式的文件：

* 九行加九列
* 每一列可能包含数字或 `?`（表示空白单元格）
* 用空格来分隔单元格

可以通过特定方式来构造数独游戏；每一列或行的数字不能重复。 HDInsight 群集上已有一个构造正确的示例。 它位于 `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta`，且包含以下文本：

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

若要通过数独示例来运行此示例问题，请使用如下命令：

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

显示结果类似以下文本：

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi--example"></a>Pi (π) 示例

该示例使用统计学方法（拟蒙特卡罗法）来估算 pi 值。 点随机置于单位正方形中。 正方形还包含一个圆圈。 点位于圆圈中的概率等于圆圈面积 pi/4。 可以根据 4R 的值估算 pi 的值。 R 是落入圆圈内的点数与正方形内总点数的比率。 所使用的取样点越多，估算值越准确。

请使用以下命令运行此示例。 该命令使用 16 个映射（每个映射 10,000,000 个示例）来估算 pi 值：

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

此命令返回的值类似于 **3.14159155000000000000**。 例如，pi 采用前 10 位小数时为 3.1415926535。

## <a name="10-gb-graysort-example"></a>10GB GraySort 示例

GraySort 是一种基准排序。 其指标为在给大量数据（通常至少 100 TB）排序时达到的排序速率（TB/分钟）。

此示例使用适中的 10 GB 数据，这样它运行时能相对快一点。 它使用由 Owen O'Malley 和 Arun Murthy 开发的 MapReduce 应用程序。 这些应用程序在 2009 年，0.578 TB/分钟 (100 TB 用时 173 分钟） 的速率赢得年度的常规用途 ("Daytona") tb 级排序基准。 这和其他排序基准的详细信息，请参阅[排序基准](https://sortbenchmark.org/)站点。

本示例使用三组 MapReduce 程序：

* **TeraGen**：一种 MapReduce 程序，用于生成要进行排序的数据行

* **TeraSort**：对输入数据进行抽样，并使用 MapReduce 将数据排序到总序中

    TeraSort 是一种标准 MapReduce 排序，但自定义的分区程序除外。 此分区程序使用 N-1 个抽样键（用于定义每次化简的键范围）的已排序列表。 具体说来，sample[i-1] <= key < sample[i] 之类的所有键都将会发送到化简变量 i。 此分区程序可确保化简变量 i 的输出全都小于化简变量 i+1 的输出。

* **TeraValidate**：一个 MapReduce 程序，用于验证输出是否已全局排序

    它在输出目录中对于每个文件创建一个映射，每个映射都确保每个键均小于或等于前一个键。 此映射函数生成每个文件第一个和最后一个键的记录。 化简函数确保文件 i 的第一个键大于文件 i-1 的最后一个键。 任何问题都会报告为包含故障键的化简阶段的输出结果。

使用以下步骤来生成数据，排序，并对输出进行验证：

1. 生成 10 GB 数据，这些数据被存储到 HDInsight 群集在 `/example/data/10GB-sort-input` 处的默认存储：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    `-Dmapred.map.tasks` 告诉 Hadoop 多少个映射任务用于此作业。 最后两个参数指示作业创建 10 GB 数据并将其存储在 `/example/data/10GB-sort-input` 处。

2. 使用以下命令对数据排序：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    `-Dmapred.reduce.tasks` 告诉 Hadoop 多少个化简任务会用于此作业。 最后两个参数只是数据的输入和输出位置。

3. 使用以下方法来验证排序所生成的数据：

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>后续步骤

在本文中，学习了如何运行基于 Linux 的 HDInsight 群集附带的示例。 有关 Pig、Hive 和 MapReduce 如何与 HDInsight 配合使用的教程，请参阅以下主题：

* [将 Apache Pig 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)
* [将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 MapReduce 与 HDInsight 上的 Apache Hadoop 配合使用](hdinsight-use-mapreduce.md)