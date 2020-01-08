---
title: 航天工业预见性维护指南 - Team Data Science Process
description: 在航天工业、实用工具和运输业中用于预见性维护的 Microsoft Cortana Intelligence 解决方案模板的技术指南。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 37acd88dbfe8ed1790f1bfa68d5e9762a861ddc7
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626403"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace"></a>在航天工业中用于预见性维护的 Cortana Intelligence 解决方案模板技术指南

> [!Important]
> 此文已弃用。 讨论仍与航天工业的预测性维护相关，但如果需要最新信息，请参阅[面向商业受众的解决方案概述](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace)。


解决方案模板可加速在 Cortana Intelligence Suite 上构建 E2E 演示的过程。 已部署的模板使用所需的 Cortana Intelligence 组件预配订阅，并在两者之间建立关系。 它还使用从数据生成器应用程序中的数据示例来设定数据管道的种子（会在部署解决方案模板后在本地计算机上下载和安装数据生成器应用程序）。 生成器中的数据将生成数据管道，并开始生成机器学习预测，然后可在 Power BI 仪表板上将其可视化。

部署过程会逐步引导用户完成设置解决方案凭据的多个步骤。 请务必记下这些凭据，例如在部署期间提供的解决方案名称、用户名和密码。 


本文的目标是：
- 介绍订阅中预配的参考体系结构和组件。
- 演示如何将示例数据替换为自己的数据。 
- 演示如何修改解决方案模板。  

> [!TIP]
> 可下载和打印[本文的 PDF 版本](https://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf)。
> 
> 

## <a name="overview"></a>概述
![预见性维护体系结构](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

部署解决方案时，会激活 Cortana Analytics Suite 中的 Azure 服务（包括事件中心、流分析、HDInsight、数据工厂和机器学习）。 体系结构图展示了如何构造航天工业预测性维护解决方案模板。 可以通过在与解决方案部署一起创建的解决方案模板图表上单击 Azure 门户中的服务对其进行调查（HDInsight 除外，该服务是在要求相关管道活动运行时按需预配的，且随后进行了删除）。
下载[图表的完全尺寸版本](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png)。

以下部分介绍解决方案部分。

## <a name="data-source-and-ingestion"></a>数据源和引入
### <a name="synthetic-data-source"></a>综合数据源
对于此模板，使用的数据源是从桌面应用程序生成的，将下载应用程序并于部署成功后在本地运行。

若要查找有关下载及安装此应用程序的说明，请在解决方案模板图表上选择第一个节点“预测性维护数据生成器”。 可在“属性”栏中找到说明。 此应用程序会在解决方案流的余下部分使用的数据点或事件送入 [Azure 事件中心](#azure-event-hub)服务。 此数据源是使用 [Turbofan 引擎下降模拟数据集](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) 由 [NASA 数据存储库](https://c3.nasa.gov/dashlink/resources/139/)中的公开可用数据派生的。

仅当它在计算机上运行时，事件生成应用程序才填充 Azure 事件中心。  

### <a name="azure-event-hub"></a>Azure 事件中心  
[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)服务是综合数据源提供的输入接收者。

## <a name="data-preparation-and-analysis"></a>数据准备和分析  
### <a name="azure-stream-analytics"></a>Azure 流分析
使用 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)对 [Azure 事件中心](#azure-event-hub)服务的输入流提供近乎实时的分析。 然后可将结果发布到 [Power BI](https://powerbi.microsoft.com) 仪表板，在 [Azure 存储服务](https://azure.microsoft.com/services/storage/)中存档所有原始传入事件，供 [Azure 数据工厂](https://azure.microsoft.com/documentation/services/data-factory/)服务做后续处理。

### <a name="hdinsight-custom-aggregation"></a>HDInsight 自定义聚合
使用 HDInsight 运行 [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本（由 Azure 数据工厂协调），提供使用 Azure 流分析服务存档的原始事件聚合。

### <a name="azure-machine-learning"></a>Azure 机器学习
使用 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)服务（由 Azure 数据工厂协调）预测特定飞机引擎（根据收到的输入数据）的剩余使用寿命 (RUL)。 

## <a name="data-publishing"></a>数据发布
### <a name="azure-sql-database"></a>Azure SQL 数据库
使用 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)存储在 [Power BI](https://powerbi.microsoft.com) 仪表板中使用的 Azure 机器学习服务收到的预测数据。

## <a name="data-consumption"></a>数据使用
### <a name="power-bi"></a>Power BI
使用 [Power BI](https://powerbi.microsoft.com) 显示仪表板，其中包含 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)提供的聚合和警报，以及 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)中存储的、使用 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)生成的 RUL 预测。

## <a name="how-to-bring-in-your-own-data"></a>如何输入自己的数据
本部分说明如何将自己的数据输入 Azure，以及对于放入此体系结构的数据，需要更改哪些方面。

你的数据集不可能符合用于此解决方案模板的 [Turbofan 引擎降级模拟数据集](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)所使用的数据集。 了解数据与需求对于如何修改此模板以配合自己的数据非常重要。 

以下部分介绍引入新数据集需要修改的模板部分。

### <a name="azure-event-hub"></a>Azure 事件中心
Azure 事件中心支持丰富的格式，可以使用 CSV 或 JSON 格式将数据发布到中心。 Azure 事件中心对数据不做任何特殊处理，但用户必须了解提供的数据。

本文档不会介绍如何引入数据，但可以使用事件中心 API，轻松将事件或数据发送到 Azure 事件中心。

### <a name="azure-stream-analytics-1"></a>Azure Stream Analytics
可以使用 Azure 流分析服务从数据流读取数据并将数据输出到任意数量的源，从而提供近实时分析。

对于航天工业预测性维护的解决方案模板，Azure 流分析查询包含四个子查询，每个查询均从 Azure 事件中心服务使用事件，并且输出到四个不同的位置。 这些输出包括三个 Power BI 数据集和一个 Azure 存储位置。

可通过以下方式找到 Azure 流分析查询：

* 连接到 Azure 门户
* 查找部署解决方案时生成的流分析作业![流分析图标](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png)（例如  ，预测性维护解决方案的 **maintenancesa02asapbi** 和 **maintenancesa02asablob**）
* 选择
  
  * “输入”可查看查询输入
  * “查询”可查看查询本身
  * “输出”可查看不同的输出

有关 Azure 流分析查询构造的信息，请参阅 MSDN 上的 [Stream Analytics Query Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)（流分析查询参考）。

在此解决方案中，查询将三个数据集（具有近乎实时的传入数据流相关分析信息）输出到作为此解决方案模板中的一部分提供的 Power BI 仪表板。 由于对传入数据格式有隐含的了解，必须根据数据格式更改这些查询。

第二个流分析作业 **maintenancesa02asablob** 中的查询将所有[事件中心](https://azure.microsoft.com/services/event-hubs/)事件输出到 [Azure 存储](https://azure.microsoft.com/services/storage/)，由于将完整的事件信息流输出到存储，因此无论数据格式为何都无需进行修改。

### <a name="azure-data-factory"></a>Azure 数据工厂
[Azure 数据工厂](https://azure.microsoft.com/documentation/services/data-factory/)服务协调数据的移动和处理。 在航天工业预测性维护的解决方案模板中，数据工厂由 3 个[管道](../../data-factory/concepts-pipelines-activities.md)组成，使用不同的技术移动和处理数据。  可以打开随解决方案部署创建的解决方案模板图示底部的数据工厂节点来访问数据工厂。 数据集下的错误之所以发生，是因为启动数据生成器之前已部署数据工厂。 这些错误可以忽略，不会防碍数据工厂的正常运行

![数据工厂数据集错误](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

本部分介绍了 [Azure 数据工厂](https://azure.microsoft.com/documentation/services/data-factory/)中的必要[管道和活动](../../data-factory/concepts-pipelines-activities.md)。 下面是解决方案的图示视图。

![Azure 数据工厂](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

此工厂的两个管道包含 [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本，用于分区及聚合数据。 可以看到，脚本位在安装期间创建的 [Azure 存储](https://azure.microsoft.com/services/storage/)帐户中。 其位置为：maintenancesascript\\\\script\\\\hive\\\\（或 https://[解决方案名称].blob.core.windows.net/maintenancesascript）。

类似于 [Azure 流分析](#azure-stream-analytics-1)查询，[Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本对传入的数据格式有隐含了解，必须根据数据格式进行更改。

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
该[管道](../../data-factory/concepts-pipelines-activities.md)包含单个活动 - [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) 活动，其使用 [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) 运行 [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本，在 [Azure 流分析作业](https://azure.microsoft.com/services/stream-analytics/)期间对放入 [Azure 存储](https://azure.microsoft.com/services/storage/)的数据进行分区。

此分区任务的 [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本为 ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
此[管道](../../data-factory/concepts-pipelines-activities.md)包含多个活动，其最终结果为来自与此解决方案模板关联的 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)试验评分的预测。

包含的活动有：

* 使用 [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) 的 [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) 活动运行 [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本来执行 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)试验所需的聚合及特征设计。
  此分区任务的 [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本为 ***PrepareMLInput.hql***。
* [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) 活动将来自 [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) 活动的结果移到可供 [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) 活动访问的单个 [Azure 存储](https://azure.microsoft.com/services/storage/) Blob。
* [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) 活动调用 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)试验，将结果放入单个 [Azure 存储](https://azure.microsoft.com/services/storage/) Blob。

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
此[管道](../../data-factory/concepts-pipelines-activities.md)包含单个活动 - [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) 活动，将 [Azure 机器学习](#azure-machine-learning)试验的结果从 ***MLScoringPipeline*** 移到随解决方案模板安装一起预配的 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)。

### <a name="azure-machine-learning"></a>Azure 机器学习
用于此解决方案模板的 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)试验提供了飞机引擎的剩余使用寿命 (RUL)。 该试验因使用的数据集而有所不同，需要专门针对引入的数据进行修改或替换。

有关 如何创建 Azure 机器学习试验的信息，请参阅[预见性维护：步骤 1（共 3 步），数据准备和特征设计](https://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)。

## <a name="monitor-progress"></a>监视进度
启动数据生成器后，管道将开始冻结，解决方案的不同组件遵循数据工厂发出的命令开始操作。 可通过两种方式监视管道。

1. 某个流分析作业会将原始传入数据写入 Blob 存储。 如果在成功部署解决方案的屏幕中单击解决方案的“Blob 存储”组件，然后在右窗格中单击“打开”，则会转到 [Azure 门户](https://portal.azure.com/)。 进入门户后，单击“Blob”。 在随后出现的面板中，可以看到容器列表。 单击“maintenancesadata”  。 随后出现的面板中有 **rawdata** 文件夹。 在 rawdata 文件夹内，可以看到名为 hour=17、hour=18 等的文件夹。 存在这些文件夹表明原始数据正在计算机上生成并存储在 Blob 存储中。 应会在这些文件夹中看到具有有限 MB 大小的 csv 文件。
2. 管道的最后一个步骤是将数据（例如机器学习的预测数据）写入 SQL 数据库。 最多可能需要 3 个小时，数据才会出现在 SQL 数据库中。 监视 SQL 数据库中有多少数据的方法之一是使用 [Azure 门户](https://portal.azure.com/)。 在左侧面板中找到“SQL 数据库”![SQL 图标](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png)并单击它。 然后查找自己的数据库 **pmaintenancedb** 并单击它。 在下一页的底部，单击“管理”
   
    ![管理图标](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    可在此处单击“新建查询”并查询行数（例如，select count(*) from PMResult）。 表中的行数会随数据库的增长而增加。

## <a name="power-bi-dashboard"></a>Power BI 仪表板

设置 Power BI 仪表板，可视化 Azure 流分析数据（热路径）以及来自 Azure 机器学习（冷路径）的批量预测结果。

### <a name="set-up-the-cold-path-dashboard"></a>设置冷路径仪表板
在冷路径数据管道中，目标是完成航班后获取每个飞机引擎的预测 RUL（剩余使用寿命）（周期）。 预测结果每 3 小时更新一次，预测过去 3 小时内已结束航班的的飞机引擎。

Power BI 将连接到充当其数据源、用于存储预测结果的 Azure SQL 数据库。 注意:1) 部署解决方案后，预测会在 3 小时内在数据库中显示。
生成器下载附带的 pbix 文件包含某些种子数据，因此需要立即创建 Power BI 仪表板。 2) 此步骤的先决条件是下载并安装免费软件 [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)。

以下步骤介绍如何将 pbix 文件连接到解决方案部署包含可视化数据时启动的 SQL 数据库（例如预测结果）。

1. 获取数据库凭据。
   
   在继续下一步之前，需要获取**数据库服务器名称、数据库名称、用户名和密码**。 下面提供了有关如何查找这些信息的指导。
   
   * 解决方案模板图示上的“Azure SQL 数据库”  变为绿色后，请单击它，并单击“打开”  。
   * 会看到新的浏览器选项卡/窗口显示 Azure 门户页面。 单击左侧面板中的“资源组”  。
   * 选择要用于部署该解决方案的订阅，并选择“YourSolutionName\_ResourceGroup”  。
   * 在新弹出的面板中，单击 ![SQL 图标](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png)图标以访问数据库。 数据库名称位于此图标的旁边（例如 'pmaintenancedb'），“数据库服务器名称”列于服务器名称属性下方且应类似于 YourSoutionName.database.windows.net    。
   * 数据库**用户名**和**密码**与部署解决方案期间记下的用户名和密码相同。
2. 使用 Power BI Desktop 更新冷路径报表文件的数据源。
   
   * 在下载和解压生成器文件的文件夹中，双击“PowerBI\\PredictiveMaintenanceAerospace.pbix”  文件。 如果在打开文件时看到任何警告消息，请忽略。 请在文件顶部单击“编辑查询”  。
     
     ![编辑查询](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * 会看到两个表：**RemainingUsefulLife** 和 **PMResult**。 选择第一个表，并在右侧“查询设置”  面板的“应用的步骤”  下的“源”  旁边单击![查询设置图标](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png)。 忽略显示的任何警告消息。
   * 在弹出窗口中，将“服务器”  和“数据库”  替换为自己的服务器和数据库名称，然后单击“确定”  。 对于服务器名称，请确保指定端口 1433 (**YourSolutionName.database.windows.net, 1433**)。 将数据库字段保留为 **pmaintenancedb**。 忽略屏幕上出现的警告消息。
   * 下一个弹出窗口的左侧窗格中出现了两个选项（“Windows”  和“数据库”  ）。 单击“数据库”  ，填充“用户名”  和“密码”  （这是首次部署解决方案和创建 Azure SQL 数据库时输入的用户名与密码）。 在“选择要将这些设置应用到的级别”中，选中数据库级别选项。 然后单击“连接”  。
   * 单击第二个表 **PMResult**，在右侧“查询设置”  面板的“应用的步骤”  下的“源”  旁边单击![导航图标](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png)，根据以上步骤更新服务器和数据库名称并单击“确定”。
   * 返回上一页后，请关闭窗口。 此时会显示一条消息 - 单击“应用”。  最后，单击“保存”保存更改  。 Power BI 文件现在已与服务器建立连接。 如果可视化效果是空的，请务必单击图例右上角的橡皮擦图标清除可视化效果中的选择内容，这样即可查看所有数据。 使用刷新按钮显示可视化效果中的新数据。 最初，只会在可视化效果中看到种子数据，因为数据工厂计划为每隔 3 小时刷新一次。 3 小时后，刷新数据时，可以看到新预测数据反映在可视化效果中。
3. （可选）将冷路径仪表板发布到 [Power BI online](https://www.powerbi.com/)。 请注意，此步骤需要 Power BI 帐户（或 Office 365 帐户）。
   
   * 单击“发布”  ，几秒钟后会出现一个窗口，显示“发布到 Power BI 成功!” 和一个绿色的复选标记。 单击以下链接“在 Power BI 中 打开 PredictiveMaintenanceAerospace.pbix”。 若要查找详细说明，请参阅[从 Power BI Desktop 发布](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)。
   * 若要创建新仪表板，请在左侧窗格中单击“仪表板”  部分旁边的 **+** 号。 为新仪表板输入名称“Predictive Maintenance Demo”。
   * 打开报表后，请单击![大头针图标](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png)以将所有可视化效果固定到仪表板。 要查找详细说明，请参阅[从报表将磁贴固定到 Power BI 仪表板](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)。
     转到仪表板页并调整可视化效果的大小和位置并编辑其标题。 若要查找有关如何编辑磁贴的详细说明，请参阅[编辑磁贴 - 重设大小、移动、重命名、固定、删除、添加超链接](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)。 下面是固定了一些冷路径可视化效果的仪表板示例。  根据运行数据生成器的时间，可视化效果上的数字可能会不同。
     <br/>
     ![最终视图](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * 计划刷新的数据，请将鼠标悬停**PredictiveMaintenanceAerospace**数据集，单击![省略号图标](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png)，然后选择**计划刷新**。
     <br/>
     **注意：** 如果看到警告消息，请单击“编辑凭据”，确保数据库凭据与步骤 1 中所述相同  。
     <br/>
     ![计划刷新](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * 展开“计划刷新”部分。  打开“保持数据最新”。
     <br/>
   * 根据需要计划刷新。 若要查找详细信息，请参阅 [Power BI 中的数据刷新](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi)。

### <a name="setup-hot-path-dashboard"></a>设置热路径仪表板
以下步骤逐步说明如何可视化部署解决方案时生成的流分析作业的数据输出。 若要执行以下步骤，需要一个 [Power BI online](https://www.powerbi.com/) 帐户。 如果没有帐户，可以[创建一个](https://powerbi.microsoft.com/pricing)。

1. 在 Azure 流分析 (ASA) 中添加 Power BI 输出。
   
   * 必须按照 [Azure 流分析和 Power BI：用于实时查看流数据的分析仪表板](../../stream-analytics/stream-analytics-power-bi-dashboard.md)中的说明操作，将 Azure 流分析作业的输出设置为 Power BI 仪表板。
   * ASA 查询具有三个输出，分别为 **aircraftmonitor**、**aircraftalert** 和 **flightsbyhour**。 可通过单击查询选项卡查看查询。需要根据相应的表将输出添加到 ASA。 添加第一个输出时 (**aircraftmonitor**)，请确保“输出别名”、“数据集名称”和“表名称”都相同 (**aircraftmonitor**)。    重复以上步骤为 **aircraftalert** 和 **flightsbyhour** 添加输出。 将三个输出表全部添加完成并启动 ASA 作业后，应收到一条确认消息（“成功启动流分析作业 maintenancesa02asapbi”）。
2. 登录到 [Power BI online](https://www.powerbi.com)
   
   * 在左侧面板“我的工作区”中的“数据集”部分，会显示数据集名称 **aircraftmonitor**、**aircraftalert** 和 **flightsbyhour**。 这是在上一步骤中从 Azure 流分析推送的流数据。 数据集 **flightsbyhour** 可能不与其他两个数据集同时显示，因为其后的 SQL 查询的性质不同。 但它会在一小时后显示。
   * 确保“可视化效果”窗格已打开，并显示在屏幕的右侧。
3. 将数据传输到 Power BI 后，可开始可视化流式处理数据。 下面是固定了一些热路径可视化效果的仪表板示例。 根据相应的数据集可创建其他仪表板磁贴。 根据运行数据生成器的时间，可视化效果上的数字可能会不同。

    ![仪表板视图](media/cortana-analytics-technical-guide-predictive-maintenance/dashboard-view.png)

1. 下面为创建以上磁贴之一的步骤 – “传感器 11 车队视图与阈值 48.26”磁贴：
   
   * 在左侧面板“数据集”部分中单击数据集“aircraftmonitor”  。
   * 单击**折线图**图标。
   * 单击“字段”  窗格中的“已处理”  ，其会在“可视化效果”  窗格中的“轴”下显示。
   * 单击“s11”和“s11\_alert”，两者都会在“值”下显示。 单击“s11”  和“s11\_警告”  旁的小箭头，将“总和”更改为“平均值”。
   * 单击顶部的“保存”  ，将报表命名为“aircraftmonitor”。 名为“aircraftmonitor”的报表随即显示在左侧“导航”  窗格的“报表”  部分。
   * 单击折线图右上角的“固定视觉对象”  图标。 可能会显示“固定到仪表板”窗口让你选择仪表板。 选择“预测性维护演示”，然后单击“固定”。
   * 在仪表板上将鼠标悬停在此磁贴上，单击右上角的“编辑”图标，将其标题更改为“传感器 11 车队视图与阈值 48.26”，将副标题更改为“时间段内车队平均数”。

## <a name="delete-your-solution"></a>删除解决方案
在不经常使用解决方案的情况下，请务必停止数据生成器，因为运行数据生成器会产生较高的费用。 如果不使用解决方案，请将其删除。 删除解决方案会删除部署该解决方案时在订阅中预配的所有组件。 若要删除解决方案，请在解决方案模板左侧面板中单击该解决方案的名称，并单击“删除”。 

## <a name="cost-estimation-tools"></a>成本估算工具
以下两个工具可帮助进一步了解在订阅中运行航天工业预见性维护的解决方案模板产生的总成本：

* [Microsoft Azure Cost Estimator Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure Cost Estimator Tool (desktop)](https://www.microsoft.com/download/details.aspx?id=43376)

