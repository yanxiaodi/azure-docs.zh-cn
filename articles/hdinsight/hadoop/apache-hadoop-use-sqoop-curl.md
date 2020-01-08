---
title: 在 Azure HDInsight 中使用 Apache Sqoop 导出数据
description: 了解如何使用卷将 Apache Sqoop 作业远程提交到 Azure HDInsight。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: f70c0a0b68e24e3d61a6c0cef238d1f60911e271
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810734"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>使用 Curl 在 HDInsight 中运行 Apache Sqoop 作业
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Curl 在 HDInsight 中的 Apache Hadoop 群集上运行 Apache Sqoop 作业。 本文演示如何使用 Curl 从 Azure 存储导出数据并将其导入到 SQL Server 数据库中。 本文是[在 HDInsight 中将 Apache Sqoop 与 Hadoop 配合使用](./hdinsight-use-sqoop.md)的续篇。

本文档使用 Curl 演示如何使用原始 HTTP 请求来与 HDInsight 交互，以便运行、监视和检索 Sqoop 作业的结果。 若要执行这些操作，需要使用 HDInsight 群集提供的 WebHCat REST API（前称 Templeton）。

## <a name="prerequisites"></a>先决条件

* 从[在 HDInsight 中将 Apache Sqoop 与 Hadoop 配合使用](./hdinsight-use-sqoop.md)中完成[设置测试环境](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)。

* 用于查询 Azure SQL 数据库的客户端。 考虑使用 [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) 或 [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)。

* [Curl](https://curl.haxx.se/). Curl 是一种将数据传入或传出 HDInsight 群集的工具。

* [jq](https://stedolan.github.io/jq/). jq 实用工具用于处理从 REST 请求返回的 JSON 数据。

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>使用 Curl 提交 Apache Sqoop 作业

通过 Curl 将使用 Apache Sqoop 作业的数据从 Azure 存储导出到 SQL Server。

> [!NOTE]  
> 使用 Curl 或者与 WebHCat 进行任何其他形式的 REST 通信时，必须提供 HDInsight 群集管理员用户名和密码对请求进行身份验证。 此外，还必须使用群集名称作为用来向服务器发送请求的统一资源标识符 (URI) 的一部分。

对于本部分中的命令，请将 `USERNAME` 替换为要向群集进行身份验证的用户，并将 `PASSWORD` 替换为用户帐户的密码。 将 `CLUSTERNAME` 替换为群集的名称。
 
REST API 通过 [基本身份验证](https://en.wikipedia.org/wiki/Basic_access_authentication)进行保护。 始终应该使用安全 HTTP (HTTPS) 来发出请求，以确保安全地将凭据发送到服务器。

1. 在命令行中，使用以下命令验证你是否可以连接到 HDInsight 群集。

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    应会收到类似于下面的响应：

    ```json
    {"status":"ok","version":"v1"}
    ```

2. 将 `SQLDATABASESERVERNAME`、`USERNAME@SQLDATABASESERVERNAME`、`PASSWORD`、`SQLDATABASENAME` 替换为先决条件中的相应值。 使用以下命令 sqoop 作业：

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    此命令中使用的参数如下：

   * **-d** - 由于未使用 `-G`，请求将按默认使用 POST 方法。 `-d` 指定与请求一起发送的数据值。

       * **user.name** - 正在运行命令的用户。

       * **command** - 要执行的 Sqoop 命令。

       * **statusdir** - 此作业的状态要写入到的目录。

     此命令应会返回可用于检查作业状态的作业 ID。

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. 若要检查作业的状态，请使用以下命令。 将 `JOBID` 替换为上一步骤返回的值。 例如，如果返回值为`{"id":"job_1415651640909_0026"}`，则`JOBID`为`job_1415651640909_0026`。

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    如果作业已完成，状态将是 **SUCCEEDED**。
   
   > [!NOTE]  
   > 此 Curl 请求返回具有作业相关信息的 JavaScript 对象表示法 (JSON) 文档；使用 jq 可以仅检索状态值。

4. 在作业的状态更改为“SUCCEEDED”后，可以从 Azure Blob 存储中检索作业的结果。 随查询一起传递的 `statusdir` 参数包含输出文件的位置；在本例中，该位置为 `wasb:///example/data/sqoop/curl`。 此地址会将作业的输出存储在 HDInsight 群集所用的默认存储容器的 `example/data/sqoop/curl` 目录中。

    可使用 Azure 门户访问 stderr 和 stdout blob。

5. 若要验证数据是否已导出，请在 SQL 客户端中使用以下查询查看已导出的数据：

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>限制
* 批量导出 - 在基于 Linux 的 HDInsight 上，用于将数据导出到 Microsoft SQL Server 或 Azure SQL 数据库的 Sqoop 连接器目前不支持批量插入。
* 批处理 - 在基于 Linux 的 HDInsight 上，如果执行插入时使用 `-batch` 开关，Sqoop 会执行多次插入而不是批处理插入操作。

## <a name="summary"></a>总结
如本文档中所示，可以使用原始 HTTP 请求来运行、监视和查看 HDInsight 群集上的 Sqoop 作业的结果。

有关本文中使用的 REST 接口的详细信息，请参阅 <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API 指南</a>。

## <a name="next-steps"></a>后续步骤
[将 Apache Sqoop 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-sqoop.md)

涉及 curl 的其他 HDInsight 文章:
 
* [使用 Azure REST API 创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [使用 REST 在 HDInsight 中通过 Apache Hadoop 运行 Apache Hive 查询](apache-hadoop-use-hive-curl.md)
* [使用 REST 通过 Apache Hadoop on HDInsight 运行 MapReduce 作业](apache-hadoop-use-mapreduce-curl.md)

