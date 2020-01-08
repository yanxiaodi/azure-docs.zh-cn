---
title: 使用 Azure Log Analytics 优化 System Center Operations Manager 环境 | Microsoft 文档
description: 可以使用 System Center Operations Manager 运行状况检查解决方案定期评估环境的风险和运行状况。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 49aad8b1-3e05-4588-956c-6fdd7715cda1
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: magoedte
ms.openlocfilehash: 27b55af74a713c51655891df8c852ff44cd3744a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60401605"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>使用 System Center Operations Manager 运行状况检查（预览版）解决方案优化环境

![“System Center Operations Manager 运行状况检查”符号](./media/scom-assessment/scom-assessment-symbol.png)

可以使用 System Center Operations Manager 运行状况检查解决方案定期评估 System Center Operations Manager 管理组的风险和运行状况。 本文将帮助你安装、配置和使用该解决方案，以便针对潜在问题采取纠正措施。

此解决方案提供了特定于已部署服务器基础结构的建议优先级列表。 这些建议跨四个重点领域进行了分类，将帮助你快速了解风险并采取纠正措施。

提出的建议基于 Microsoft 工程师从数千次客户拜访中所获得的知识和经验。 每一项建议都会就为何问题可能对你至关重要以及如何实施建议更改提供相关指导。

可以选择对组织最重要的重点领域，并跟踪一个运行正常无风险环境的进度。

在解决方案并执行评估后，会在基础结构的“System Center Operations Manager 运行状况检查”仪表板上显示有关重点领域的摘要信息。  以下部分介绍如何使用“System Center Operations Manager 运行状况检查”仪表板上的信息，可以在其中查看并针对 Operations Manager 环境采取建议的操作。 

![System Center Operations Manager 解决方案磁贴](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![“System Center Operations Manager 运行状况检查”仪表板](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>安装和配置解决方案

该解决方案适用于 Microsoft System Center 2012 Operations Manager Service Pack 1、 Microsoft System Center 2012 R2 Operations Manager、 Microsoft System Center 2016 Operations Manager、 Microsoft System Center 2016 Operations Manager 和 Microsoft SystemCenter Operations Manager 1807

使用以下信息安装和配置解决方案。

- 在 Log Analytics 中使用运行状况检查解决方案之前，必须先安装该解决方案。 可从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview)安装该解决方案。

- 将解决方案添加到工作区以后，仪表板上的“System Center Operations Manager 运行状况检查”磁贴会显示“需要更多的配置”这样一条消息。  单击该磁贴，并按照页面中所述的配置步骤操作

  ![System Center Operations Manager 仪表板磁贴](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> 可以使用脚本配置 System Center Operations Manager，只需执行 Log Analytics 中解决方案的配置页所述的步骤即可。

 若要通过 Operations Manager Operations 控制台配置评估，请按以下顺序执行以下步骤：
1. [设置 System Center Operations Manager 运行状况检查的运行方式帐户](#operations-manager-run-as-accounts-for-log-analytics)  
2. 配置 System Center Operations Manager 运行状况检查规则

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>System Center Operations Manager 运行状况检查数据收集详细信息

System Center Operations Manager 运行状况检查解决方案从以下源收集数据：

* 注册表
* Windows Management Instrumentation (WMI)
* 事件日志
* 文件数据
* 使用 PowerShell 和 SQL 查询直接从 Operations Manager 收集，以及从指定的管理服务器收集。  

数据从管理服务器收集，并每隔七天转发到 Log Analytics。  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Log Analytics 的 Operations Manager 运行方式帐户

Log Analytics 基于工作负荷的管理包生成，提供增值服务。 每个工作负荷需要特定于工作负荷的权限，才能在不同的安全上下文（例如域用户帐户）中运行管理包。 使用特权凭据配置 Operations Manager 运行方式帐户。 有关更多信息，请参阅 Operations Manager 文档中的[如何创建运行方式帐户](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx)。

使用以下信息为 System Center Operations Manager 运行状况检查设置 Operations Manager 运行方式帐户。

### <a name="set-the-run-as-account"></a>设置运行方式帐户

在继续下一步之前，运行方式帐户必须满足以下要求：

* 该帐户是域用户帐户，并且是支持任何 Operations Manager 角色的所有服务器（管理服务器，以及托管操作数据、数据仓库和 ACS 数据库、报告组件、Web 控制台和网关服务器的 SQL Server）上的本地管理员组的成员。
* 正在评估管理组的 Operation Manager 管理员角色
* 如果该帐户没有 SQL sysadmin 权限，请执行该[脚本](#sql-script-to-grant-granular-permissions-to-the-run-as-account)，向托管一个或所有 Operations Manager 数据库的每个 SQL Server 实例上的帐户授予粒度权限。

1. 在 Operations Manager 控制台中，选择“管理”导航按钮。 
2. 在“运行方式配置”下，单击“帐户”。  
3. 在“创建运行方式帐户”向导中的“简介”页上，单击“下一步”。   
4. 在“常规属性”页上的“运行方式帐户类型:”列表中，选择“Windows”。   
5. 在“显示名称”文本框中键入显示名称，并选择性地在“说明”框中键入说明，单击“下一步”。   
6. 在“分发安全性”页上，选择“更安全”。  
7. 单击**创建**。  

创建运行方式帐户后，需要将管理组中的管理服务器指定为该帐户的目标，并将其关联到某个预定义的运行方式配置文件，以便能够使用凭据运行工作流。  

1. 在“运行方式配置”下选择“帐户”，在结果窗格中，双击前面创建的帐户。  
2. 在“分发”选项卡上，单击“选定的计算机”框旁边的“添加”，添加要将该帐户分发到的管理服务器。     单击“确定”两次以保存更改。 
3. 在“运行方式配置”下，单击“配置文件”。  
4. 搜索“SCOM 评估配置文件”。 
5. 配置文件名称应为：Microsoft System Center Operations Manager 运行状况检查运行方式配置文件。 
6. 右键单击该配置文件并更新其属性，添加最近创建的运行方式帐户。

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>向运行方式帐户授予具体权限的 SQL 脚本

执行以下 SQL 脚本，向 Operations Manager 使用的、托管操作数据、数据仓库和 ACS 数据库的 SQL Server 实例上的运行方式帐户授予所需的权限。

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>配置运行状况检查规则

System Center Operations Manager 运行状况检查解决方案的管理包中包含一个名为“Microsoft System Center Operations Manager 运行运行状况检查规则”的规则。  此规则负责执行运行状况检查。 若要启用该规则并配置频率，请使用以下过程。

默认情况下，禁用 Microsoft System Center Operations Manager 运行运行状况检查规则。 若要执行运行状况检查，必须在管理服务器上启用该规则。 使用以下步骤。

#### <a name="enable-the-rule-for-a-specific-management-server"></a>为特定的管理服务器启用规则

1. 在中**创作**工作区中的 Operations Manager 操作控制台中，搜索规则*Microsoft System Center Operations Manager 运行运行状况检查规则*中**规则**窗格。
2. 在搜索结果中，选择包含文本“类型:  管理服务器”的规则。
3. 右键单击该规则，并单击“重写” > “对于类为管理服务器的特定对象”   。
4.  在可用管理服务器列表中，选择要在其上运行该规则的管理服务器。  这应该是前面配置的，要与运行方式帐户关联的同一个管理服务器。
5.  请务必将“已启用”参数值的重写值更改为 **True**。 <br><br> ![重写参数](./media/scom-assessment/rule.png)

    继续在此窗口中操作，使用下一个过程配置运行频率。

#### <a name="configure-the-run-frequency"></a>配置运行频率

评估默认配置为每隔 10,080 分钟（即七天）运行一次。 最小可以将该值重写为 1440 分钟（即一天）。 该值表示连续运行评估的最小间隔时间。 若要重写该间隔，请使用以下步骤。

1. 在 Operations Manager 控制台的“创作”工作区的“规则”部分中，搜索规则“Microsoft System Center Operations Manager 运行运行状况检查规则”。   
2. 在搜索结果中，选择包含文本“类型:  管理服务器”的规则。
3. 右键单击该规则，并单击“重写规则” > “对于类为管理服务器的所有对象”   。
4. 将“间隔”参数值更改为所需的间隔值。  在以下示例中，该值设置为 1440 分钟（一天）。<br><br> ![间隔参数](./media/scom-assessment/interval.png)<br>  

    如果设置的值小于 1440 分钟，该规则将按一天的间隔运行。 在本示例中，规则将忽略间隔值，按一天的频率运行。


## <a name="understanding-how-recommendations-are-prioritized"></a>了解如何设置建议的优先级

每项建议都指定有一个权重值，用于标识该建议的相对重要性。 仅显示 10 个最重要的建议。

### <a name="how-weights-are-calculated"></a>如何计算权重

权重是基于三个关键因素的聚合值：

- 所发现的问题会导致不良后果的*概率*。 概率较高相当于建议的总体分数较高。
- 问题对组织的*影响*（如果它确实会导致不良后果）。 影响较大相当于建议的总体分数更高。
- 实施建议所需的*工作*。 工作量较大相当于建议的总体分数较低。

每一项建议的权重表示为可用于每个重点区域的总分百分比。 例如，如果“可用性和业务连续性”重点区域中的建议评分为 5%，则实施该建议可将总体可用性和业务连续性的评分提高 5%。

### <a name="focus-areas"></a>重点区域

**可用性和业务连续性** - 该重点区域显示针对基础结构和业务保护的服务可用性、可恢复性建议。

**性能和可扩展性** - 该重点区域显示帮助组织实现 IT 基础结构扩展的建议，确保 IT 环境满足当前性能要求，并且能够应对不断变化的基础结构需求。

**升级、迁移和部署** - 该重点区域显示帮助升级、迁移并将 SQL Server 部署到现有基础结构的建议。

**操作和监视** - 该重点关注领域显示帮助简化 IT 运营、实施预防性维护并使性能最大化的建议。

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>每个重点区域的分数都应该以 100% 为目标吗？

不一定。 建议基于 Microsoft 工程师从数千次客户拜访中所获得的知识和经验。 但是，服务器基础结构各不相同，或多或少都需要一些具体的相关建议。 例如，如果虚拟机未公开到 Internet，那么一些安全建议可能没什么用。 某些可用性建议可能不太适用于提供低优先级临时数据收集和报告的服务。 对于成熟企业至关重要的问题对于一家初创公司可能不太重要。 可能要确定哪些重点区域是要优先考虑的，并查看分数随时间的变化。

每项建议都会提供有关该建议为何重要的指导。 考虑 IT 服务的性质和组织的业务需求，请使用本指导评估实施建议对你是否适用。

## <a name="use-health-check-focus-area-recommendations"></a>使用运行状况检查重点区域建议

在 Log Analytics 中使用运行状况检查解决方案之前，必须先安装该解决方案。 若要详细了解如何安装解决方案，请参阅[安装管理解决方案](../../azure-monitor/insights/solutions.md)。 安装完成后，可以通过使用 Azure 门户中工作区的“概述”  页上的“System Center Operations Manager 运行状况检查”磁贴来查看建议摘要。

查看概述的针对基础结构的合规性评估，并深入分析建议。

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>查看针对重点区域的建议并采取纠正措施
1. 通过 [https://portal.azure.com](https://portal.azure.com) 登录到 Azure 门户。
2. 在 Azure 门户中，单击左下角的“更多服务”  。 在资源列表中，键入“Log Analytics”  。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”  。
3. 在 Log Analytics 订阅窗格中选择一个工作区，再单击“工作区摘要”  菜单项。  
4. 在“概述”页上，单击“System Center Operations Manager 运行状况检查”磁贴。  
5. 在“System Center Operations Manager 运行状况检查”页上，查看某个重点区域边栏选项卡中的摘要信息，并单击其中一个查看针对该重点区域的建议。 
6. 在任何重点区域页上，均可以查看针对环境所做的优先级建议。 单击“受影响的对象”  下的建议，以查看有关为何给出此建议的详细信息。<br><br> ![重点区域](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. 可以采取“建议的操作”  中建议的纠正操作。 解决该项后，以后的评估将记录已执行的建议操作，并且将提高合规性分数。 已更正的项会显示为“通过的对象”  。

## <a name="ignore-recommendations"></a>忽略建议

如果有要忽略的建议，可以创建 Log Analytics 用来防止建议出现在评估结果中的文本文件。

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>确定要忽略的建议
1. 在 Azure 门户中所选工作区对应的 Log Analytics 工作区页上，单击“日志搜索”菜单项。 
2. 使用以下查询列出对于环境中计算机失败的建议。

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > 如果工作区已升级到[新 Log Analytics 查询语言](../../azure-monitor/log-query/log-query-overview.md)，则上述查询会更改为如下所示。
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    下面是显示日志搜索查询的屏幕截图：<br><br> ![日志搜索](./media/scom-assessment/scom-log-search.png)<br>

3. 选择要忽略的建议。 将 RecommendationId 的值用于接下来的过程。

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>创建和使用 IgnoreRecommendations.txt 文本文件

1. 创建一个名为 IgnoreRecommendations.txt 的文件。
2. 在单独的行上粘贴或键入要 Log Analytics 忽略的每个建议的 RecommendationId，保存并关闭该文件。
3. 将以下文件夹中的文件置于每台要让 Log Analytics 忽略建议的计算机上。
4. 在 Operations Manager 管理服务器上 - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server。

### <a name="to-verify-that-recommendations-are-ignored"></a>验证建议是否已被忽略

1. 在下一次计划评估运行后（默认情况下每隔七天运行一次），指定的建议会被标记为“已忽略”，不会在运行状况检查仪表板上显示。
2. 可以使用以下日志搜索查询列出所有已忽略的建议。

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > 如果工作区已升级到[新 Log Analytics 查询语言](../../azure-monitor/log-query/log-query-overview.md)，则上述查询会更改为如下所示。
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. 如果以后决定想要查看已忽略的建议，请删除任何 IgnoreRecommendations.txt 文件，或者可以从中删除 RecommendationID。

## <a name="system-center-operations-manager-health-check-solution-faq"></a>System Center Operations Manager 运行状况检查解决方案常见问题解答

*我已将运行状况检查解决方案添加到了 Log Analytics 工作区。但没有看到建议。为什么看不到呢？* 添加解决方案后，使用以下步骤在 Log Analytics 仪表板上查看建议。  

- [设置 System Center Operations Manager 运行状况检查的运行方式帐户](#operations-manager-run-as-accounts-for-log-analytics)  
- [配置 System Center Operations Manager 运行状况检查规则](#configure-the-health-check-rule)


是否有某种方法可配置检查的运行频率？  是的。 请参阅[配置运行频率](#configure-the-run-frequency)。

如果添加 System Center Operations Manager 运行状况检查解决方案后发现另一台服务器，那么是否会检查它？  是的，发现之后，即会对它进行检查，默认情况下每隔七天检查一次。

*执行数据收集的进程的名称是什么？* AdvisorAssessment.exe

*AdvisorAssessment.exe 进程在哪个位置运行？* AdvisorAssessment.exe 在启用运行状况检查规则的管理服务器的 HealthService 进程下运行。 使用该进程可以通过远程数据收集来发现整个环境。

*收集数据需要多长时间？* 在服务器上收集数据大约需要一小时。 在包含许多 Operations Manager 实例或数据库的环境中，时间可能更长。

*如果将评估间隔设置为小于 1440 分钟会发生什么情况？* 评估预先配置的最高运行频率为每天一次。 如果将间隔值重写为小于 1440 分钟的值，评估将使用 1440 分钟作为间隔值。

如何知道是否存在不符合先决条件的情况？  如果执行了运行状况检查但未看到结果，则有可能是检查不符合某些先决条件。 可以在日志搜索中执行查询 `Operation Solution=SCOMAssessment` 和 `SCOMAssessmentRecommendation FocusArea=Prerequisites`，确定不符合哪些先决条件。

*先决条件错误中包含一条 `Failed to connect to the SQL Instance (….).` 消息。问题出在哪里？* 收集数据的进程 AdvisorAssessment.exe 在管理服务器的 HealthService 进程下运行。 在运行状况检查过程中，该进程会尝试连接到 Operations Manager 数据库所在的 SQL Server。 如果防火墙规则阻止与 SQL Server 实例建立连接，则可能会出现此错误。

*收集的数据类型是什么？* 通过 Windows PowerShell、SQL 查询和文件信息收集器收集以下类型的数据：- WMI 数据 - 注册表数据 - EventLog 数据 - Operations Manager 数据。

*为何需要配置运行方式帐户？* 使用 Operations Manager 时，会运行各种 SQL 查询。 为了运行这些查询，必须使用一个具有所需权限的运行方式帐户。 此外，查询 WMI 需要用到本地管理员凭据。

*仅显示前 10 条建议的原因* 我们不会提供名目繁多的详尽任务列表，只是建议先按优先级着重实施建议的方法。 在解决这些建议后，其他建议将变为可用。 如果想要查看详细的列表，可以使用日志搜索查看所有建议。

*有没有方法来忽略建议？* 是的，请参阅[忽略建议](#ignore-recommendations)。


## <a name="next-steps"></a>后续步骤

- [搜索日志](../../azure-monitor/log-query/log-query-overview.md)以了解如何分析详细的 System Center Operations Manager 运行状况检查数据和建议。
