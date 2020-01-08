---
title: 排查 Azure HDInsight 中的 HDFS 问题
description: 获取有关使用 HDFS 和 Azure HDInsight 的常见问题答案。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 1c5d9f665c9b3e7a439a09f4259f304f8f8b1a0a
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718336"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>使用 Azure HDInsight 对 Apache Hadoop HDFS 进行故障排除

了解在 Apache Ambari 中使用 Hadoop 分布式文件系统 (HDFS) 有效负载时遇到的常见问题及其解决方法。 有关完整的命令列表，请参阅[HDFS 命令指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html)和[文件系统 Shell 指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)。

## <a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>如何从群集内访问本地 HDFS？

### <a name="issue"></a>问题

从 HDInsight 群集内通过命令行和应用程序代码（而不是使用 Azure Blob 存储或 Azure Data Lake Storage）访问本地 HDFS。

### <a name="resolution-steps"></a>解决步骤

1. 在命令提示符下，按原义使用 `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...`，如以下命令中所示：

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. 从源代码按原义使用 URI `hdfs://mycluster/`，如以下示例应用程序中所示：

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. 使用以下命令在 HDInsight 群集上运行已编译的 .jar 文件（例如，名为 `java-unit-tests-1.0.jar` 的文件）：

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="du"></a>du

[-Du](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du)命令显示给定目录中包含的文件和目录的大小或文件的长度（如果它只是一个文件）。

@No__t-0 选项将生成所显示文件长度的聚合摘要。  
@No__t-0 选项格式化文件大小。

例如：

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>rm

[-Rm](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm)命令删除指定为参数的文件。

例如：

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
