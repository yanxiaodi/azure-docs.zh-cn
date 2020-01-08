---
title: 什么是 Azure HDInsight 中的交互式查询？
description: Azure HDInsight 中的交互式查询（也称为 Apache Hive LLAP）简介
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 09/17/2019
ms.openlocfilehash: 3e0fddc03ad880d36876276827198b0ac180a00d
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076356"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>什么是 Azure HDInsight 中的交互式查询？

交互式查询（也称为 Apache Hive LLAP 或[低延迟分析处理](https://cwiki.apache.org/confluence/display/Hive/LLAP)）是一种 Azure HDInsight [群集类型](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types)。 交互式查询支持内存中缓存，可提高 Apache Hive 查询速度和交互性。 客户使用交互式查询以超快的方式查询存储在 Azure 存储和 Azure Data Lake Storage 中的数据。 交互式查询使开发人员和数据科学家可以使用他们最喜欢的 BI 工具轻松处理大数据。 HDInsight 交互式查询支持使用多种工具轻松访问大数据。

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

交互式查询群集与 Apache Hadoop 群集有所不同。 交互式 Hive 群集只包含 Hive 服务。

仅可通过 Apache Ambari Hive 视图、Beeline 和 Microsoft Hive 开放式数据库连接驱动程序 (Hive ODBC) 访问交互式查询群集中的 Hive 服务。 不能通过 Hive 控制台、Templeton、Azure 经典 CLI 或 Azure PowerShell 对其进行访问。

## <a name="create-an-interactive-query-cluster"></a>创建交互式查询群集

有关创建 HDInsight 群集的信息，请参阅[在 HDInsight 中创建 Apache Hadoop 群集](../hdinsight-hadoop-provision-linux-clusters.md)。 选择“交互式查询”群集类型。

> [!IMPORTANT]
> 交互式查询群集的最小头节点大小为 Standard_D13_v2。 有关详细信息，请参阅 [Azure VM 大小调整图表](../../cloud-services/cloud-services-sizes-specs.md#dv2-series)。

## <a name="execute-apache-hive-queries-from-interactive-query"></a>从交互式查询执行 Apache Hive 查询

若要执行 Hive 查询，可以使用以下选项：

* 使用 Microsoft Power BI

    请参阅[在 Azure HDInsight 中使用 Power BI 直观显示交互式查询 Apache Hive 数据](./apache-hadoop-connect-hive-power-bi-directquery.md)以及[在 Azure HDInsight 中使用 Power BI 直观显示大数据](../hadoop/apache-hadoop-connect-hive-power-bi.md)。

* 使用 Visual Studio

    请参阅[使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Apache Hive 查询](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)。

* 使用 Visual Studio Code

    请参阅[将 Visual Studio Code 用于 Apache Hive、LLAP 或 pySpark](../hdinsight-for-vscode.md)。
* 使用 Apache Ambari Hive 视图运行 Apache Hive。
  
    请参阅[将 Apache Hive 视图与 Azure HDInsight 中的 Apache Hadoop 配合使用](../hadoop/apache-hadoop-use-hive-ambari-view.md)。

* 使用 Beeline 运行 Apache Hive。
  
    请参阅[通过 Beeline 将 Apache Hive 与 HDInsight 中的 Apache Hadoop 配合使用](../hadoop/apache-hadoop-use-hive-beeline.md)。
  
    可以使用来自头结点或空边缘节点的 Beeline。 建议使用来自空边缘节点的 Beeline。 有关如何使用空边缘节点创建 HDInsight 群集的信息，请参阅[在 HDInsight 中使用空边缘节点](../hdinsight-apps-use-edge-node.md)。
* 使用 Hive ODBC 运行 Apache Hive。
  
    请参阅[使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Apache Hadoop](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)。

若要查找 Java Database Connectivity (JDBC) 连接字符串：

1. 使用以下 URL 登录 Apache Ambari：`https://<cluster name>.AzureHDInsight.net`。
2. 在左侧菜单中，选择“Hive”  。
3. 若要复制 URL，请选择剪贴板图标：

   ![HDInsight Hadoop 交互式查询 LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[在 HDInsight 中创建交互式查询群集](../hdinsight-hadoop-provision-linux-clusters.md)。
* 了解如何[在 Azure HDInsight 中使用 Power BI 直观显示大数据](../hadoop/apache-hadoop-connect-hive-power-bi.md)。
* 了解如何[在 Azure HDInsight 中使用 Apache Zeppelin 运行 Apache Hive 查询](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
