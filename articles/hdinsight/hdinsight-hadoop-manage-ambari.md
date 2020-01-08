---
title: 使用 Ambari Web UI 监视和管理 Azure HDInsight
description: 了解如何使用 Ambari 监视和管理基于 Linux 的 HDInsight 群集。 本文档介绍如何使用 HDInsight 群集随附的 Ambari Web UI。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 2f46f90edcdd1c4cdf7583c7e628aee205b312e1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098662"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>使用 Apache Ambari Web UI 管理 HDInsight 群集

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari 提供简单易用的 Web UI 和 REST API 来简化 Apache Hadoop 群集的管理和监视。 Ambari 包含在 HDInsight 群集上，用于监视群集和进行配置更改。

本文档介绍如何搭配使用 Ambari Web UI 和 HDInsight 群集。

## <a id="whatis"></a>什么是 Apache Ambari？

[Apache Ambari](https://ambari.apache.org) 通过提供易于使用的 Web UI 简化了 Hadoop 管理。 可以使用 Ambari 来管理和监视 Hadoop 群集。 开发人员可以使用 [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) 在其应用程序中集成这些功能。

## <a name="connectivity"></a>连接性

你的 HDInsight 群集`https://CLUSTERNAME.azurehdinsight.net`上提供了 Ambari Web UI，其中`CLUSTERNAME`是你的群集的名称。

> [!IMPORTANT]  
> 连接到 HDInsight 上的 Ambari 需要 HTTPS。 当提示进行身份验证时，请使用在创建群集时提供的管理员帐户名称和密码。

## <a name="ssh-tunnel-proxy"></a>SSH 隧道（代理）

尽管可以直接通过 Internet 访问群集的 Ambari，但 Ambari Web UI 中的某些链接（例如 JobTracker 的链接）并未在 Internet 上公开。 若要访问这些服务，必须创建一个 SSH 隧道。 有关详细信息，请参阅[将 SSH 隧道与 HDInsight 配合使用](hdinsight-linux-ambari-ssh-tunnel.md)。

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]  
> 并非 Ambari Web UI 的所有功能都受 HDInsight 支持。 有关详细信息，请参阅本文档的[不受支持操作](#unsupported-operations)部分。

连接到 Ambari Web UI 时，系统会提示进行页面验证。 请使用在创建群集过程中你使用的群集管理员用户（默认 Admin）和密码。

当该页打开时，请注意顶栏。 此栏中包含以下信息和控件：

![Apache Ambari 仪表板概述](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|项 |描述 |
|---|---|
|Ambari 徽标|打开仪表板，以便可以监视群集。|
|群集名称 # 项操作|显示进行中的 Ambari 操作数目。 选择群集名称或“# 项操作”会显示后台操作列表。|
|# 个警报|显示与群集相关的警告或严重警报（如果有）。|
|仪表板|显示仪表板。|
|Services|群集中服务的信息和配置设置。|
|主机|群集中节点的信息和配置设置。|
|警报|包含信息、警告和严重警报的日志。|
|行政区划|已安装在群集上的软件堆栈/服务、服务帐户信息和 Kerberos 安全性。|
|“管理”按钮|Ambari 管理、用户设置和注销。|

## <a name="monitoring"></a>监视

### <a name="alerts"></a>警报

以下列表包含 Ambari 使用的常见警报状态：

* **正常**
* **警告**
* **严重**
* **未知**

警报（“正常”状态除外）会导致页面顶部以“# 个警报”条目显示警报数目。 选择此条目会显示警报及其状态。

警报已组织成若干个默认组，可以从“警报”页面进行查看。

![Apache Ambari 警报页面摘要](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

可通过使用“操作”菜单并选择“管理警报组”来管理这些组。

![Apache Ambari 管理警报组](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

还可管理警报方式，并通过在“操作”菜单中选择“管理警报通知”创建警报通知。 所有当前通知都会显示。 还可以从此处创建通知。 出现特定的警报/严重性组合时，可通过**电子邮件**或 **SNMP** 发送通知。 例如，可在“YARN 默认设置”组中的任何警报设为“严重”时发送电子邮件消息。

![Apache Ambari 创建警报通知](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

最后，在“操作”菜单中选择“管理警报设置”可设置发送通知前出现警报的次数。 可以使用此设置来防止针对暂时性错误发出通知。

### <a name="cluster"></a>群集

仪表板的“度量值”选项卡包含一系列 Widget，可让你一目了然地轻松监视群集状态。 “CPU 使用率”等多个 Widget 可在单击后提供更多信息。

![具有指标的 Apache Ambari 仪表板](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

“热图”选项卡以绿色到红色的彩色热图显示度量值。

![带有热图的 Apache Ambari 仪表板](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

若要了解群集内节点的详细信息，请选择“主机”。 然后选择感兴趣的具体节点。

![Apache Ambari 主机摘要详细信息](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Services

仪表板上的“服务”边栏可让你快速了解群集上运行的服务的状态。 各种图标用来指示状态或应当采取的操作。 例如，如果某项服务需要再循环，则会显示一个黄色的再循环符号。

![Apache Ambari services 侧栏](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> 所显示的服务因 HDInsight 群集类型和版本而异。 此处显示的服务可能与群集显示的服务不同。

选择一项服务会显示有关该服务的更多详细信息。

![Apache Ambari 服务摘要信息](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>快速链接

某些服务会在页面顶部显示“快速链接”链接。 这可以用于访问特定于服务的 Web UI，例如：

* **作业历史记录** - MapReduce 作业历史记录。
* **Resource Manager** - YARN ResourceManager UI。
* **NameNode** - Hadoop 分布式文件系统 (HDFS) NameNode UI。
* **Oozie Web UI** - Oozie UI。

选择其中任一链接会在浏览器中打开新选项卡，这会显示所选页面。

> [!NOTE]  
> 选择某项服务的**快速链接**条目可能会返回“找不到服务”错误。 如果遇到此错误，则在使用此服务的**快速链接**条目时必须使用 SSH 隧道。 有关信息，请参阅[将 SSH 隧道与 HDInsight 配合使用](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>管理

### <a name="ambari-users-groups-and-permissions"></a>Ambari 用户、组和权限

使用[已加入域](./domain-joined/hdinsight-security-overview.md)的 HDInsight 群集时，支持使用用户、组和权限。 若要深入了解如何在已加入域的群集上使用 Ambari 管理 UI，请参阅[管理已加入域的 HDInsight 群集](./domain-joined/hdinsight-security-overview.md)。

> [!WARNING]  
> 不要在基于 Linux 的 HDInsight 群集上更改 Ambari 监视程序 (hdinsightwatchdog) 的密码。 更改密码将导致无法通过群集使用脚本操作或执行缩放操作。

### <a name="hosts"></a>主机

“主机”页面列出群集中的所有主机。 若要管理主机，请遵循以下步骤。

![Apache Ambari 主机页面概述](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> 对于 HDInsight 群集，不应使用添加、停用和重用主机的功能。

1. 选择要管理的主机。

2. 使用“操作”菜单选择要执行的操作：

    |项 |描述 |
    |---|---|
    |启动所有组件|启动主机上的所有组件。|
    |停止所有组件|停止主机上的所有组件。|
    |重启所有组件|停止然后启动主机上的所有组件。|
    |启用维护模式|隐藏主机的警报。 如果正在执行生成了警报的操作，则应当启用此模式。 例如，停止和启动服务。|
    |关闭维护模式|使主机恢复正常警报。|
    |停止|停止主机上的 DataNode 或 NodeManagers。|
    |Start|启动主机上的 DataNode 或 NodeManagers。|
    |重新启动|停止然后启动主机上的 DataNode 或 NodeManagers。|
    |解除授权|从群集中删除主机。 **请勿在 HDInsight 群集上使用此操作**。|
    |重用|将以前已解除授权的主机添加到群集中。 **请勿在 HDInsight 群集上使用此操作**。|

### <a id="service"></a>服务

在“仪表板”或“服务”页中，使用服务列表底部的“操作”按钮来停止和启动所有服务。

![Apache Ambari 服务操作列表](./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png)

> [!WARNING]  
> 虽然“添加服务”列于该菜单中，但不应将其用于将服务添加到 HDInsight 群集。 群集设置期间应使用脚本操作添加新服务。 有关使用脚本操作的详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

虽然“操作”按钮可以重新启动所有服务，但要启动、停止或重新启动的往往是特定服务。 使用以下步骤来对单个服务执行操作：

1. 从“仪表板”或“服务”页面中选择一个服务。

2. 在“摘要”选项卡的顶部，使用“服务操作”按钮，然后选择要执行的操作。 这会重启所有节点上的服务。

    ![Apache Ambari 单个服务操作](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > 在群集运行时重新启动某些服务可能会生成警报。 若要避免生成警报，可使用“服务操作”按钮来为服务启用**维护模式**，然后再执行重启。

3. 选择某个操作后，页面顶部的“# 项操作”条目的数字便会递增，指出正在进行后台操作。 如果已配置为显示，则会显示后台操作的列表。

   > [!NOTE]  
   > 如果你已为服务启用了**维护模式**，请记得在操作完成后使用“服务操作”按钮来将它禁用。

若要配置服务，请使用以下步骤：

1. 从“仪表板”或“服务”页面中选择一个服务。

2. 选择“配置”选项卡。这会显示当前配置。 同时，还会显示以前的配置列表。

    ![Apache Ambari 服务配置](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. 使用显示的字段修改配置，并选择“保存”。 或者，选择以前的某个配置，并选择“设为当前配置”以回滚到以前的设置。

## <a name="ambari-views"></a>Ambari 视图

Ambari 视图允许开发人员使用 [Apache Ambari 视图框架](https://cwiki.apache.org/confluence/display/AMBARI/Views)将 UI 元素插入 Ambari Web UI。 HDInsight 提供了以下视图和 Hadoop 群集类型：

* Hive 视图：Hive 视图允许直接从 Web 浏览器运行 Hive 查询。 可保存查询、查看结果、将结果保存到群集存储中或将结果下载到本地系统。 有关使用 Hive 视图的详细信息，请参阅[将 Apache Hive 视图与 HDInsight 配合使用](hadoop/apache-hadoop-use-hive-ambari-view.md)。

* Tez 视图：使用 Tez 视图可以更好地理解和优化作业。 可以查看与 Tez 作业的执行情况以及使用了哪些资源有关的信息。

## <a name="unsupported-operations"></a>不受支持操作

HDInsight 上不支持以下 Ambari 操作：

* 移动指标收集器服务。 查看指标收集器服务上的信息时，“服务操作”菜单中的一个可用操作是移动指标收集器。 HDInsight 不支持此操作。

## <a name="next-steps"></a>后续步骤

了解如何将 [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) 与 HDInsight 配合使用。
