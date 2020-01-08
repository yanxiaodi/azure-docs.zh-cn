---
title: 在 Azure HDInsight 中使用交互式 Spark Shell
description: 交互式 Spark Shell 提供了一个读取执行打印进程，用于一次运行一个 Spark 命令并查看结果。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 7aac2812787a7c14d99377754a4f85e699ef3f09
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441893"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>从 Spark Shell 运行 Apache Spark

交互式 [Apache Spark](https://spark.apache.org/) Shell 提供了一个 REPL（读取执行打印循环）环境，用于一次运行一个 Spark 命令并查看结果。 此进程可用于开发和调试。 Spark 为其每个支持的语言都提供了一个 shell：Scala、Python 和 R。

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>通过 SSH 访问 Apache Spark Shell

通过使用 SSH 连接到群集的主头节点访问 HDInsight 上的 Apache Spark Shell：

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

可以从 Azure 门户获取群集的完整 SSH 命令：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到 HDInsight Spark 群集的窗格。
3. 选择“安全外壳 (SSH)”。

    ![Azure 门户中的 HDInsight 窗格](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. 复制显示的 SSH 命令并在终端中运行。

    ![Azure 门户中的 HDInsight SSH 窗格](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

有关使用 SSH 连接到 HDInsight 的详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="run-an-apache-spark-shell"></a>运行 Apache Spark Shell

Spark 为 Scala (spark-shell)、Python (pyspark) 和 R (sparkR) 提供 shell。 在 HDInsight 群集头节点的 SSH 会话中，输入以下命令之一：

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

现在可以用适当的语言输入 Spark 命令。

## <a name="sparksession-and-sparkcontext-instances"></a>SparkSession 和 SparkContext 实例

默认情况下，运行 Spark Shell 时，将自动实例化 SparkSession 和 SparkContext 的实例。

若要访问 SparkSession 实例，请输入 `spark`。 若要访问 SparkContext 实例，请输入 `sc`。

## <a name="important-shell-parameters"></a>重要 shell 参数

Spark Shell 命令（`spark-shell`、`pyspark` 或 `sparkR`）支持多个命令行参数。 若要查看参数的完整列表，请使用开关 `--help` 启动 Spark Shell。 请注意，上述某些参数可能仅适用于 Spark Shell 包装的 `spark-submit`。

| 开关 | description | 示例 |
| --- | --- | --- |
| --master MASTER_URL | 指定主 URL。 在 HDInsight 中，此值始终为 `yarn`。 | `--master yarn`|
| --jars JAR_LIST | 要包括在驱动程序和执行程序类路径中的本地 jar 的逗号分隔列表。 在 HDInsight 中，此列表包含 Azure 存储或 Data Lake Storage 的默认文件系统路径。 | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | 要包括在驱动程序和执行程序类路径中的 maven jar 坐标的逗号分隔列表。 依次搜索本地 maven 存储库、maven 中心存储库、使用 `--repositories` 指定的任何其他远程存储库。 坐标的格式为 *groupId*:*artifactId*:*version*。 | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | 仅适用于 Python，要在 PYTHONPATH 上放置的 .zip、.egg 或 .py 文件的逗号分隔列表。 | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅 [Azure HDInsight 上的 Apache Spark 简介](apache-spark-overview.md)。
- 若要使用 Spark 群集和 SparkSQL，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
- 若要编写使用 Spark 处理流数据的应用程序，请参阅[什么是 Apache Spark 结构化流式处理？](apache-spark-streaming-overview.md)。
