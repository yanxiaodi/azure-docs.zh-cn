---
title: 监视和管理数据管道 - Azure | Microsoft 文档
description: 了解如何使用“监视和管理”应用来监视和管理 Azure 数据工厂和管道。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 052ea99f0489458269adf4dca2c6713535933638
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139578"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>使用“监视和管理”应用监视和管理 Azure 数据工厂管道
> [!div class="op_single_selector"]
> * [使用 Azure 门户/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [使用“监视和管理”应用](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用数据工厂服务的当前版本，请参阅[监视和管理数据工厂管道](../monitor-visually.md)。

本文介绍如何使用“监视和管理”应用监视、管理和调试数据工厂管道。 可以通过观看以下视频开始使用该应用程序：

> [!NOTE]
> 视频中所示的用户界面可能与门户中看到的内容不完全匹配。 它略显陈旧，但概念保持不变。 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>启动“监视和管理”应用
若要启动“监视和管理”应用，请针对数据工厂单击“数据工厂”边栏选项卡上的“监视和管理”磁贴。

![数据工厂主页上的“监视”磁贴](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

可以看到“监视和管理”应用在单独的窗口中打开。  

![“监视和管理”应用](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> 如果 Web 浏览器停滞在“正在授权...”处，请清除“阻止第三方 Cookie 和站点数据”复选框，或在将其保持选中的状态下为 **login.microsoftonline.com** 创建一个例外，并尝试再次打开该应用。


在中间窗格中的“活动窗口”列表中，每次运行某个活动时，都可以看到一个活动窗口。 例如，如果将该活动计划为在五小时内每小时运行一次，将看到与五个数据切片关联的五个活动窗口。 如果在底部列表中看不到活动窗口，请执行以下操作：
 
- 更新顶部的“开始时间”和“结束时间”筛选器以匹配管道的开始时间和结束时间，然后单击“应用”按钮。  
- “活动窗口”列表不会自动刷新。 请单击“活动窗口”列表中工具栏上的“刷新”按钮。  

如果没有用于测试这些步骤的数据工厂应用程序，请完成教程：[使用数据工厂将数据从 Blob 存储复制到 SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

## <a name="understand-the-monitoring-and-management-app"></a>了解“监视和管理”应用
在左侧有三个选项卡：“资源浏览器”、“监视视图”和“警报”。 第一个选项卡（**资源浏览器**）在默认情况下处于选中状态。

### <a name="resource-explorer"></a>资源浏览器
会看到如下内容：

* 左侧窗格中的资源浏览器**树视图**。
* 中间窗格顶部的**图示视图**。
* 中间窗格底部的“活动窗口”列表。
* 右侧窗格中的“属性”、“活动窗口资源管理器”和“脚本”选项卡。

在资源浏览器中，可在树视图的数据工厂中查看所有资源（管道、数据集、链接服务）。 在资源浏览器中选择对象时：

* 图示视图中将突出显示相关的数据工厂实体。
* “活动窗口”列表中将突出显示[相关的活动窗口](data-factory-scheduling-and-execution.md)。  
* 右侧窗格的“属性”窗口中会显示所选对象的属性。
* 将显示所选对象的 JSON 定义（如果适用）。 例如：链接服务、数据集或管道。

![资源浏览器](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

有关活动窗口的详细概念性信息，请参阅[计划和执行](data-factory-scheduling-and-execution.md)一文。

### <a name="diagram-view"></a>图示视图
数据工厂的图示视图提供单个窗格来监视和管理数据工厂及其资产。 在图示视图中选择数据工厂实体（数据集/管道）时：

* 数据工厂实体会在树视图中处于选中状态。
* “活动窗口”列表中将突出显示相关的活动窗口。
* “属性”窗口中会显示所选对象的属性。

管道已启用（不处于暂停状态）时，将使用绿线显示管道：

![正在运行的管道](./media/data-factory-monitor-manage-app/PipelineRunning.png)

通过在图示视图中选择管道并使用命令栏上的按钮，可以暂停、恢复或终止管道。

![命令栏上的“暂停”/“恢复”](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
图示视图中有三个用于管道的命令栏按钮。 可以使用第二个按钮暂停管道。 暂停不会终止当前正在运行的活动，而是会允许其继续完成。 第三个按钮暂停管道并终止其现有的执行活动。 第一个按钮恢复管道。 暂停管道后，管道的颜色将改变。 例如，暂停的管道如下图中所示： 

![暂停的管道](./media/data-factory-monitor-manage-app/PipelinePaused.png)

可以通过使用 Ctrl 键对两个或更多管道进行多选。 可以使用命令栏按钮来同时暂停/恢复多个管道。

还可以右键单击管道，然后选择相应选项以暂停、恢复或终止管道。 

![管道的上下文菜单](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

单击“打开管道”选项可查看管道中的所有活动。 

![“打开管道”菜单](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

在打开的管道视图中，可以看到管道中的所有活动。 在此示例中，只有一个活动：复制活动。 

![打开的管道](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

若要返回到上一视图，请单击顶部痕迹菜单中的数据工厂名称。

在管道视图中，选择输出数据集或将鼠标悬停在输出数据集上时，可以看到该数据集的“活动窗口”弹出窗口。

![“活动窗口”弹出窗口](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

可以在右侧窗格的“属性”窗口中单击活动窗口以查看其详细信息。

![“活动窗口”属性](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

在右侧窗格中，切换到“活动窗口资源管理器”选项卡以查看更多详细信息。

![活动窗口资源管理器](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

还可在“尝试”部分看到活动的每次运行尝试的**已解析变量**。

![已解析变量](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

切换到“脚本”选项卡以查看所选对象的 JSON 脚本定义。   

![脚本选项卡](./media/data-factory-monitor-manage-app/ScriptTab.png)

可在三处看到活动窗口：

* 图示视图（中间窗格）中的“活动窗口”弹出窗口。
* 右侧窗格中的“活动窗口资源管理器”。
* 底部窗格中的“活动窗口”列表。

在“活动窗口”弹出窗口和“活动窗口资源管理器”中，可使用向左、向右键滚动到上一周和下一周。

![活动窗口资源管理器向左/向右键](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

在图示视图底部，可以看到以下按钮：放大、缩小、缩放到合适大小、比例 100%、锁定布局。 “锁定布局”按钮将防止意外移动图示视图中的表和管道。 它在默认情况下处于“打开”状态。 可将其关闭，还可在图示中移动实体。 将其关闭后，可使用最后一个按钮来自动放置表和管道。 还可以通过使用鼠标滚轮来放大或缩小。

![图示视图缩放命令](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>“活动窗口”列表
中间窗格底部的“活动窗口”列表显示资源浏览器或图示视图中所选数据集的所有活动窗口。 默认情况下，该列表按降序排序，这意味着可在顶部看到最新的活动窗口。

![“活动窗口”列表](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

此列表不会自动刷新，因此请使用工具栏上的刷新按钮进行手动刷新。  

活动窗口可处于以下任一状态：

<table>
<tr>
    <th align="left">状态</th><th align="left">子状态</th><th align="left">描述</th>
</tr>
<tr>
    <td rowspan="8">等待</td><td>ScheduleTime</td><td>未到运行活动窗口的时间。</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>上游依赖项未准备就绪。</td>
</tr>
<tr>
<td>ComputeResources</td><td>计算资源不可用。</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>所有活动实例都忙于运行其他活动窗口。</td>
</tr>
<tr>
<td>ActivityResume</td><td>活动暂停，且在恢复活动之前无法运行活动窗口。</td>
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
<td>正在处理活动窗口。</td>
</tr>
<tr>
<td rowspan="4">已失败</td><td>已超时</td><td>活动执行时间超过活动允许的时间。</td>
</tr>
<tr>
<td>已取消</td><td>用户操作已取消活动窗口。</td>
</tr>
<tr>
<td>验证</td><td>验证失败。</td>
</tr>
<tr>
<td>-</td><td>未能生成和/或验证活动窗口。</td>
</tr>
<td>就绪</td><td>-</td><td>活动窗口已就绪，可供使用。</td>
</tr>
<tr>
<td>已跳过</td><td>-</td><td>未处理活动窗口。</td>
</tr>
<tr>
<td>无</td><td>-</td><td>过去一直以不同状态存在但已被重置的活动窗口。</td>
</tr>
</table>


单击列表中的活动窗口时，可在右侧的“活动窗口资源管理器”或“属性”窗口中看到其详细信息。

![活动窗口资源管理器](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>刷新活动窗口
详细信息不会自动刷新，因此请使用命令栏上的“刷新”按钮（第二个按钮）手动刷新活动窗口列表。  

### <a name="properties-window"></a>属性窗口
属性窗口位于监视和管理应用的最右侧窗格中。

![属性窗口](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

它会显示资源浏览器（树视图）、图示视图或“活动窗口”列表中所选项的属性。

### <a name="activity-window-explorer"></a>活动窗口资源管理器
“活动窗口资源管理器”窗口位于“监视和管理”应用的最右侧窗格中。 它显示“活动窗口”弹出窗口或“活动窗口”列表中所选活动窗口的相关详细信息。

![活动窗口资源管理器](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

通过在顶部日历视图中单击活动窗口资源管理器，可切换到另一活动窗口。 还可使用顶部的向左键/向右键按钮查看上周或下周的活动窗口。

在底部窗格中，可使用工具栏按钮来重新运行活动窗口或刷新窗格中的详细信息。

### <a name="script"></a>脚本
可以使用“脚本”选项卡来查看所选数据工厂实体（链接服务、数据集或管道）的 JSON 定义。

![脚本选项卡](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>使用系统视图
使用“监视和管理”应用附带的预建系统视图（“最近活动窗口”、“失败活动窗口”、“进行中活动窗口”），可以查看数据工厂的最近/失败/进行中活动窗口。

通过单击左侧“监视视图”选项卡切换到此视图。

![监视视图选项卡](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

目前有三种系统视图受支持。 选择一个选项可在“活动窗口”列表（位于中间窗格底部）中查看最近活动窗口、失败活动窗口或进行中活动窗口。

选择“最近活动窗口”选项时，将看到按**上次尝试时间**降序排序的所有最近活动窗口。

可以使用“失败活动窗口”视图来查看列表中所有失败的活动窗口。 在该列表中选择失败活动窗口，可在“属性”窗口或“活动窗口资源管理器”中查看其详细信息。 还可以下载失败活动窗口的任何日志。

## <a name="sort-and-filter-activity-windows"></a>排序并筛选活动窗口
更改命令栏中的“开始时间”和“结束时间”设置，以筛选活动窗口。 更改开始时间和结束时间后，单击结束时间旁边的按钮可刷新“活动窗口”列表。

![开始和结束时间](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> 目前，“监视和管理”应用中的所有时间均采用 UTC 格式。
>
>

在“活动窗口”列表中，单击某一列的名称（例如：状态）。

![“活动窗口”列表列菜单](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

可以执行以下操作：

* 按升序排序。
* 按降序排序。
* 按一个或多个值（“就绪”、“等待”等）筛选。

对列指定筛选器时，将看到为该列启用的筛选器按钮，该按钮指示列中的值为已筛选值。

![“活动窗口”列表的列的筛选器](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

可以使用相同的弹出窗口来清除筛选器。 若要清除“活动窗口”列表的所有筛选器，请单击命令栏上的清除筛选器按钮。

![清除“活动窗口”列表的所有筛选器](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>执行批处理操作
### <a name="rerun-selected-activity-windows"></a>重新运行选定的活动窗口
选择活动窗口，单击第一个命令栏按钮的向下键，并选择“重新运行” / “与管道中的上游一起重新运行”。 选择“与管道中的上游一起重新运行”选项时，也将重新运行所有上游活动窗口。
    ![重新运行活动窗口](./media/data-factory-monitor-manage-app/ReRunSlice.png)

还可在列表中选择多个活动窗口，并同时重新运行它们。 你可能希望基于状态筛选活动窗口（例如：**失败**）--并在解决导致活动窗口失败的问题后重新运行失败的活动窗口。 有关在列表中筛选活动窗口的详细信息，请参阅以下部分。  

### <a name="pauseresume-multiple-pipelines"></a>暂停/恢复多个管道
可以通过使用 Ctrl 键对两个或更多管道进行多选。 可以使用命令栏按钮（在下图的红色矩形中突出显示）来暂停/恢复它们。

![命令栏上的“暂停”/“恢复”](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
