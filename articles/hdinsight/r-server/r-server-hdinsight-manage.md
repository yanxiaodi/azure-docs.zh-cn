---
title: 管理 HDInsight 上的 ML Services 群集 - Azure
description: 了解如何在 Azure HDInsight 中管理 ML 服务群集上的各种任务。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: e0ce8b97df6f2d6e95255d3f4dfc9f76fa08a594
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123541"
---
# <a name="manage-ml-services-cluster-on-azure-hdinsight"></a>管理 Azure HDInsight 上的 ML Services 群集

本文介绍如何管理 Azure HDInsight 上的现有 ML 服务群集，以执行以下任务：添加多个并发用户、远程连接到 ML 服务群集、更改计算上下文等。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的机器学习服务群集。 参阅[使用 Azure 门户创建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)，并选择“机器学习服务”作为“群集类型”。

* 安全外壳（SSH）客户端：SSH 客户端可用于远程连接到 HDInsight 群集，并直接在群集上运行命令。 有关详细信息，请参阅 [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)（将 SSH 与 HDInsight 配合使用）。

## <a name="enable-multiple-concurrent-users"></a>允许多个并发用户

通过向运行 RStudio Community 版本的边缘节点添加更多用户，可以为 HDInsight 上的 ML Services 群集启用多个并发用户。 在创建 HDInsight 群集时，必须提供两个用户，即一个 HTTP 用户，一个 SSH 用户：

![HDI Azure 门户登录参数](./media/r-server-hdinsight-manage/hdi-concurrent-users1.png)

- 群集登录用户名：一个通过 HDInsight 网关进行身份验证的 HTTP 用户，该网关用于保护所创建的 HDInsight 群集。 此 HTTP 用户用于访问 Apache Ambari UI、Apache Hadoop YARN UI 以及其他 UI 组件。
- 安全外壳 (SSH) 用户名：一个需通过安全外壳访问群集的 SSH 用户。 此用户是适用于所有头节点、辅助角色节点和边缘节点的 Linux 系统中的用户。 因此，可以使用安全外壳访问远程群集中的任何节点。

在 HDInsight 上的 ML Services 群集中使用的 R Studio Server Community 版本仅接受 Linux 用户名和密码作为登录机制， 而不支持传递令牌。 因此，首次尝试在 ML Services 群集上访问 R Studio 时，需要登录两次。

- 首先，通过 HDInsight 网关使用 HTTP 用户凭据登录。

- 然后，使用 SSH 用户凭据登录到 RStudio。
  
目前，在预配 HDInsight 群集时只能创建一个 SSH 用户帐户。 因此，若要允许多个用户访问 HDInsight 上的 ML Services 群集，必须在 Linux 系统中创建其他用户。

由于 RStudio 在群集的边缘节点上运行，因此需执行下述几个步骤：

1. 使用现有 SSH 用户登录到边缘节点
2. 在边缘节点中添加更多的 Linux 用户
3. 通过已创建的用户使用 RStudio Community 版本

### <a name="step-1-use-the-created-ssh-user-to-sign-in-to-the-edge-node"></a>步骤 1：使用创建的 SSH 用户登录到边缘节点

按照[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md) 中的说明访问边缘节点。 HDInsight 上的 ML Services 群集的边缘节点地址是 `CLUSTERNAME-ed-ssh.azurehdinsight.net`。

### <a name="step-2-add-more-linux-users-in-edge-node"></a>步骤 2：在边缘节点中添加更多的 Linux 用户

若要将用户添加到边缘节点，请执行以下命令：

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user
    sudo passwd <yournewusername>

以下屏幕截图显示了输出。

![屏幕截图输出并发用户](./media/r-server-hdinsight-manage/hdi-concurrent-users2.png)

当系统提示输入“当前 Kerberos 密码:”时，只需按 Enter 将其忽略即可。 `useradd` 命令中的 `-m` 选项指示系统会为用户创建主文件夹，该文件夹是 RStudio Community 版本所需的。

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>步骤 3：通过已创建的用户使用 RStudio Community 版本

从 https://CLUSTERNAME.azurehdinsight.net/rstudio/ 访问 RStudio。 如果是在创建群集后首次登录，请输入群集管理员凭据后跟创建的 SSH 用户凭据。 如果不是首次登录，则仅输入所创建的 SSH 用户的凭据。

也可同时使用原始凭据（默认为 sshuser）从其他浏览器窗口登录。

另请注意，在 Linux 系统中，新添加的用户没有根权限，但对远程 HDFS 和 WASB 存储中的所有文件具有相同的访问权限。

## <a name="connect-remotely-to-microsoft-ml-services"></a>远程连接到 Microsoft ML Services

可从台式机上运行的 ML Client 远程实例设置对 HDInsight Spark 计算上下文的访问权限。 为此，在台式机上定义 RxSpark 计算上下文时必须指定选项（hdfsShareDir、shareDir、sshUsername、sshHostname、sshSwitches 和 sshProfileScript）：例如：

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- '<clustername>-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- '<sshuser>'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

有关详细信息，请参阅 [How to use RevoScaleR in an Apache Spark compute context](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios)（如何在 Apache Spark 计算上下文中使用 RevoScaleR）中的“使用 Microsoft Machine Learning Server 作为 Hadoop 客户端”部分

## <a name="use-a-compute-context"></a>使用计算上下文

借助计算上下文，用户可控制是在边缘节点上本地执行计算，还是将计算分布到 HDInsight 群集的节点之间。  有关使用 RStudio 服务器设置计算上下文的示例，请参阅在[Azure HDInsight 中使用 RStudio 服务器在 ML 服务群集上执行 R 脚本](machine-learning-services-quickstart-job-rstudio.md)。

## <a name="distribute-r-code-to-multiple-nodes"></a>将 R 代码分布到多个节点

使用 HDInsight 上的 ML Services，可利用现有 R 代码并使用 `rxExec` 跨多个群集节点运行该代码。 执行参数扫描或模拟时，可以使用此函数。 以下代码是 `rxExec` 的用法示例：

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

如果仍在使用 Spark 上下文，此命令将针对运行代码 `(Sys.info()["nodename"])` 的工作节点返回 nodename 值。 例如，在四节点群集上，输出应与以下代码片段类似：

    $rxElem1
        nodename
    "wn3-mymlser"

    $rxElem2
        nodename
    "wn0-mymlser"

    $rxElem3
        nodename
    "wn3-mymlser"

    $rxElem4
        nodename
    "wn3-mymlser"

## <a name="access-data-in-apache-hive-and-parquet"></a>访问 Apache Hive 和 Parquet 中的数据

通过 HDInsight ML Services 可直接访问 Hive 和 Parquet 中的数据，以供 Spark 计算上下文中的 ScaleR 函数使用。 这些功能通过名为 RxHiveData 和 RxParquetData 的新 ScaleR 数据源函数提供，它们通过使用 Spark SQL 将数据直接加载到 Spark DataFrame 中，供 ScaleR 进行分析。

以下代码是一些示例代码，演示了如何使用新函数：

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


有关如何使用这些新函数的其他信息，请使用 `?RxHivedata` 和 `?RxParquetData` 命令查看 ML Services 中的联机帮助。  

## <a name="install-additional-r-packages-on-the-cluster"></a>在群集上安装其他 R 包

### <a name="to-install-r-packages-on-the-edge-node"></a>在边缘节点上安装 R 包

若要在边缘节点上安装其他 R 包，可以在通过 SSH 连接到边缘节点后，直接从 R 控制台中使用 `install.packages()`。 

### <a name="to-install-r-packages-on-the-worker-node"></a>在工作节点上安装 R 包

若要在群集的工作节点上安装 R 包，必须使用脚本操作。 脚本操作为 Bash 脚本，用于更改 HDInsight 群集的配置或安装其他软件（例如其他 R 包）。 

> [!IMPORTANT]  
> 只有在创建群集之后，才可以使用脚本操作来安装其他 R 包。 请勿在群集创建期间使用此过程，因为脚本依赖于配置完毕的 ML Services。

1. 执行[使用脚本操作自定义群集](../hdinsight-hadoop-customize-cluster-linux.md)中的步骤。

3. 对于“提交脚本操作”，提供以下信息：

   * 对于“脚本类型”，选择“自定义”。

   * 对于“名称”，为脚本操作提供一个名称。

     * 对于“Bash 脚本 URI”，输入 `https://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`。 此脚本会在工作节点上安装其他 R 包

   * 仅选中“辅助角色”所对应的复选框。

   * **参数**：要安装的 R 包。 例如： `bitops stringr arules`

   * 选中“持久保存此脚本操作”复选框。  

   > [!NOTE]
   > 1. 默认情况下，将从与安装的 ML Server 版本一致的 Microsoft MRAN 存储库快照中安装所有 R 包。 若要安装较新版的包，则可能存在不兼容的风险。 不过，将 `useCRAN` 指定为包列表的第一个元素（例如 `useCRAN bitops, stringr, arules`）即可完成此类安装。  
   > 2. 某些 R 包需要额外的 Linux 系统库。 为方便起见，已预先安装了 HDInsight ML Services，其中包含最常用的 100 个 R 包所需的依赖项。 但是，如果安装的 R 包需要除此之外的库，则必须下载此处使用的基本脚本，并添加安装系统库的步骤。 接下来，必须将修改的脚本上传到 Azure 存储中的公共 Blob 容器，并使用修改的脚本来安装包。
   >    有关开发脚本操作的详细信息，请参阅[脚本操作开发](../hdinsight-hadoop-script-actions-linux.md)。

   ![Azure 门户提交脚本操作](./media/r-server-hdinsight-manage/submit-script-action.png)

4. 选择“创建”运行脚本。 脚本完成后，可在所有辅助角色节点上使用 R 包。

## <a name="next-steps"></a>后续步骤

* [使 HDInsight 上的 ML Services 群集开始运转](r-server-operationalize.md)
* [适用于 HDInsight 上的 ML Services 群集的计算上下文选项](r-server-compute-contexts.md)
* [适用于 HDInsight 上的 ML Services 群集的 Azure 存储选项](r-server-storage.md)
