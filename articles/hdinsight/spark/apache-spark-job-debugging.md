---
title: 调试 Azure HDInsight 中运行的 Apache Spark 作业
description: 使用 YARN UI、Spark UI 和 Spark History Server 来跟踪和调试 Azure HDInsight 中的 Spark 群集上运行的作业
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 0e80aa44652efbc58f8259944058aabe59ca5d1a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338464"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>调试 Azure HDInsight 中运行的 Apache Spark 作业

在本文中，将了解如何使用 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI、Spark UI 和 Spark 历史记录服务器来跟踪和调试 HDInsight 群集上运行的 [Apache Spark](https://spark.apache.org/) 作业。 使用 Spark 群集中提供的笔记本启动 Spark 作业，相关信息请参阅“机器学习：使用 MLLib 对食物检测数据进行预测分析”。 也可以执行以下步骤来跟踪使用任何其他方法（例如 spark-submit）提交的应用程序。

## <a name="prerequisites"></a>先决条件

必须满足以下条件：

* Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
* 你应当已开始运行笔记本，相关信息请参阅[机器学习：使用 MLLib 对食物检测数据进行预测分析](apache-spark-machine-learning-mllib-ipython.md)。 有关如何运行此笔记本的说明，请单击以下链接。  

## <a name="track-an-application-in-the-yarn-ui"></a>在 YARN UI 中跟踪应用程序

1. 启动 YARN UI。 在“群集仪表板”下单击“Yarn”。

    ![Azure 门户启动 YARN UI](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > 或者，也可以从 Ambari UI 启动 YARN UI。 若要启动 Ambari UI，请在“群集仪表板”下单击“Ambari 主页”。 在 Ambari UI 中依次单击“YARN”、“快速链接”、活动的资源管理器和“资源管理器 UI”。

2. 由于 Spark 作业是使用 Jupyter 笔记本启动的，因此应用程序的名称为 **remotesparkmagics**（这是从笔记本启动的所有应用程序的名称）。 单击应用程序名称旁边的应用程序 ID，以获取有关该作业的详细信息。 此时会启动应用程序视图。

    ![Spark history server 查找 Spark 应用程序 ID](./media/apache-spark-job-debugging/find-application-id1.png)

    对于从 Jupyter 笔记本启动的应用程序，在退出笔记本之前，其状态始终是“正在运行”。
3. 从应用程序视图中，可以进一步深入以找到与应用程序和日志 (stdout/stderr) 关联的容器。 也可以通过单击“跟踪 URL”对应的链接来启动 Spark UI，如下所示。

    ![Spark history server 下载容器日志](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>在 Spark UI 中跟踪应用程序

在 Spark UI 中，可以深入到前面启动的应用程序所产生的 Spark 作业。

1. 若要启动 Spark UI，请在应用程序视图中，单击“跟踪 URL”旁边的链接，如上面的屏幕截图中所示。 可以看到，应用程序启动的所有 Spark 作业正在 Jupyter 笔记本中运行。

    ![Spark history server job 选项卡](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. 单击“执行器”选项卡以查看每个执行器的处理和存储信息。 还可以通过单击“线程转储”链接来检索调用堆栈。

    ![Spark history server 执行器选项卡](./media/apache-spark-job-debugging/view-spark-executors.png)

3. 单击“阶段”选项卡以查看与应用程序关联的阶段。

    ![Spark history "服务器阶段" 选项卡](./media/apache-spark-job-debugging/view-apache-spark-stages.png "查看 Spark 阶段")

    每个阶段可能有多个任务，可以查看这些任务的执行统计信息，如下所示。

    ![Spark history "服务器阶段" 选项卡详细信息](./media/apache-spark-job-debugging/view-spark-stages-details.png "查看 Spark 阶段详细信息")

4. 在阶段详细信息页上，可以启动 DAG 可视化。 展开页面顶部的“DAG 可视化”链接，如下所示。

    ![查看 Spark 阶段 DAG 可视化](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG (Direct Aclyic Graph) 呈现了应用程序中的不同阶段。 图形中的每个蓝框表示从应用程序调用的 Spark 操作。

5. 在阶段详细信息页上，还可以启动应用程序时间线视图。 展开页面顶部的“事件时间线”链接，如下所示。

    ![查看 Spark 阶段事件时间线](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    此时以时间线形式显示 Spark 事件。 时间线视图提供三个级别：跨作业、作业内和阶段内。 上图中捕获了指定阶段的时间线视图。

   > [!TIP]  
   > 如果选中“启用缩放”复选框，则可以在时间线视图中左右滚动。

6. Spark UI 中的其他选项卡也提供了有关 Spark 实例的有用信息。

   * “存储”选项卡 - 如果应用程序创建了 RDD，可以在“存储”选项卡中找到相关信息。
   * "环境" 选项卡-此选项卡提供有关 Spark 实例的大量有用信息，例如：
     * Scala 版本
     * 与群集关联的事件日志目录
     * 应用程序的执行器核心数
     * 等等

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>使用 Spark History Server 查找有关已完成的作业的信息

完成某个作业后，有关该作业的信息将保存在 Spark History Server 中。

1. 要启动 Spark History Server，请在“概述”边栏选项卡的“群集仪表板”下单击“Spark History Server”。

    ![Azure 门户启动 spark history server](./media/apache-spark-job-debugging/launch-spark-history-server.png "启动 Spark 历史记录 Server1")

   > [!TIP]  
   > 或者，也可以从 Ambari UI 启动 Spark History Server UI。 要启动 Ambari UI，请在“概述”边栏选项卡的“群集仪表板”下单击“Ambari 主页”。 在 Ambari UI 中，依次单击“Spark”、“快速链接”和“Spark History Server UI”。

2. 随后会看到已列出所有已完成的应用程序。 单击应用程序 ID 可深入到该应用程序以获取更多信息。

    ![Spark history server 完成的应用程序](./media/apache-spark-job-debugging/view-completed-applications.png "启动 Spark history Server2")

## <a name="see-also"></a>请参阅

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [使用扩展的 Spark 历史记录服务器调试 Apache Spark 作业](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>适用于数据分析师

* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 来通过 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检验结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)
* [使用 HDInsight 中的 Apache Spark 执行 Application Insight 遥测数据分析](apache-spark-analyze-application-insight-logs.md)
* [使用 Caffe on Azure HDInsight Spark 进行分布式深度学习](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>适用于 Spark 开发人员

* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)
