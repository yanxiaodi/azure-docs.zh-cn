---
title: 在 HDInsight 中的 Apache Hadoop 上将 C# 与 Apache Hive 和 Apache Pig 配合使用 - Azure
description: 了解在 Azure HDInsight 中如何将 C# 用户定义函数 (UDF) 与 Apache Hive 和 Apache Pig 流式处理配合使用。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: fa40f206447f631c78052bda085b26a56e481194
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066919"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>将C#用户定义函数与 HDInsight 中 Apache Hadoop 上的 Apache Hive 和 Apache Pig 配合使用

了解如何在 HDInsight 中将 C# 用户定义函数 (UDF) 与 Apache Hive 和 Apache Pig 配合使用。

> [!IMPORTANT]
> 本文档中的各个步骤适用于基于 Linux 和基于 Windows 的 HDInsight 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 组件版本控制](../hdinsight-component-versioning.md)。

Hive 和 Pig 都可以将数据传递到外部应用程序以进行处理。 此过程称为_流式处理_。 使用 .NET 应用程序时，数据将传递到 STDIN 上的应用程序，该应用程序也会在 STDOUT 上返回结果。 若要从 STDIN 和 STDOUT 读取和写入数据，可以使用控制台应用程序中的 `Console.ReadLine()` 和 `Console.WriteLine()`。

## <a name="prerequisites"></a>先决条件

* 熟悉编写和生成面向 .NET Framework 4.5 的 C# 代码。

    * 使用任何需要的 IDE。 我们建议使用 [Visual Studio](https://www.visualstudio.com/vs) 2015、2017 或 [Visual Studio Code](https://code.visualstudio.com/)。 本文档中的各个步骤都使用 Visual Studio 2017。

* 将 .exe 文件上传到群集以及运行 Pig 和 Hive 作业的方法。 建议使用针对 Visual Studio 的 Data Lake 工具、Azure PowerShell 和 Azure 经典 CLI。 本文档中的各个步骤都使用针对 Visual Studio 的 Data Lake 工具上传文件和运行 Hive 查询示例。

    有关运行 Hive 查询和 Pig 作业的其他方法的信息，请参阅以下文档：

    * [将 Apache Hive 和 HDInsight 配合使用](hdinsight-use-hive.md)

    * [将 Apache Pig 和 HDInsight 配合使用](hdinsight-use-pig.md)

* HDInsight 群集上的 Hadoop。 有关创建群集的详细信息，请参阅[创建 HDInsight 群集](../hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="net-on-hdinsight"></a>HDInsight 上的 .NET

* 使用 [Mono (https://mono-project.com)](https://mono-project.com) 运行 .NET 应用程序的__基于 Linux 的 HDInsight__ 群集。 HDInsight 版本 3.6 附带了 Mono 版本 4.2.1。

    有关 Mono 与 .NET Framework 版本的兼容性的详细信息，请参阅 [Mono 兼容性](https://www.mono-project.com/docs/about-mono/compatibility/)。

* __基于 Windows 的 HDInsight__ 群集使用 Microsoft .NET CLR 运行 .NET 应用程序。

有关包含在 HDInsight 版本中的 .NET framework 和 Mono 版本的详细信息，请参阅 [HDInsight 组件版本](../hdinsight-component-versioning.md)。

## <a name="create-the-c-projects"></a>创建 C\# 项目

### <a name="apache-hive-udf"></a>Apache Hive UDF

1. 打开 Visual Studio 并创建一个解决方案。 对于项目类型，选择“控制台应用(.NET Framework)”，并将新项目命名为“HiveCSharp”。

    > [!IMPORTANT]
    > 如果使用的是基于 Linux 的 HDInsight 群集，请选择“.NET Framework 4.5”。 有关 Mono 与 .NET Framework 版本的兼容性的详细信息，请参阅 [Mono 兼容性](https://www.mono-project.com/docs/about-mono/compatibility/)。

2. 将 Program.cs 的内容替换为以下代码：

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. 生成项目。

### <a name="apache-pig-udf"></a>Apache Pig UDF

1. 打开 Visual Studio 并创建一个解决方案。 对于项目类型，选择“控制台应用程序”，并将新项目命名为“PigUDF”。

2. 将 **Program.cs** 文件的内容替换为以下代码：

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    此代码分析发送自 Pig 的行，并对以 `java.lang.Exception` 开头的行重新设置格式。

3. 保存 **Program.cs**，并生成项目。

## <a name="upload-to-storage"></a>上传到存储

1. 在 Visual Studio 中，打开“服务器资源管理器”。

2. 依次展开“Azure”和“HDInsight”。

3. 如果出现提示，请输入 Azure 订阅凭据，并单击“登录”。

4. 展开要将此应用程序部署到的 HDInsight 群集。 列出带有文本“（默认存储帐户）”的条目。

    ![显示群集存储帐户的服务器资源管理器](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * 如果此条目可以展开，则在使用 __Azure 存储帐户__作为该群集的默认存储。 要查看该群集的默认存储上的文件，请展开该条目，并双击“（默认容器）”。

    * 如果此条目无法展开，则在使用 __Azure Data Lake Storage__ 作为该群集的默认存储。 若要查看该群集的默认存储上的文件，请双击“（默认存储帐户）”条目。

6. 若要上传 .exe 文件，请使用以下方法之一：

   * 如果使用的是 __Azure 存储帐户__，请单击“上传”图标，并浏览到“HiveCSharp”项目的“bin\debug”文件夹。 最后，选择 **HiveCSharp.exe** 文件并单击“确定”。

       ![新项目的 HDInsight 上传图标](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)
    
   * 如果使用的是 __Azure Data Lake Storage__，请右键单击文件列表中的空白区域，并选择“上传”。 最后，选择“HiveCSharp.exe”文件并单击“打开”。

     上传“HiveCSharp.exe”完成后，请为“PigUDF.exe”文件重复该上传过程。

## <a name="run-an-apache-hive-query"></a>运行 Apache Hive 查询

1. 在 Visual Studio 中，打开“服务器资源管理器”。

2. 依次展开“Azure”和“HDInsight”。

3. 右键单击已将 **HiveCSharp** 应用程序部署到的群集，并选择“编写 Hive 查询”。

4. 请使用以下文本执行 Hive 查询：

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > 取消注释与用于群集的默认存储类型相匹配的 `add file` 语句。

    此查询将从 `hivesampletable` 中选择 `clientid`、`devicemake` 和 `devicemodel` 字段并将这些字段传递到 HiveCSharp.exe 应用程序。 该查询预期应用程序返回三个字段，它们存储为 `clientid`、`phoneLabel` 和 `phoneHash`。 该查询还预期在默认存储容器的根目录中找到 HiveCSharp.exe。

5. 单击“提交”将作业提交到 HDInsight 群集。 此时会打开“Hive 作业摘要”窗口 。

6. 单击“刷新”以刷新摘要，直到“作业状态”更改为“已完成”。 若要查看作业输出，请单击“作业输出”。

## <a name="run-an-apache-pig-job"></a>运行 Apache Pig 作业

1. 使用 SSH 连接到 HDInsight 群集。 例如， `ssh sshuser@mycluster-ssh.azurehdinsight.net` 。 有关详细信息，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. 使用以下命令之一启动 Pig 命令行：

        pig

    > [!IMPORTANT]
    > 如果使用的是基于 Windows 的群集，请改用以下命令：
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    此时显示 `grunt>` 提示。

3. 输入以下命令以运行使用 .NET Framework 应用程序的 Pig 作业：

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    `DEFINE` 语句为 pigudf.exe 应用程序创建别名 `streamer`，`CACHE` 将从群集的默认存储中加载它。 以后，可以将 `streamer` 与 `STREAM` 运算符配合使用来处理 LOG 中包含的单一行，并将数据返回为一系列的列。

    > [!NOTE]
    > 用于流式处理的应用程序名称在使用别名时必须用 \`（反斜杠引号）字符括起来，当与 `SHIP` 一起使用时必须用 ' （单引号）括起来。

4. 在输入最后一行后，该作业应该启动。 它将返回类似于以下文本的输出：

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>后续步骤

在本文档中，已了解了如何在 HDInsight 上通过 Hive 和 Pig 使用 .NET Framework 应用程序。 如果希望了解如何将 Python 与 Hive 和 Pig 配合使用，请参阅[在 HDInsight 中将 Python 与 Apache Hive 和 Apache Pig 配合使用](python-udf-hdinsight.md)。

若要了解使用 Pig 和 Hive 的其他方式以及如何使用 MapReduce，请参阅以下文档：

* [将 Apache Hive 和 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hdinsight-use-mapreduce.md)
