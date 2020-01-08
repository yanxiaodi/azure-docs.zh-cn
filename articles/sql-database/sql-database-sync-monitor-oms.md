---
title: 利用 Azure Monitor 日志监视 Azure SQL 数据同步 |Microsoft Docs
description: 了解如何使用 Azure Monitor 日志监视 Azure SQL 数据同步
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: d1461a1bb026d478d51a5f79cc02b34172524db6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566413"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>利用 Azure Monitor 日志监视 SQL 数据同步 

若要检查 SQL 数据同步活动日志并检测错误和警告，以前必须在 Azure 门户手动检查 SQL 数据同步，或者使用 PowerShell 或 REST API。 请按照本文中的步骤配置自定义解决方案，以便改进数据同步监控体验。 可以自定义该解决方案以适合你的方案。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

有关 SQL 数据同步的概述，请参阅[使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)。

> [!IMPORTANT]
> 目前，Azure SQL 数据同步不支持 Azure SQL 数据库托管实例。

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>监视所有同步组的仪表板 

再也无需单独仔细查看每个同步组的日志就能查找问题。 可以通过使用自定义 Azure Monitor 视图在一个位置监视所有订阅中的所有同步组。 此视图显示对 SQL 数据同步客户很重要的信息。

![数据同步监控仪表板](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>自动电子邮件通知

不再需要在 Azure 门户中手动或通过 PowerShell、REST API 检查日志。 使用[Azure Monitor 日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview), 可以创建警报, 这些警报直接发送到发生错误时需要查看的人员的电子邮件地址。

![数据同步电子邮件通知](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>如何设置这些监视功能？ 

通过执行以下操作, 在不到一小时的时间内实现自定义 Azure Monitor 日志监视 SQL 数据同步解决方案:

需要配置三个组件：

-   用于将 SQL 数据同步日志数据馈送到 Azure Monitor 日志的 PowerShell runbook。

-   电子邮件通知的 Azure Monitor 警报。

-   用于监视的 Azure Monitor 视图。

### <a name="samples-to-download"></a>要下载的示例

可下载以下两个示例：

-   [数据同步日志 PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [数据同步 Azure Monitor 视图](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>系统必备

请确保已设置以下内容：

-   一个 Azure 自动化帐户

-   Log Analytics 工作区

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>要获取 SQL 数据同步日志的 PowerShell Runbook 

使用托管在 Azure 自动化中的 PowerShell runbook 提取 SQL 数据同步日志数据, 并将其发送到 Azure Monitor 日志。 已包含示例脚本。 作为先决条件，需要有一个 Azure 自动化帐户。 然后，你需要创建 runbook 并安排它运行。 

### <a name="create-a-runbook"></a>创建 runbook

有关创建 runbook 的详细信息，请参阅[我的第一个 PowerShell runbook](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell)。

1.  在 Azure 自动化帐户下，请选择“流程自动化”下的“Runbook”选项卡。

2.  在 Runbook 页面左上角选择“添加 Runbook”。

3.  选择“导入现有的 Runbook”。

4.  在“Runbook 文件”下，使用给定的 `DataSyncLogPowerShellRunbook` 文件。 将“Runbook 类型”设置为 `PowerShell`。 为 runbook 提供一个名称。

5.  选择“创建”。 现在你拥有了一个 runbook。

6.  在 Azure 自动化帐户下，请选择“共享资源”下的“变量”选项卡。

7.  在“变量”页上，选择“添加变量”。 创建一个变量来存储 runbook 的上次执行时间。 如果你有多个 runbook，则每个 runbook 都需要一个变量。

8.  该变量名称设置为 `DataSyncLogLastUpdatedTime`，并将其类型设置为 DateTime。

9.  选择 runbook，然后单击页顶部的编辑按钮。

10. 请为你的帐户和 SQL 数据同步配置执行所需的更改。 （如需更多详细信息，请参阅示例脚本。）

    1.  Azure 信息。

    2.  同步组信息。

    3.  Azure Monitor 日志信息。 在 Azure 门户 | 设置 | 连接的源中查找此信息。 有关将数据发送到 Azure Monitor 日志的详细信息, 请参阅[通过 HTTP 数据收集器 API (预览版) 将数据发送到 Azure Monitor 日志](../azure-monitor/platform/data-collector-api.md)。

11. 在“测试”窗格中运行 runbook。 检查以确保它已运行成功。

    如果遇到错误，请确保已安装最新的PowerShell 模块。 可以将**模块库**中的最新 PowerShell 模块安装到自动化帐户中。

12. 单击“发布”。

### <a name="schedule-the-runbook"></a>计划 runbook。

要计划 runbook，请执行以下操作：

1.  在 runbook 下，选择“资源”下的“计划”选项卡。

2.  在“计划”页上选择“添加计划”。

3.  选择“将一个计划链接到你的 Runbook”。

4.  选择“创建新计划”。

5.  将“定期”设置为“重复执行”，并设置所需间隔。 请在脚本中使用相同的间隔, 并在 Azure Monitor 日志中使用相同的间隔。

6.  选择“创建”。

### <a name="check-the-automation"></a>检查自动化

若要监控你的自动化设置是否在按预期方式运行，请在自动化帐户的“概述”下，查找“监控”下的“作业统计信息”视图。 将此视图固定到仪表板以便于查看。 成功运行的 runbook 显示为“已完成”，失败的运行显示为“失败”。

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>为电子邮件通知创建 Azure Monitor 读者警报

若要创建使用 Azure Monitor 日志的警报, 请执行以下操作。 作为先决条件, 你需要使用 Log Analytics 工作区链接 Azure Monitor 日志。

1.  在 Azure 门户中，选择“日志搜索”。

2.  创建查询，以便在所选的间隔内按同步组选择错误和警告。 例如：

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  运行查询之后，请选择指示“警报”的钟形图标。

4.  在“基于以下项生成警报”中，请选择“指标度量值”。

    1.  将聚合值设置为“大于”。

    2.  在“大于”后，输入接收通知之前等待的阈值。 在数据同步中可能会出现暂时性的错误。若要减少干扰，请将阈值设置为 5。

5.  在“操作”下，将“电子邮件通知”设置为“是”。 输入所需的电子邮件收件人。

6.  单击“保存”。 现在，当错误发生时，指定的收件人就会收到电子邮件通知。

## <a name="create-an-azure-monitor-view-for-monitoring"></a>创建用于监视的 Azure Monitor 视图

此步骤将创建一个 Azure Monitor 视图以直观地监视所有指定的同步组。 该视图包括多个组件：

-   概述磁贴 - 显示所有同步组具有多少个错误、成功和警告。

-   所有同步组的磁贴 - 显示每个同步组的错误数和警告数。 没有问题的组将不会在此磁贴中显示。

-   每个同步的磁贴 - 显示错误数、成功数和警告数，以及最近的错误消息数。

若要配置 Azure Monitor 视图, 请执行以下操作:

1.  在 "Log Analytics 工作区" 主页上, 选择左侧的加号, 打开 "**视图设计器**"。

2.  在视图设计器的顶部栏中选择“导入”。 然后选择“DataSyncLogOMSView”示例文件。

3.  该示例视图用于管理两个同步组。 编辑此视图以适合你的方案。 打开“编辑”并进行以下更改：

    1.  请根据需要从库创建新的“圆环图和列表”对象。

    2.  在每个磁贴中，使用你的信息更新查询。

        1.  在每个磁贴上，请根据需要更改 TimeStamp_t 间隔。

        2.  在每个同步组的磁贴上，更新同步组名称。

    3.  在每个磁贴上，根据需要更新磁贴。

4.  单击“保存”，视图即已准备就绪。

## <a name="cost-of-this-solution"></a>此解决方案的成本

在大多数情况下，此解决方案是免费的。

**Azure 自动化：** Azure 自动化帐户可能会产生成本，具体取决于使用情况。 每月前 500 分钟的作业运行时间是免费的。 在大多数情况下，此解决方案预计每个月使用不到 500 分钟。 为了避免收费，请计划 Runbook 以两个小时或更长的间隔运行。 有关详细信息，请参阅[自动化定价](https://azure.microsoft.com/pricing/details/automation/)。

**Azure Monitor 日志:** Azure Monitor 日志可能会产生费用, 具体取决于你的使用情况。 免费层包括每日 500 MB 的引入数据。 在大多数情况下，此解决方案预计每天引入的数据不超过 500 MB。 若要减少使用，请使用 runbook 中包含的“仅失败筛选”。 如果你每天使用的数据超过 500 MB，请升级到付费层，以避免在达到此限制时停止分析的风险。 有关详细信息, 请参阅[Azure Monitor 日志定价](https://azure.microsoft.com/pricing/details/log-analytics/)。

## <a name="code-samples"></a>代码示例

从以下位置下载本文介绍的代码示例：

-   [数据同步日志 PowerShell Runbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [数据同步 Azure Monitor 视图](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>后续步骤
有关 SQL 数据同步的详细信息，请参阅：

-   概述 - [使用 Azure SQL 数据同步跨多个云和本地数据库同步数据](sql-database-sync-data.md)
-   设置数据同步
    - 在门户中 - [教程：设置 SQL 数据同步，以在 Azure SQL 数据库和本地 SQL Server 之间同步数据](sql-database-get-started-sql-data-sync.md)
    - 使用 PowerShell
        -  [使用 PowerShell 在多个 Azure SQL 数据库之间进行同步](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [使用 PowerShell 在 Azure SQL 数据库和 SQL Server 本地数据库之间进行同步](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync Agent - [Azure SQL 数据同步的 Data Sync Agent](sql-database-data-sync-agent.md)
-   最佳做法 - [Azure SQL 数据同步最佳做法](sql-database-best-practices-data-sync.md)
-   故障排除 - [排查 Azure SQL 数据同步问题](sql-database-troubleshoot-data-sync.md)
-   更新同步架构
    -   使用 Transact-SQL - [在 Azure SQL 数据同步中自动复制架构更改](sql-database-update-sync-schema.md)
    -   使用 PowerShell - [使用 PowerShell 更新现有同步组中的同步架构](scripts/sql-database-sync-update-schema.md)

有关 SQL 数据库的详细信息，请参阅：

-   [SQL 数据库概述](sql-database-technical-overview.md)
-   [数据库生命周期管理](https://msdn.microsoft.com/library/jj907294.aspx)
