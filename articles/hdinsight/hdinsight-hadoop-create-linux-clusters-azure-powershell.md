---
title: 使用 PowerShell 创建 Apache Hadoop 群集 - Azure HDInsight
description: 了解如何使用 Azure PowerShell 在 Linux for HDInsight 上创建 Apache Hadoop、Apache HBase、Apache Storm 或 Apache Spark 群集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: hrasheed
ms.openlocfilehash: 51270f1fd7a662cdfd747bd0bfaf9ff03dd438a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66257921"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>使用 Azure PowerShell 在 HDInsight 中创建基于 Linux 的群集

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell 是强大的脚本环境，可以用于在 Microsoft Azure 中控制和自动执行工作负荷的部署和管理。 本文档介绍如何使用 Azure PowerShell 创建基于 Linux 的 HDInsight 群集。 此外，还提供了示例脚本。

## <a name="prerequisites"></a>必备组件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

开始执行此过程之前请做好以下准备：

* Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* [Azure PowerShell](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > 使用 Azure Service Manager 管理 HDInsight 资源的 Azure PowerShell 支持**已弃用**，已在 2017 年 1 月 1 日删除。 本文档中的步骤使用的是与 Azure Resource Manager 兼容的新 HDInsight cmdlet。
    >
    > 请按照[安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) 中的步骤安装最新版本的 Azure PowerShell。 如果脚本需要修改后才能使用与 Azure 资源管理器兼容的新 cmdlet，请参阅[迁移到适用于 HDInsight 群集的基于 Azure 资源管理器的开发工具](hdinsight-hadoop-development-using-azure-resource-manager.md)，了解详细信息。

## <a name="create-cluster"></a>创建群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

若要使用 Azure PowerShell 创建 HDInsight 群集，必须完成以下过程：

* 创建 Azure 资源组
* 创建 Azure 存储帐户
* 创建 Azure Blob 容器
* 创建 HDInsight 群集

以下脚本演示了如何创建新群集：

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

使用为群集登录指定的值创建群集的 Hadoop 用户帐户。 使用此帐户连接到群集上托管的 Web UI 或 REST API 等服务。

为 SSH 用户指定的值用于创建群集的 SSH 用户。 使用此帐户在群集上启动远程 SSH 会话和运行作业。 有关详细信息，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)文档。

> [!IMPORTANT]  
> 如果计划使用 32 个以上的辅助角色节点（在创建群集时配置或者是在创建之后通过扩展群集来配置），则还必须指定至少具有 8 个核心和 14 GB RAM 的头节点大小。
>
> 有关节点大小和相关费用的详细信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

创建群集可能需要 20 分钟。

## <a name="create-cluster-configuration-object"></a>创建群集：配置对象

还可以使用 `New-AzHDInsightClusterConfig` cmdlet 创建 HDInsight 配置对象。 然后，可以修改此配置对象，为群集启用其他配置选项。 最后，使用 `New-AzHDInsightCluster` cmdlet 的 `-Config` 参数以利用该配置。

下面的脚本创建了一个配置对象，用于在 HDInsight 群集类型上配置 R Server。 该配置支持边缘节点、RStudio 和其他存储帐户。

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> 不支持在 HDInsight 群集之外的其他位置使用存储帐户。 使用此示例时，请在与服务器相同的位置上创建其他存储帐户。

## <a name="customize-clusters"></a>自定义群集

* 请参阅[使用 Bootstrap 自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell)。
* 请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="delete-the-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>故障排除

如果在创建 HDInsight 群集时遇到问题，请参阅[访问控制要求](hdinsight-hadoop-create-linux-clusters-portal.md)。

## <a name="next-steps"></a>后续步骤

成功创建 HDInsight 群集后，请通过以下资源了解如何使用群集。

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 群集

* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](hadoop/hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 群集

* [HDInsight 中的 Apache HBase 入门](hbase/apache-hbase-tutorial-get-started-linux.md)
* [为 Apache HBase on HDInsight 开发 Java 应用程序](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 群集

* [为 Storm on HDInsight 开发 Java 拓扑](storm/apache-storm-develop-java-topology.md)
* [在 Storm on HDInsight 中使用 Python 组件](storm/apache-storm-develop-python-topology.md)
* [使用 Storm on HDInsight 部署和监视拓扑](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark 群集

* [使用 Scala 创建独立的应用程序](spark/apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark 与 BI：将 HDInsight 中的 Spark 与 BI 工具配合使用来执行交互式数据分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检验结果](spark/apache-spark-machine-learning-mllib-ipython.md)

