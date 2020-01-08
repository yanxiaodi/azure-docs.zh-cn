---
title: 使用 Azure 门户和 PowerShell 监视和管理管道 | Microsoft 文档
description: 了解如何使用 Azure 门户和 Azure PowerShell 监视和管理 Azure 数据工厂及已创建的管道。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 8e8215d9737087cf1a5632dc8514c12988ff999f
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139655"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>使用 Azure 门户和 PowerShell 监视和管理 Azure 数据工厂管道
> [!div class="op_single_selector"]
> * [使用 Azure 门户/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [使用“监视和管理”应用](data-factory-monitor-manage-app.md)

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用数据工厂服务的当前版本，请参阅[监视和管理数据工厂管道](../monitor-visually.md)。

本文介绍如何使用 Azure 门户和 PowerShell 监视、管理和调试管道。

> [!IMPORTANT]
> 通过监视和管理应用程序，可更好地监视和管理数据管道并解决出现的任何问题。 有关使用此应用的详细信息，请参阅[使用“监视和管理”应用监视和管理数据工厂管道](data-factory-monitor-manage-app.md)。 

> [!IMPORTANT]
> Azure 数据工厂版本 1 现在使用新的 [Azure Monitor 警报基础结构](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。 旧警报基础结构已弃用。 因此，为版本 1 数据工厂配置的现有警报不再有效。 v1 数据工厂的现有警报不会自动迁移。 你必须在新的警报基础结构上重新创建这些警报。 登录到 Azure门户并选择“监视器”，针对指标（如失败的运行或成功的运行）为版本 1 数据工厂创建新的警报。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>了解管道和活动状态
使用 Azure 门户，可以：

* 以图示形式查看数据工厂。
* 查看管道中的活动。
* 查看输入和输出数据集。

本部分还介绍数据集切片如何从一个状态转换为另一状态。   

### <a name="navigate-to-your-data-factory"></a>导航到数据工厂
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，单击“数据工厂”。 如未看到，请单击“更多服务 >”，并在“智能 + 分析”类别下单击“数据工厂”。

   ![“浏览全部”->“数据工厂”](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. 在“数据工厂”边栏选项卡中，选择感兴趣的数据工厂。

    ![选择数据工厂](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   应显示该数据工厂的主页。

   ![“数据工厂”边栏选项卡](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>数据工厂的图示视图
数据工厂的**图示**视图提供单个窗格来监视和管理数据工厂及其资产。 若要查看数据工厂的**图示**视图，请在数据工厂的主页上单击“图示”。

![图示视图](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

可放大、缩小、缩放到合适大小、缩放到 100%、锁定图示布局，还可自动放置管道和数据集。 还可看到数据沿袭信息（即，显示选定项的上下游项）。

### <a name="activities-inside-a-pipeline"></a>管道中的活动
1. 右键单击管道，并单击“打开管道”查看管道中的所有活动以及该活动的输入和输出数据集。 如果管道包含多个活动且用户想要了解单个管道的操作沿袭，此功能非常有用。

    ![“打开管道”菜单](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. 在下面的示例中，可看到管道中的复制活动以及输入和输出。 

    ![管道中的活动](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. 通过在左上角痕迹导航中单击“数据工厂”链接，可向后导航到数据工厂主页。

    ![向后导航到数据工厂](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>查看管道中每个活动的状态
通过查看活动生成的任意数据集状态，可查看该活动的当前状态。

在“图示”中双击“OutputBlobTable”，可查看管道内不同活动运行所生成的所有切片。 可以看到过去 8 小时成功运行了复制活动，并生成了处于“就绪”状态的切片。  

![管道状态](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

数据工厂中的数据集切片可以具有以下任一状态：

<table>
<tr>
    <th align="left">状态</th><th align="left">子状态</th><th align="left">描述</th>
</tr>
<tr>
    <td rowspan="8">等待</td><td>ScheduleTime</td><td>未到运行切片的时间。</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>上游依赖项未准备就绪。</td>
</tr>
<tr>
<td>ComputeResources</td><td>计算资源不可用。</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>所有活动实例都忙于运行其他切片。</td>
</tr>
<tr>
<td>ActivityResume</td><td>活动已暂停，且在恢复活动之前无法运行切片。</td>
</tr>
<tr>
<td>重试</td><td>正在重试活动执行。</td>
</tr>
<tr>
<td>验证</td><td>验证尚未开始。</td>
</tr>
<tr>
<td>ValidationRetry</td><td>验证正在等待重试。</td>
</tr>
<tr>
<tr>
<td rowspan="2">正在进行</td><td>正在验证</td><td>正在进行验证。</td>
</tr>
<td>-</td>
<td>正在处理切片。</td>
</tr>
<tr>
<td rowspan="4">已失败</td><td>已超时</td><td>活动执行时间超过活动允许的时间。</td>
</tr>
<tr>
<td>已取消</td><td>切片已由用户操作取消。</td>
</tr>
<tr>
<td>验证</td><td>验证失败。</td>
</tr>
<tr>
<td>-</td><td>切片无法生成和/或验证。</td>
</tr>
<td>就绪</td><td>-</td><td>切片已就绪，可供使用。</td>
</tr>
<tr>
<td>已跳过</td><td>无</td><td>未在处理切片。</td>
</tr>
<tr>
<td>None</td><td>-</td><td>切片过去一直以不同状态存在，但已被重置。</td>
</tr>
</table>



通过在“最近更新的切片”边栏选项卡中单击切片条目，可查看该切片的详细信息。

![切片详细信息](./media/data-factory-monitor-manage-pipelines/slice-details.png)

如果已多次执行切片，则会在“活动运行”列表中看到多行。 通过在“活动运行”列表中单击运行条目，可以查看有关活动运行的详细信息。 该列表会显示所有日志文件以及一条错误消息（如果有）。 此功能对于查看和调试日志（而无需离开数据工厂）非常有用。

![活动运行详细信息](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

如果切片状态不是“就绪”，可以在“未就绪的上游切片”列表中看到未就绪且阻止当前切片执行的上游切片。 如果切片处于“等待”状态，并且希望了解等待中切片的上游依赖关系，此功能非常有用。

![未就绪的上游切片](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>数据集状态图
部署数据工厂且管道拥有有效的活动期后，数据集切片便会从某一状态转换为另一种状态。 当前，切片状态如以下状态图所示：

![状态图](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

数据工厂中的数据集状态转换流程如下：正在等待 - > 正在进行/正在进行（正在验证）- > 就绪/失败。

切片从“等待”状态开始，等待满足前提条件后执行。 然后，活动开始执行，切片进入“正在进行”状态。 活动执行可能成功，也可能失败。 根据执行结果，切片被标记为“就绪”或“失败”。

可以重置切片，将其从“就绪”或“失败”状态恢复到“等待”状态。 也可以将切片状态标记为“跳过”，从而避免执行活动而不处理切片。

## <a name="pause-and-resume-pipelines"></a>暂停和恢复管道
可以使用 Azure PowerShell 管理管道。 例如，通过运行 Azure PowerShell cmdlet，可以暂停和恢复管道。 

> [!NOTE] 
> 图示视图不可用于暂停和恢复管道。 若想要使用用户界面，请使用监视和管理应用程序。 有关使用此应用的详细信息，请参阅文章[使用“监视和管理”应用监视和管理数据工厂管道](data-factory-monitor-manage-app.md)。 

可以使用**AzDataFactoryPipeline** PowerShell cmdlet 暂停/挂起管道。 如果问题得以解决之前不准备运行管道，此 cmdlet 非常有用。 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
例如：

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

修复管道相关问题后，可通过运行下列 PowerShell 命令恢复挂起的管道：

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
例如：

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>调试管道
Azure 数据工厂提供了通过 Azure 门户和 Azure PowerShell 调试和排查管道问题的丰富功能。

> [!NOTE] 
> 使用“监视和管理”应用来排查错误要简便得多。 有关使用此应用的详细信息，请参阅文章[使用“监视和管理”应用监视和管理数据工厂管道](data-factory-monitor-manage-app.md)。 

### <a name="find-errors-in-a-pipeline"></a>在管道中查找错误
如果活动在管道中运行失败，则管道所生成数据集将因此故障而处于错误状态。 可以在 Azure 数据工厂中使用以下方法调试和解决错误。

#### <a name="use-the-azure-portal-to-debug-an-error"></a>使用 Azure 门户调试错误
1. 在“表”边栏选项卡中，单击“状态”设置为“失败”的问题切片。

   ![包含问题切片的“表”边栏选项卡](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. 在“数据切片”边栏选项卡中，单击失败的活动运行。

   ![出现错误的数据切片](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. 在“活动运行详细信息”边栏选项卡中，可以下载与 HDInsight 处理关联的文件。 单击“状态/stderr”所对应的“下载”可下载包含错误详细信息的错误日志文件。

   ![出现错误的“活动运行详细信息”边栏选项卡](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>使用 PowerShell 调试错误
1. 启动 **PowerShell**。
2. 运行**AzDataFactorySlice**命令以查看切片及其状态。 应看到“失败”状态的切片。        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   例如：

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   将“StartDateTime”替换为管道的开始时间。 
3. 现在, 运行**AzDataFactoryRun** cmdlet 以获取有关切片的活动运行的详细信息。

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    例如：

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    StartDateTime 的值是上一步中记下的错误/问题切片的开始时间。 date-time 应括在双引号内。
4. 应看到包含错误详细信息的输出（类似以下内容）：

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. 你可以使用从输出中看到的 Id 值运行**AzDataFactoryLog** cmdlet, 并使用 cmdlet 的 **-DownloadLogsoption**下载日志文件。

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>管道中的重新运行故障

> [!IMPORTANT]
> 通过“监视和管理”应用，可更轻松地排查错误和返回有故障的切片。 有关使用此应用的详细信息，请参阅[使用“监视和管理”应用监视和管理数据工厂管道](data-factory-monitor-manage-app.md)。 

### <a name="use-the-azure-portal"></a>使用 Azure 门户
排除和调试管道中的故障后，便可通过导航到错误切片并在命令栏上单击“运行”按钮来重新运行失败命令。

![重新运行失败的切片](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

在切片因策略失败（例如：如果数据不可用）而未通过验证的情况下，在命令栏上单击“验证”按钮，可以解决故障并再次验证。

![修复错误并验证](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>使用 Azure PowerShell
可以通过使用**AzDataFactorySliceStatus** cmdlet 重新运行失败。 有关 cmdlet 的语法和其他详细信息, 请参阅[AzDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus)主题。

**示例：**

以下示例将 Azure 数据工厂“WikiADF”中表“DAWikiAggregatedData”的所有切片状态设置为“等待”。

将“UpdateType”设置为“UpstreamInPipeline”，这意味着表中每个切片和所有相关（上游）表的状态都将设置为“等待”。 此参数的其他可能值为“Individual”。

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>在 Azure 门户中创建警报

1.  登录到 Azure 门户，然后依次选择“监视器”->“警报”以打开“警报”页。

    ![打开“警报”页。](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  选择“+ 创建新的预警规则”，创建新的警报。

    ![新建警报](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  定义警报条件。 （请务必在“按资源类型筛选”字段中选择“数据工厂”。）你还可以指定维度的值。

    ![定义警报条件 - 选择目标](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![定义警报条件 - 添加警报条件](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![定义警报条件 - 添加警报逻辑](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  定义警报详细信息。

    ![定义警报详细信息](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  定义操作组。

    ![定义操作组 - 新建操作组](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![定义操作组 - 设置属性](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![定义操作组 - 创建的新操作组](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>将数据工厂移到其他资源组或订阅
通过使用数据工厂主页上的“移动”命令栏按钮，可以将数据工厂移动到其他资源组或其他订阅。

![移动数据工厂](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

还可移动任何相关资源（如与数据工厂关联的警报）以及数据工厂。

![“移动资源”对话框](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
