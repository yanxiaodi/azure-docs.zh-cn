---
title: 有关在基于 Linux 的 HDInsight 上使用 Hadoop 的提示 - Azure
description: 获取有关使用基于 Linux 的 HDInsight (Hadoop) 群集的实施提示（群集在 Azure 云中熟悉的 Linux 环境中运行）。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: f50702688b9a261ed98c2eb3a5892d1bdbe8d11b
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71308089"
---
# <a name="information-about-using-hdinsight-on-linux"></a>有关在 Linux 上使用 HDInsight 的信息

Azure HDInsight 群集提供了基于熟悉的 Linux 环境并在 Azure 云中运行的 Apache Hadoop。 在大多数情况下，它的工作方式应该与其他任何 Hadoop-on-Linux 安装完全相同。 本文档指出了应注意的具体差异。

## <a name="prerequisites"></a>先决条件

此文档的许多步骤都使用以下实用程序，可能需要在系统上安装这些实用程序。

* [cURL](https://curl.haxx.se/) - 用于与基于 Web 的服务进行通信。
* 命令行 JSON 处理程序 **jq**。  请参阅 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) - 用于远程管理 Azure 服务。
* **SSH 客户端**。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="users"></a>用户

除非[加入域](./domain-joined/hdinsight-security-overview.md)，HDInsight 应被视为**单用户**系统。 单一 SSH 用户帐户是使用具有管理员级别权限的群集创建的。 可以创建其他 SSH 帐户，但这些帐户也具有对群集的管理员访问权限。

加入域的 HDInsight 支持多个用户、更具体的权限以及角色设置。 有关详细信息，请参阅[管理已加入域的 HDInsight 群集](./domain-joined/apache-domain-joined-manage.md)。

## <a name="domain-names"></a>域名

从 Internet 连接到群集时要使用的完全限定域名 (FQDN) 是 `CLUSTERNAME.azurehdinsight.net` 或 `CLUSTERNAME-ssh.azurehdinsight.net`（仅适用于 SSH）。

在内部，群集中每个节点都具有一个名称，该名称在群集配置期间分配。 若要查找群集名称，请参阅 Ambari Web UI 上的“主机”页面。 还可以使用以下方法从 Ambari REST API 返回主机列表：

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

将 `CLUSTERNAME` 替换为群集的名称。 出现提示时，请输入管理员帐户的密码。 此命令返回包含群集中主机列表的 JSON 文档。 [jq](https://stedolan.github.io/jq/) 用于为每个主机提取 `host_name` 元素值。

如果要查找某个特定服务的节点名称，可以在 Ambari 中查询该组件。 例如，若要查找 HDFS 名称节点的主机，请使用以下命令：

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

此命令会返回一个描述该服务的 JSON 文档，然后 [jq](https://stedolan.github.io/jq/) 就会只拉取主机的 `host_name` 值。

## <a name="remote-access-to-services"></a>对服务的远程访问

* **Ambari (web)**  - https://CLUSTERNAME.azurehdinsight.net

    使用群集管理员用户和密码进行身份验证，并登录到 Ambari。

    身份验证是纯文本身份验证 - 始终使用 HTTPS 来帮助确保连接是安全的。

    > [!IMPORTANT]  
    > 某些 Web UI 可使用内部域名通过 Ambari 访问节点。 内部域名不可通过 Internet 公开访问。 在尝试通过 Internet 访问某些功能时，可能会收到“找不到服务器”错误。
    >
    > 要使用 Ambari web UI 的全部功能，请使用 SSH 隧道通过代理将 Web 流量传送到群集头节点。 请参阅[使用 SSH 隧道访问 Apache Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 和其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)**  - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > 通过使用群集管理员用户和密码进行身份验证。
    >
    > 身份验证是纯文本身份验证 - 始终使用 HTTPS 来帮助确保连接是安全的。

* **WebHCat (Templeton)**  - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > 通过使用群集管理员用户和密码进行身份验证。
    >
    > 身份验证是纯文本身份验证 - 始终使用 HTTPS 来帮助确保连接是安全的。

* 端口22或 23**上的 CLUSTERNAME-ssh.azurehdinsight.net** 。 端口 22 用于连接主头结点，而端口 23 用于连接辅助头结点。 有关头节点的详细信息，请参阅 [HDInsight 中的 Apache Hadoop 群集的可用性和可靠性](hdinsight-high-availability-linux.md)。

    > [!NOTE]  
    > 只能通过 SSH 从客户端计算机访问群集头节点。 连接后，可以通过使用 SSH 从头节点访问辅助角色节点。

有关详细信息，请参阅 [HDInsight 上的 Apache Hadoop 服务使用的端口](hdinsight-hadoop-port-settings-for-services.md)文档。

## <a name="file-locations"></a>文件位置

Hadoop 相关文件可在群集节点上的 `/usr/hdp` 中找到。 此目录包含以下子目录：

* **2.6.5.3006-29**：目录名称是 HDInsight 使用的 Hadoop 平台版本。 群集上的数字可能与这里列出的有所不同。
* **current**：此目录包含 **2.6.5.3006-29** 目录下的子目录的链接。 由于该目录存在，因此，无需记住版本号。

可以在 Hadoop 分布式文件系统上的 `/example` 和 `/HdiSamples` 处找到示例数据和 JAR 文件。

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS、Azure 存储和 Data Lake Storage

在大部分 Hadoop 发行版中，数据都存储在 HDFS 中，HDFS 由群集中计算机上的本地存储提供支持。 对基于云的解决方案使用本地存储可能费用高昂，因为计算资源以小时或分钟为单位来计费。

使用 HDInsight 时，数据文件使用 Azure Blob 存储以及可选的 Azure Data Lake Storage 以可缩放和复原的方式存储在云中。 这些服务提供以下优势：

* 成本低廉的长期存储。
* 可从外部服务访问，例如网站、文件上传/下载实用程序、各种语言 SDK 和 Web 浏览器。
* 大型文件容量和大型可缩放存储。

有关详细信息，请参阅[了解 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 和 [Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)。

使用 Azure 存储或 Data Lake Storage 时，不需要从 HDInsight 进行任何特殊操作即可访问数据。 例如，以下命令将列出 `/example/data` 文件夹中的文件，无论它是存储在 Azure 存储还是 Data Lake Storage 上：

    hdfs dfs -ls /example/data

在 HDInsight 中，数据存储资源（Azure Blob 存储和 Azure Data Lake Storage）与计算资源相分离。 因此，你可以根据需要创建 HDInsight 群集来执行计算，然后在工作完成后删除该群集，同时，在云存储中安全地将数据文件持久保存所需的任意时长。


### <a name="URI-and-scheme"></a>URI 和方案

某些命令可能需要你在访问文件时会方案指定为 URI 的一部分。 例如，Storm-HDFS 组件就需要指定方案。 使用非默认存储（作为“附加”存储添加到群集的存储）时，必须始终将方案作为 URI 的一部分来使用。

使用 __Azure 存储__时，可以使用以下 URI 方案之一：

* `wasb:///`：使用未加密通信访问默认存储。

* `wasbs:///`：使用加密通信访问默认存储。  仅 HDInsight 3.6 及以上版本支持 wasbs 方案。

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`：与非默认存储帐户通信时使用。 例如，有额外的存储帐户时，或访问存储在可公开访问的存储帐户中的数据时。

使用__Azure Data Lake Storage Gen2__时，请使用以下 URI 方案：

* `abfs://`：使用加密通信访问默认存储。

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`：与非默认存储帐户通信时使用。 例如，有额外的存储帐户时，或访问存储在可公开访问的存储帐户中的数据时。

使用 __Azure Data Lake Storage Gen1__ 时，可以使用以下 URI 方案之一：

* `adl:///`：访问群集的默认 Data Lake Storage。

* `adl://<storage-name>.azuredatalakestore.net/`：与非默认 Data Lake Storage 通信时使用。 还可用于访问 HDInsight 群集根目录外部的数据。

> [!IMPORTANT]  
> 使用 Data Lake Storage 作为 HDInsight 的默认存储时，必须在存储中指定一个用作 HDInsight 存储根目录的路径。 默认路径为 `/clusters/<cluster-name>/`。
>
> 使用 `/` 或 `adl:///` 访问数据时，只能访问存储在群集根目录（例如 `/clusters/<cluster-name>/`）中的数据。 若要在商店中的任意位置访问数据，请使用 `adl://<storage-name>.azuredatalakestore.net/` 格式。

### <a name="what-storage-is-the-cluster-using"></a>群集正在使用哪种存储

可使用 Ambari 检索群集的默认存储配置。 使用以下命令可使用 curl 检索 HDFS 配置信息，并使用 [jq](https://stedolan.github.io/jq/) 进行筛选：

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> 此命令会返回应用到服务器的第一个配置 (`service_config_version=1`)，其中包含此信息。 可能需要列出所有配置版本，以查找最新的版本。

此命令会返回类似于以下 URI 的值：

* `wasb://<container-name>@<account-name>.blob.core.windows.net`（如果使用 Azure 存储帐户）。

    帐户名是 Azure 存储帐户的名称。 容器名称是作为群集存储的根的 blob 容器。

* `adl://home` 如果使用了 Azure Data Lake Storage。 要获取 Data Lake Storage 名称，请使用以下 REST 调用：

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    此命令返回以下主机名：`<data-lake-store-account-name>.azuredatalakestore.net`。

    要获取作为 HDInsight 根目录的存储中的目录，请使用以下 REST 调用：

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    此命令返回如下所示的路径：`/clusters/<hdinsight-cluster-name>/`。

也可以在 Azure 门户中使用以下步骤查找存储信息：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 HDInsight 群集。

2. 在“属性”部分中，选择“存储帐户”。 将显示群集的存储信息。

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>如何从外部 HDInsight 访问文件

可通过多种方法从 HDInsight 群集外部访问数据。 以下是可用于处理数据的实用程序和 SDK 的几个链接：

如果使用 __Azure 存储__，请参阅以下链接，了解访问数据的方法：

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2)：适用于 Azure 的命令行接口命令。 在安装后，使用 `az storage` 命令获取有关使用存储的帮助，或者使用 `az storage blob` 获取特定于 Blob 的命令。
* [blobxfer.py](https://github.com/Azure/blobxfer)：用于 Azure 存储中的 blob 的 python 脚本。
* 各种 SDK：

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [存储 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

如果使用 __Azure Data Lake Storage__，请参阅以下链接，了解访问数据的方法：

* [Web 浏览器](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure CLI](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [针对 Visual Studio 的 Azure Data Lake 工具](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>缩放群集

使用群集缩放功能可动态更改群集使用的数据节点数。 可以在其他作业或进程正在群集上运行时执行缩放操作。  另请参阅[缩放 HDInsight 群集](./hdinsight-scaling-best-practices.md)

不同的群集类型会受缩放操作影响，如下所示：

* **Hadoop**：减少群集中的节点数时，群集中的某些服务将重新启动。 缩放操作会导致正在运行或挂起的作业在缩放操作完成时失败。 可以在操作完成后重新提交这些作业。
* **HBase**：在完成缩放操作后的几分钟内，区域服务器会自动进行平衡。 若要手动均衡区域服务器，请使用以下步骤：

    1. 使用 SSH 连接到 HDInsight 群集。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

    2. 使用以下命令来启动 HBase shell：

            hbase shell

    3. 加载 HBase shell 后，使用以下方法来手动平衡区域服务器：

            balancer

* **Storm**：应在执行缩放操作后重新平衡任何正在运行的 Storm 拓扑。 重新平衡允许拓扑根据群集中的新节点数重新调整并行度设置。 若要重新平衡正在运行的拓扑，请使用下列选项之一：

    * **SSH**：连接到服务器并使用以下命令来重新平衡拓扑：

            storm rebalance TOPOLOGYNAME

        还可以指定参数来替代拓扑原来提供的并行度提示。 例如，`storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` 会将拓扑重新配置为 5 个工作进程，3 个用于 BlueSpout 组件的执行程序和 10 个用于 YellowBolt 组件的执行程序。

    * **Storm UI**：使用以下步骤来重新平衡使用 Storm UI 的拓扑。

        1. 在`https://CLUSTERNAME.azurehdinsight.net/stormui` web 浏览器中打开， `CLUSTERNAME`其中是风暴群集的名称。 如果系统提示，请输入创建群集时指定的 HDInsight 群集管理员 (admin) 名称和密码。
        2. 选择要重新平衡的拓扑，并选择“重新平衡”按钮。 输入执行重新平衡操作前的延迟。

* **Kafka**：执行缩放操作后，应重新均衡分区副本。 有关详细信息，请参阅[通过 Apache Kafka on HDInsight 实现数据的高可用性](./kafka/apache-kafka-high-availability.md)文档。

有关缩放 HDInsight 群集的特定信息，请参阅：

* [使用 Azure 门户管理 HDInsight 中的 Apache Hadoop 群集](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [使用 Azure CLI 管理 HDInsight 中的 Apache Hadoop 群集](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>如何安装 Hue（或其他 Hadoop 组件）？

HDInsight 是一个托管服务。 如果 Azure 检测到群集问题，可以删除有故障的节点，并创建一个节点来取代它。 如果在群集节点上手动安装组件，则发生此操作时，这些组件不会保留。 应该改用 [HDInsight 脚本操作](hdinsight-hadoop-customize-cluster-linux.md)。 脚本操作可用于进行以下更改：

* 安装并配置服务或网站。
* 安装并配置需要在群集中多个节点上更改配置的组件。

脚本操作是 Bash 脚本。 该脚本在群集创建期间运行，用于安装并配置其他组件。 提供了用于安装以下组件的示例脚本：

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

有关开发自己的脚本操作的信息，请参阅[使用 HDInsight 进行脚本操作开发](hdinsight-hadoop-script-actions-linux.md)。

### <a name="jar-files"></a>Jar 文件

某些 Hadoop 技术以自包含 jar 文件形式提供，这些文件是包含函数，用作 MapReduce 作业的一部分，或在 Pig 或 Hive 内部使用。 它们通常不需要进行任何设置，并可以在创建后上传到群集和直接使用。 如需确保组件在群集重置映像后仍存在，可将 jar 文件存储在群集的默认存储（WASB 或 ADL）中。

例如，如果要使用 [Apache DataFu](https://datafu.incubator.apache.org/) 的最新版本，可以下载包含项目的 jar，并将其上传到 HDInsight 群集。 然后按照 DataFu 文档（关于如何从 Pig 或 Hive 中使用它）操作。

> [!IMPORTANT]  
> 一些属于独立 jar 文件的组件是 HDInsight 随附的，但是不在路径中。 若要查找特定组件，可使用以下命令在群集上搜索：
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> 此命令返回任何匹配的 jar 文件的路径。

要使用不同版本的组件，请上传所需版本，并在作业中使用它。

> [!IMPORTANT]
> 完全支持通过 HDInsight 群集提供的组件，Microsoft 支持部门将帮助找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助你进一步排查问题。 这可能导致问题解决，或要求参与可用的开放源代码技术渠道，在该处可找到该技术的深入专业知识。 例如，有许多可以使用的社区站点，例如：[面向 HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)、[https://stackoverflow.com](https://stackoverflow.com)。 此外，Apache 项目在 [https://apache.org](https://apache.org) 上提供了项目站点，例如：[Hadoop](https://hadoop.apache.org/)、[Spark](https://spark.apache.org/)。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Ambari REST API 管理 HDInsight 群集](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](hadoop/hdinsight-use-pig.md)
* [将 MapReduce 作业与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)
