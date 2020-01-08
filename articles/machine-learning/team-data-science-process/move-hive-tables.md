---
title: 创建 Hive 表以及从 Blob 存储加载数据 - Team Data Science Process
description: 使用 Hive 查询创建 Hive 表以及从 Azure Blob 存储加载数据。 对 Hive 表进行分区和使用优化行纵栏表 (ORC) 格式来增强查询性能。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: af9c072c428c486cab89288db4c9ee1c26513185
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250132"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>从 Blob 存储创建 Hive 表和加载数据

本文介绍通用 Hive 查询，该查询可创建 Hive 表并从 Azure Blob 存储加载数据。 同时提供了一些如何对 Hive 表进行分区和使用优化行纵栏表 (ORC) 格式来增强查询性能的指南。

## <a name="prerequisites"></a>系统必备
本文假设用户具备以下条件：

* 已创建 Azure 存储帐户。 有关说明，请参阅[关于 Azure 存储帐户](../../storage/common/storage-introduction.md)。
* 已预配具有 HDInsight 服务的自定义 Hadoop 群集。  如果需要说明，请参阅[在 HDInsight 中设置群集](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。
* 已启用了对群集的远程访问，登录并打开了 Hadoop 命令行控制台。 如果需要说明，请参阅[管理 Apache Hadoop 群集](../../hdinsight/hdinsight-administer-use-portal-linux.md)。

## <a name="upload-data-to-azure-blob-storage"></a>将数据上传到 Azure Blob 存储
如果按照[设置 Azure 虚拟机以进行高级分析](../../machine-learning/data-science-virtual-machine/overview.md)中提供的说明创建 Azure 虚拟机，那么此脚本文件应已下载到了虚拟机上的 *C:\\Users\\\<用户名\>\\Documents\\Data Science Scripts* 目录中。 这些 Hive 查询仅需要用户在相应的字段中插入自己的数据架构和 Azure Blob 存储配置，即可开始进行提交。

假设 Hive 表的数据是**未压缩**的表格格式，并且该数据已上传至 Hadoop 群集所使用的存储帐户的默认或其他容器。

如果要针对 **NYC 出租车行程数据**进行练习，请执行以下操作：

* **下载** 24 个 [NYC 出租车行程数据](https://www.andresmh.com/nyctaxitrips)文件（12 个行程文件和 12 个费用文件），
* 将所有文件**解压缩**为 .csv 文件，然后
* 将它们上传  到 Azure 存储帐户的默认设置（或适当的容器）；在[将 Azure 存储与 Azure HDInsight 群集配合使用](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)主题中介绍了用于这类帐户的选项。 可在此[文章](hive-walkthrough.md#upload)中找到将 .csv 文件上传到存储帐户的默认容器的流程。

## <a name="submit"></a>如何提交 Hive 查询
可通过以下方法提交 Hive 查询：

1. [在 Hadoop 群集的头节点中通过 Hadoop 命令行提交 Hive 查询](#headnode)
2. [使用 Hive 编辑器提交 Hive 查询](#hive-editor)
3. [使用 Azure PowerShell 命令提交 Hive 查询](#ps)

Hive 查询类似于 SQL。 如果熟悉 SQL，可能会发现[适用于 SQL 用户的 Hive 备忘单](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)很有用。

提交 Hive 查询时，还可控制 Hive 查询的输出目标，无论是输出到屏幕上还是头节点上的本地文件，或是输入到 Azure blob。

### <a name="headnode"></a> 1.在 Hadoop 群集的头节点中通过 Hadoop 命令行提交 Hive 查询
如果是复杂的 Hive 查询，在 Hadoop 群集的头节点中直接提交通常比使用 Hive 编辑器或 Azure PowerShell 脚本进行提交的处理速度更快。

登录到 Hadoop 群集的头节点，在头节点的桌面上打开 Hadoop 命令行，并输入命令 `cd %hive_home%\bin`。

有三种方法可在 Hadoop 命令行中提交 Hive 查询：

* 直接提交
* 使用 .hql 文件
* 使用 Hive 命令控制台

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>直接在 Hadoop 命令行中提交 Hive 查询。
可以运行类似 `hive -e "<your hive query>;` 的命令直接在 Hadoop 命令行中提交简单的 Hive 查询。 此处有一个示例，其中用红色框突出了提交 Hive 查询的命令，用红色框突出了 Hive 查询中的输出。

![用于提交 Hive 查询的命令以及来自 Hive 查询的输出](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>在 .hql 文件中提交 Hive 查询
如果 Hive 查询更加复杂并且具有多个行，那么在命令行或 Hive 命令控制台中编辑查询的方法并不可行。 另一种方法是在 Hadoop 群集的头节点中使用文本编辑器，将 Hive 查询保存到头节点本地目录中的 .hql 文件中。 然后可使用如下所示的 `-f` 参数将 .hql 文件中的 Hive 查询进行提交：

    hive -f "<path to the .hql file>"

![.hql 文件中的 Hive 查询](./media/move-hive-tables/run-hive-queries-3.png)

**取消在屏幕上打印 Hive 查询的进度状态**

默认情况下，在 Hadoop 命令行中提交了 Hive 查询后，Map/Reduce 作业的进度会打印在屏幕上。 若要取消在屏幕上打印 Map/Reduce 作业的进度，可在命令行中使用参数 `-S`（“S”大写），如下所示：

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>在 Hive 命令控制台中提交 Hive 查询。
也可以首先通过在 Hadoop 命令行中运行命令 `hive` 输入 Hive 命令控制台，然后在 Hive 命令控制台中提交 Hive 查询。 下面是一个示例。 在此示例中，两个红色框分别突出显示了用于输入 Hive 命令控制台的命令，以及在 Hive 命令控制台中提交的 Hive 查询。 绿色框突出显示了 Hive 查询的输出。

![打开 Hive 命令控制台并输入命令，查看 Hive 查询输出](./media/move-hive-tables/run-hive-queries-2.png)

之前的示例会直接在屏幕上输出 Hive 查询结果。 此外，可以将输出写入到头节点上的本地文件，或者写入到 Azure blob。 然后，可以使用其他工具进一步对 Hive 查询输出进行分析。

**将 Hive 查询结果输出到本地文件。**
要将 Hive 查询结果输出到头节点上的本地目录，必须按如下所示的那样在 Hadoop 命令行中提交 Hive 查询：

    hive -e "<hive query>" > <local path in the head node>

在下面的示例中，Hive 查询的输出将写入到目录 `C:\apps\temp` 中的文件 `hivequeryoutput.txt` 中。

![Hive 查询的输出](./media/move-hive-tables/output-hive-results-1.png)

**将 Hive 查询结果输出到 Azure blob**

还可以将 Hive 查询结果输出到 Azure blob，其位于 Hadoop 群集的默认容器中。 以下是使用此方法的 Hive 查询：

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

在下面的示例中，Hive 查询的输出将写入到一个 blob 目录 `queryoutputdir`，该目录位于 Hadoop 群集的默认容器中。 此处，只需提供目录名称，无需提供 blob 名称。 如果同时提供目录名称和 blob 名称，会引发错误，例如：`wasb:///queryoutputdir/queryoutput.txt`。

![Hive 查询的输出](./media/move-hive-tables/output-hive-results-2.png)

如果使用 Azure 存储资源管理器打开 Hadoop 群集的默认容器，则可以看到 Hive 查询的输出，如下图中所示。 可以应用筛选器（红色框中突出显示的），以此来仅检索名称中具有指定字母的 blob。

![显示 Hive 查询输出的 Azure 存储资源管理器](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2.使用 Hive 编辑器提交 Hive 查询
还可以通过在 web 浏览器中输入 URL, 将 " *https: Hadoop 群集名称" clustername>.azurehdinsight.net\//Home/HiveEditor 格式输入为 "https:/\<Hadoop 群集 > 名称*"。 必须先登录才能查看此控制台，因此需要使用 Hadoop 群集凭据进行登录。

### <a name="ps"></a> 3.使用 Azure PowerShell 命令提交 Hive 查询
也可以使用 PowerShell 来提交 Hive 查询。 有关说明，请参阅[使用 PowerShell 提交 Hive 作业](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md)。

## <a name="create-tables"></a>创建 Hive 数据库和表
Hive 查询在 [GitHub 存储库](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql)中共享，并可从该处下载。

以下是创建一个 Hive 表的 Hive 查询。

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

以下是需要插入的字段以及其他配置的说明：

* **\<数据库名称\>** ：要创建的数据库的名称。 如果只想使用默认数据库，则可以省略 *create database...* 查询。
* **\<表名称\>** ：要在指定的数据库内创建的表的名称。 如果你要使用默认的数据库，则该表可通过 *\<表名称\>* 而非 \<数据库名称\> 直接引用。
* **\<字段分隔符\>** ：用于分隔数据文件（要上传到 Hive 表）中的字段的分隔符。
* **\<行分隔符\>** ：用于分隔数据文件中的行的分隔符。
* **\<存储位置\>** ：用于保存 Hive 表数据的 Azure 存储位置。 如果你未指定 *LOCATION \<存储位置\>* ，则默认情况下，数据库和表存储在 Hive 群集的默认容器的 hive/warehouse/  目录中。 如果要指定存储位置，该存储位置必须在数据库和表的默认容器中。 此位置必须与群集的默认容器相关, 格式为 *"wasb:///\<directory 1 >/"* 或 *"\<wasb:///directory 1 >/\<directory 2 >/"* 等。执行查询后，相对目录会创建在默认容器中。
* **TBLPROPERTIES("skip.header.line.count"="1")** ：如果数据文件具有标题行，则必须在 create table  查询的**末尾处**添加此属性。 否则，标题行将作为记录加载到表。 如果数据文件没有标题行，则可以在查询中省略此配置。

## <a name="load-data"></a>将数据加载到 Hive 表
以下是将数据加载到 Hive 表的 Hive 查询。

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **\<Blob 数据的路径\>** ：如果要上传到 Hive 表的 blob 文件位于 HDInsight Hadoop 群集的默认容器 *\<对 blob 数据的路径\>* 应采用格式 *wasb: / /\<此容器中目录 > /\<blob 文件名称 >* 。 blob 文件也可以在 HDInsight Hadoop 群集的其他容器中。 在这种情况下,  *\<blob 数据\>的路径*应采用 *\<"wasb://容器名称" 格式 >\<存储帐户名称 >\<> "* 。

  > [!NOTE]
  > 要上传到 Hive 表的 blob 数据必须位于 Hadoop 群集存储帐户的默认或其他容器中。 否则，*LOAD DATA* 查询会失败，并声称它无法访问数据。
  >
  >

## <a name="partition-orc"></a>高级主题：已分区表以及将 Hive 数据存储为 ORC 格式
如果数据较大，对表进行分区则有利于仅需要对表的少量分区进行扫描的查询。 例如，可以将网站的日志数据按照日期进行分区，这是合理的。

除了对 Hive 表进行分区以外，还有益于以优化行纵栏表 (ORC) 格式存储 Hive 数据。 有关 ORC 格式设置的详细信息，请参阅<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">使用 ORC 文件提高 Hive 读取、写入和处理数据时的性能</a>。

### <a name="partitioned-table"></a>已分区表
以下是创建已分区表并将数据加载到其中的 Hive 查询。

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

查询已分区表时，建议在 `where` 子句的**开头**添加分区条件，这样能显著提高搜索的效力。

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>将 Hive 数据存储为 ORC 格式
不能直接将 Blob 存储中的数据加载到存储为 ORC 格式的 Hive 表中。 下面的步骤介绍了如何将 Azure blob 中的数据加载到存储为 ORC 格式的 Hive 表中。

创建外部表 **STORED AS TEXTFILE**，然后将 Blob 存储中的数据加载到此表。

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

使用与步骤 1 中的外部表相同的架构和相同的字段分隔符创建内部表，并且将 Hive 数据存储为 ORC 格式。

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

从步骤 1 中的外部表选择数据，然后将其插入到 ORC 表

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> 如果 TEXTFILE 表 *\<数据库名称\>.\<外部 textfile 表名称\>* 具有分区，则在步骤 3 中，`SELECT * FROM <database name>.<external textfile table name>` 命令会选择分区变量作为返回的数据集中的字段。 将数据插入 *\<数据库名称\>.\<ORC 表名称\>* 失败，因为 *\<数据库名称\>.\<ORC 表名称\>* 没有将分区变量作为表架构中的字段。 在这种情况下，需要专门选择要插入到 *\<数据库名称\>.\<ORC 表名称\>* 的字段，如下所示：
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

在所有数据都已插入到 *\<数据库名称\>.\<ORC 表名称\>* 后，使用以下查询可以安全地删除 *\<外部 textfile 表名称\>* ：

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

完成此过程后，应该拥有一个可用的表，此表中具有 ORC 格式的数据。  
