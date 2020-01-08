---
title: 调试 Apache Hadoop：查看日志和解释错误消息 - Azure HDInsight
description: 了解在使用 PowerShell 管理 HDInsight 时可能会收到的错误消息，以及恢复正常所需采取的步骤。
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 8ad2bdd0f12abad08515f0314b9c03cc971127cb
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059219"
---
# <a name="analyze-apache-hadoop-logs-in-azure-hdinsight"></a>在 Azure HDInsight 中分析 Apache Hadoop 日志

Azure HDInsight 中的每个 Apache Hadoop 群集都有一个用作默认文件系统的 Azure 存储帐户。 该存储帐户称作默认存储帐户。 群集使用默认存储帐户上的 Azure 表存储和 Blob 存储来存储其日志。  若要了解群集的默认存储帐户，请参阅[在 HDInsight 中管理 Apache Hadoop 群集](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts)。 即使在删除群集以后，日志仍会保留在存储帐户中。

## <a name="logs-written-to-azure-tables"></a>写入到 Azure 表的日志

使用写入到 Azure 表的日志，可以在一定程度上了解 HDInsight 群集中发生的事件。

创建 HDInsight 群集时，会自动在默认表存储中为基于 Linux 的群集创建六个表：

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

表文件名为 **u\<ClusterName>DDMonYYYYatHHMMSSsss\<TableName>** 。

这些表包含以下字段：

* ClusterDnsName
* ComponentName
* EventTimestamp
* 主机
* MALoggingHash
* 消息
* N
* PreciseTimeStamp
* Role
* RowIndex
* 租户
* TIMESTAMP
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>用于访问日志的工具
访问这些表中的数据可以使用许多工具：

* Visual Studio
* Azure 存储资源管理器
* Power Query for Excel

#### <a name="use-power-query-for-excel"></a>使用 Power Query for Excel
可以从 [Microsoft Power Query for Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379) 安装 Power Query。 有关系统要求，请参阅下载页。

**使用 Power Query 打开和分析服务日志**

1. 打开 **Microsoft Excel**。
2. 在“Power Query”菜单中依次单击“来自 Azure”和“来自 Microsoft Azure 表存储”。
   
    ![HDInsight Hadoop Excel PowerQuery 打开 Azure 表存储](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. 输入存储帐户名称，短名称或 FQDN。
4. 输入存储帐户密钥。 会看到一系列表：
   
    ![存储在 Azure 表存储中的 HDInsight Hadoop 日志](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. 右键单击“导航器”窗格中的 hadoopservicelog 表，并选择“编辑”。 应看到四个列。 （可选）删除“分区键”、“行键”和“时间戳”列，方法是：选中这些项，并在功能区的选项中单击“删除列”。
6. 单击“内容”列上的展开图标，选择要导入 Excel 电子表格中的列。 我选择了 TraceLevel 和 ComponentName 进行本次演示：这样我就可以大致知道哪些组件有问题。
   
    ![HDInsight Hadoop 日志选择列 excel](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png "HDInsight Hadoop 日志选择列 excel")
7. 单击“确定”导入数据。
8. 选择“TraceLevel”、“角色”和“ComponentName”列，并单击功能区中的“分组依据”控件。
9. 单击“分组依据”对话框中的“确定”
10. 单击“应用并关闭”。

现在可以根据需要使用 Excel 来筛选和排序。 你可能想要包括其他列（例如“消息”列），以便在出现问题时对其进行更深入的分析，但选择上述列并对其分组以后，已经可以基本了解 Hadoop 服务的情况。 setuplog 和 hadoopinstalllog 表也是如此。

#### <a name="use-visual-studio"></a>使用 Visual Studio
**使用 Visual Studio**

1. 打开 Visual Studio。
2. 在“视图”菜单中，单击“云资源管理器”。 也可直接单击“CTRL+\,”或“CTRL+X”。
3. 在“云资源管理器”中，选择“资源类型”。  其他可用选项是“资源组”。
4. 依次展开“存储帐户”、群集的默认存储帐户、“表”。
5. 双击“hadoopservicelog”。
6. 添加筛选器。 例如：
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop 日志选择列与](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png "HDInsight Hadoop 日志选择列与")
   
    有关构造筛选器的详细信息，请参阅[构造表设计器的筛选器字符串](../../vs-azure-tools-table-designer-construct-filter-strings.md)。

## <a name="logs-written-to-azure-blob-storage"></a>写入 Azure Blob 存储的日志
使用写入到 Azure 表的日志，可以在一定程度上了解 HDInsight 群集中发生的事件。 但是，这些表不提供任务级日志，这些日志在问题发生时可以用于进一步分析问题。 为了更进一步地详细了解所发生的问题，可以对 HDInsight 群集进行配置，将通过 Templeton 提交的作业的任务日志写入 Blob 存储帐户。 实际上，这是指通过 Microsoft Azure PowerShell cmdlet 或 .NET 作业提交 API 提交的作业，而不是指通过 RDP 提交的或通过命令行访问群集时提交的作业。 

若要查看日志，请参阅[在基于 Linux 的 HDInsight 上访问 Apache Hadoop YARN 应用程序日志](../hdinsight-hadoop-access-yarn-app-logs-linux.md)。


有关应用程序日志的详细信息，请参阅 [Simplifying user-logs management and access in Apache Hadoop YARN](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/)（简化 Apache Hadoop YARN 中的用户日志管理和访问）。


## <a name="view-cluster-health-and-job-logs"></a>查看群集运行状况和作业日志
### <a name="access-the-ambari-ui"></a>访问 Ambari UI
在 Azure 门户中，单击某个 HDInsight 群集名称以打开群集窗格。 在群集窗格中，单击“仪表板”。

![HDInsight 启动群集仪表板](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>访问 Yarn UI
在 Azure 门户中，单击某个 HDInsight 群集名称以打开群集窗格。 在群集窗格中，单击“仪表板”。 出现提示时，输入群集管理员凭据。 在 Ambari 中，从左侧的服务列表中选择 **YARN**。 在显示的页面上，选择“快速链接”，然后选择活动头节点条目和资源管理器 UI。

可使用 YARN UI 执行以下操作：

* **获取群集状态**。 在左窗格中展开“群集”，并单击“关于”。 此时会显示群集状态详细信息，例如总分配内存、所用核心数、群集资源管理器状态、群集版本等。
  
    ![HDInsight 启动群集仪表板 yarn](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "HDInsight 启动群集仪表板 yarn")
* **获取节点状态**。 在左窗格中展开“群集”，并单击“节点”。 此时会列出群集中的所有节点、每个节点的 HTTP 地址、分配给每个节点的资源等。
* **监视作业状态**。 在左窗格中展开“群集”，并单击“应用程序”列出群集中的所有作业。 若要查看特定状态（例如“新”、“已提交”、“正在运行”等）的作业，请单击“应用程序”下的相应链接。 可以进一步单击作业名称以查找该作业的详细信息，例如输出、日志等。

### <a name="access-the-hbase-ui"></a>访问 HBase UI
在 Azure 门户中，单击某个 HDInsight HBase 群集名称以打开群集窗格。 在群集窗格中，单击“仪表板”。 出现提示时，输入群集管理员凭据。 在 Ambari 中，从服务列表中选择“HBase”。 在页面顶部选择“快速链接”，指向活动 Zookeeper 节点链接，并单击“HBase Master UI”。

## <a name="hdinsight-error-codes"></a>HDInsight 错误代码
本部分中列举的错误消息旨在帮助 Azure HDInsight 中的 Hadoop 用户了解在使用 Azure PowerShell 管理服务时可能会遇到的错误情况，并向他们建议要从错误中恢复可以执行哪些步骤。

其中某些错误消息也可以在使用 Azure 门户管理 HDInsight 群集时在该门户中看到。 但是，由于在此上下文中针对可能的补救措施的约束，可能会遇到的其他一些错误消息可能不是很精细。 将在问题得到明显缓解的上下文中提供其他错误消息。 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **说明**：请至少为一个组件提供 Azure SQL 数据库详细信息，以便对 Hive 和 Oozie 元存储使用自定义设置。
* **缓解措施**：用户需要提供有效的 SQL Azure 元存储并且重试该请求。  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **说明**：无法在区域 *nameOfYourRegion* 中创建群集。 使用有效的 HDInsight 区域并且重试请求。
* **缓解措施**：用户应该创建当前支持它们的群集区域：东南亚、西欧、北欧、美国东部或美国西部。  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **说明**：服务器无法找到请求的群集记录。  
* **缓解措施**：请重试操作即可。

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **说明**：群集 DNS 名称 *yourDnsName* 无效。 请确保名称以字母数字开头和结尾，并且只能包含“-”特殊符号  
* **缓解措施**：确保将有效的 DNS 名称用于群集（该名称以字母数字开头和结尾，并且不包含除短划线“-”之外的任何特殊字符），然后重试操作。

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **说明**：群集名称 *yourClusterName* 不可用。 请选取另一个名称。  
* **缓解措施**：用户应该指定唯一且不存在的群集名称，然后重试。 如果用户正在使用门户，则 UI 将通知他们该群集名称是否已在创建步骤期间使用。

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **说明**：群集密码无效。 密码的长度必须至少为 10 个字符，并且必须包含至少一个数字、大写字母、小写字母和特殊字符且没有空格，不应包含用户名作为密码的一部分。  
* **缓解措施**：提供有效的群集密码并且重试操作。

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **说明**：群集用户名无效。 请确保用户名不包含特殊字符或空格。  
* **缓解措施**：提供有效的群集用户名并且重试操作。

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **说明**：群集 DNS 名称 *yourDnsClusterName* 无效。 请确保名称以字母数字开头和结尾，并且只能包含“-”特殊符号  
* **缓解措施**：提供有效的 DNS 群集用户名并且重试操作。

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **说明**：URI *yourcontainerURI* 中的容器名称和请求正文中的 DNS 名称 *yourDnsName* 必须相同。  
* **缓解措施**：确保你的容器名称和 DNS 名称相同并且重试操作。

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **说明**：无效的群集配置。 在节点大小中找不到任何数据节点定义。  
* **缓解措施**：请重试操作即可。

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **说明**：为群集删除部署失败  
* **缓解措施**：重试删除操作。

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **说明**：服务配置错误。 未找到请求的 DNS 映射信息。  
* **缓解措施**：删除群集并且创建一个新群集。

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **说明**：重复群集容器创建尝试。 存在针对 *nameOfYourContainer* 的记录，但 Etag 不匹配。
* **缓解措施**：为容器提供唯一名称并且重试创建操作。

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **说明**：托管服务 *nameOfYourHostedService* 已包含群集。 托管服务不能包含多个群集  
* **缓解措施**：在其他托管服务中托管群集。

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **说明**：服务器无法更新群集部署的状态。  
* **缓解措施**：请重试操作即可。 如果此情况多次发生，请与 CSS 联系。

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **说明**：在维护过程中删除了群集 *yourClusterName*。 请重新创建群集。
* **缓解措施**：重新创建群集。

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **说明**：无效的群集配置。 在节点大小中找不到所需头节点配置。
* **缓解措施**：请重试操作即可。

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **说明**：无法创建托管服务 *nameOfYourHostedService*。 请重试请求。  
* **缓解措施**：重试请求。

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **说明**：托管服务 *nameOfYourHostedService* 已有生产部署。 托管服务不能包含多个生产部署。 请使用不同的群集名称重试请求。
* **缓解措施**：使用不同的群集名称重试请求。

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **说明**：无法找到群集的托管服务 *nameOfYourHostedService*。  
* **缓解措施**：如果该群集处于错误状态，则删除该群集，然后重试。

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **说明**：托管服务 *nameOfYourHostedService* 没有关联的部署。  
* **缓解措施**：如果该群集处于错误状态，则删除该群集，然后重试。

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **说明**：SubscriptionId *yourSubscriptionId* 没有可供创建群集 *yourClusterName* 的内核。 必需：*resourcesRequired*，可用：*resourcesAvailable*。  
* **缓解措施**：释放订阅中的资源或者增加可用于订阅的资源，然后尝试再次创建群集。

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **说明**：订阅 ID *yourSubscriptionId* 没有用于新 HostedService 的配额来创建群集 *yourClusterName*。  
* **缓解措施**：释放订阅中的资源或者增加可用于订阅的资源，然后尝试再次创建群集。

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **说明**：服务器遇到内部错误。 请重试请求。  
* **缓解措施**：重试请求。

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **说明**：Azure 存储位置 *dataRegionName* 不是有效位置。 请确保区域正确并重试请求。
* **缓解措施**：选择支持 HDInsight 的存储位置，检查群集是否是共置的，然后重试操作。

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **说明**：数据节点的 VM 大小无效。 所有数据节点仅支持“大型 VM”大小。  
* **缓解措施**：指定数据节点支持的节点大小并且重试操作。

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **说明**：头节点的 VM 大小无效。 头节点仅支持“特大型 VM”大小。  
* **缓解措施**：为头节点指定支持的节点大小并且重试操作

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **说明**：要使用的订阅 ID *yourSubscriptionId* 没有对群集 *yourClusterName* 执行删除操作所需的足够权限。  
* **缓解措施**：如果该群集处于错误状态，则删除该群集，然后重试。  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **说明**：外部存储帐户 Blob 容器名称 *yourContainerName* 无效。 请确保名称以字母开头，并且仅包含小写字母、数字和短划线。  
* **缓解措施**：指定有效的存储帐户 Blob 容器名称并且重试操作。

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **说明**：若要设置密钥详细信息，需要对外部存储帐户 *yourStorageAccountName* 进行配置。  
* **缓解措施**：指定存储帐户的有效密钥并且重试操作。

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **说明**：版本标头 *yourVersionHeader* 的格式不是有效的 yyyy-mm-dd 格式。  
* **缓解措施**：为版本标头指定有效格式并且重试请求。

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **说明**：无效的群集配置。 找到了多个头节点配置。  
* **缓解措施**：对配置进行编辑，仅指定一个头节点。

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **说明**：无法在允许的时间内或可能的最大重试尝试次数内完成该操作。 请重试请求。  
* **缓解措施**：重试请求。

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **说明**：参数 *yourParameterName* 不能为 Null 或空。  
* **缓解措施**：为该参数指定有效值。

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **说明**：一个或多个群集创建请求输入无效。 请确保输入值正确，并重试请求。  
* **缓解措施**：请确保输入值正确，并重试请求。

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **说明**：区域容量不可用于区域 *yourRegionName* 和订阅 ID *yourSubscriptionId*。  
* **缓解措施**：指定支持 HDInsight 群集的区域。 公开支持的区域有：东南亚、西欧、北欧、美国东部或美国西部。

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **说明**：存储帐户 *yourStorageAccountName* 位于区域 *currentRegionName* 中。 它应该与群集区域 *yourClusterRegionName* 相同。  
* **缓解措施**：在群集所在区域中指定存储帐户；或者如果数据已在该存储帐户中，则在现有存储帐户所在区域中创建新群集。 如果在使用门户，则 UI 会事先通知存在此问题。

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **说明**：给定的订阅 ID *yourSubscriptionId* 不是活动的。  
* **缓解措施**：重新激活订阅或者获取新的有效订阅。

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **说明**：找不到订阅 ID *yourSubscriptionId*。  
* **缓解措施**：检查订阅 ID 是否有效并且重试操作。

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **说明**：无法解析 DNS *yourDnsUrl*。 请确保提供针对 Blob 终结点的完全限定 URL。  
* **缓解措施**：提供有效的 Blob URL。 该 URL 必须完全有效，包括以 *http://* 开头和 *.com* 结尾。

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **说明**：无法验证资源 *yourDnsUrl* 的位置。 请确保提供针对 Blob 终结点的完全限定 URL。  
* **缓解措施**：提供有效的 Blob URL。 该 URL 必须完全有效，包括以 *http://* 开头和 *.com* 结尾。

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **说明**：版本功能不可用于版本 *specifiedVersion* 和订阅 ID *yourSubscriptionId*。  
* **缓解措施**：选择一个可用版本并且重试操作。

### <a id="VersionNotSupported"></a>VersionNotSupported
* **说明**：版本 *specifiedVersion* 不受支持。
* **缓解措施**：选择一个支持的版本并且重试操作。

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **说明**：版本 *specifiedVersion* 在 Azure 区域 *specifiedRegion* 中不可用。  
* **缓解措施**：选择一个在指定区域中受支持的版本并且重试操作。

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **说明**：无效的群集配置。 在外部帐户中找不到所需的 WASB 帐户配置。  
* **缓解措施**：确认该帐户存在并且在配置中正确指定，然后重试操作。

## <a name="next-steps"></a>后续步骤

* [在基于 Linux 的 HDInsight 上为 Apache Hadoop 服务启用堆转储](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [使用 Apache Ambari Web UI 管理 HDInsight 群集](../hdinsight-hadoop-manage-ambari.md)
