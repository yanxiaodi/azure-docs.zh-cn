---
title: Azure 监视器 PowerShell 快速入门示例
description: 使用 PowerShell 访问 Azure Monitor 功能，如自动缩放、警报、webhook 和搜索活动日志。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/14/2018
ms.author: robb
ms.subservice: ''
ms.openlocfilehash: 886eb8578e004eba3b6fabc1deb42db0fb7fac70
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350241"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Azure 监视器 PowerShell 快速入门示例
本文给出了示例 PowerShell 命令，可帮助用户访问 Azure 监视器的功能。

> [!NOTE]
> 自 2016 年 9 月 25 日起，“Azure Insights”更名为 Azure 监视器。 但是，命名空间及以下命令仍包含“insights”一词。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>设置 PowerShell
如果尚未安装，请在计算机上安装要运行的 PowerShell。 有关详细信息，请参阅[如何安装和配置 PowerShell](/powershell/azure/overview)。

## <a name="examples-in-this-article"></a>本文中的示例
本文中的示例演示了如何使用 Azure 监视器 cmdlet。 还可以在 [Azure 监视器 (Insights) Cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights) 上查看 Azure 监视器 PowerShell cmdlet 的完整列表。

## <a name="sign-in-and-use-subscriptions"></a>登录并使用订阅
首先，登录到 Azure 订阅。

```powershell
Connect-AzAccount
```

会出现登录界面。 登录帐户后，会出现 TenantID 和默认订阅 ID。 所有 Azure cmdlets 都在默认订阅的上下文中工作。 若要查看有权访问的订阅的列表，请使用以下命令：

```powershell
Get-AzSubscription
```

若要查看工作上下文（运行命令所针对的订阅），请使用以下命令：

```powershell
Get-AzContext
```
若要将工作环境更改为另一订阅，请使用以下命令：

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>检索订阅的活动日志
使用 [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet。  下面是一些常见示例。 活动日志保留过去 90 天的操作。 使用此时间之前的日期会生成错误消息。  

查看当前日期/时间，以确认要在以下命令中使用的时间：
```powershell
Get-Date
```

从此时间/日期中获取要显示的日志条目︰

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

在一个时间/日期范围中获取日志条目︰

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

从特定资源组中获取日志条目︰

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

在一个时间/日期范围中从特定资源提供程序获取日志条目︰

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

获取特定调用方的所有日志项︰

```powershell
Get-AzLog -Caller 'myname@company.com'
```

以下命令可从活动日志中检索最后 1000 个事件：

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog` 支持许多其他参数。 有关更多信息，请参阅 `Get-AzLog` 参考。

> [!NOTE]
> `Get-AzLog` 仅提供 15 天的历史记录。 使用 **-MaxRecords** 参数可查询 15 天之外的最后 N 个事件。 要访问超过 15 天的事件，请使用 REST API 或 SDK （使用 SDK 的 C# 示例）。 如果不包括 **StartTime**，则默认值为 **EndTime** 减去一小时。 如果不包括 **EndTime**，则默认值为当前时间。 所有时间均是 UTC 时间。
> 
> 

## <a name="retrieve-alerts-history"></a>检索警报历史记录
若要查看所有警报事件，可以使用以下示例查询 Azure 资源管理器日志。

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

若要查看特定警报规则的历史记录，可以使用 `Get-AzAlertHistory` cmdlet，同时会传入警报规则的资源 ID。

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzAlertHistory` cmdlet 支持各种参数。 有关详细信息，请参阅 [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx)。

## <a name="retrieve-information-on-alert-rules"></a>检索关于警报规则的信息
下面的所有命令可用于名为“montest”的资源组。

查看警报规则的所有属性︰

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

检索某个资源组的所有警报︰

```powershell
Get-AzAlertRule -ResourceGroup montest
```

检索目标资源的所有警报规则设置。 例如，虚拟机上的所有警报规则设置。

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` 支持其他参数。 有关详细信息，请参阅 [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx)。

## <a name="create-metric-alerts"></a>创建指标警报
可使用 `Add-AlertRule` cmdlet 来创建、更新或禁用警报规则。

可以分别使用 `New-AzAlertRuleEmail` 和 `New-AzAlertRuleWebhook` 创建电子邮件和 webhook 属性。 在警报规则 cmdlet 中，将这些属性作为操作分配给警报规则的“操作”属性。

下表描述了用于使用指标创建警报的参数和值。

| 参数 | value |
| --- | --- |
| 姓名 |simpletestdiskwrite |
| 此警报规则的位置 |East US |
| resourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| 创建的警报的 MetricName |\PhysicalDisk(_Total)\Disk Writes/sec。See the `Get-MetricDefinitions` cmdlet about how to retrieve the exact metric names |
| 运算符 |GreaterThan |
| 阈值（此指标的计数/秒） |1 |
| WindowSize（hh:mm:ss 格式） |00:05:00 |
| 聚合（在这种情况下使用平均计数的指标的统计信息） |Average |
| 自定义电子邮件（字符串数组） |'foo@example.com','bar@example.com' |
| 向所有者、参与者和读者发送电子邮件 |-SendToServiceOwners |

创建电子邮件操作

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

创建 Webhook 操作

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

在经典虚拟机上创建关于 CPU %指标的警报规则

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

检索警报规则

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

如果给定属性已存在警报规则，则添加警报 cmdlet 还会更新该规则。 若要禁用警报规则，请包括 **-DisableRule** 参数。

## <a name="get-a-list-of-available-metrics-for-alerts"></a>获取警报的可用指标的列表
可以使用 `Get-AzMetricDefinition` cmdlet 来查看针对特定资源的所有指标的列表。

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

以下示例会生成一张表，其中包含指标名称和单位。

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

`Get-AzMetricDefinition` 的可用选项的完整列表位于 [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx) 中。

## <a name="create-and-manage-activity-log-alerts"></a>创建和管理活动日志警报
可以使用 `Set-AzActivityLogAlert` cmdlet 来设置活动日志警报。 活动日志警报会要求你首先将条件定义为条件字典，然后创建使用这些条件的警报。

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

其他 Webhook 属性都是可选的。 可以使用 `Get-AzActivityLogAlert` 返回活动日志警报的内容。

## <a name="create-and-manage-autoscale-settings"></a>创建和管理自动缩放设置
资源（例如 Web 应用、VM、云服务或虚拟机规模集）只能有一种为其配置的自动缩放设置。
但是，每个自动缩放设置可具有多个配置文件。 例如，一个用于基于性能的缩放配置文件，另一个用于基于计划的配置文件。 每个配置文件可以为其配置多个规则。 有关自动缩放的详细信息，请参阅[如何自动缩放应用程序](../../cloud-services/cloud-services-how-to-scale-portal.md)。

请使用以下步骤：

1. 创建规则。
2. 创建配置文件，将之前创建的规则映射到该配置文件。
3. 可选：通过配置 webhook 和电子邮件属性，创建自动缩放通知。
4. 通过映射在前面步骤中创建的配置文件和通知，创建自动缩放设置，并使用目标资源上的名称。

以下示例演示了如何使用 CPU 使用率指标为基于 Windows 操作系统的虚拟机规模集创建自动缩放设置。

首先，创建向外扩展规则，实例计数增加。

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

随后，创建向内扩展规则，实例计数减少。

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

然后，创建规则的配置文件。

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

创建 webhook 属性。

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

创建自动缩放设置的通知属性，包括电子邮件和之前创建的 webhook。

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

最后，创建自动缩放设置以添加之前创建的配置文件。 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

有关管理自动缩放设置的详细信息，请参阅 [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx)。

## <a name="autoscale-history"></a>自动缩放历史记录
以下示例演示了如何查看近期的自动缩放和警报事件。 使用活动日志搜索来查看自动缩放历史记录。

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

可以使用 `Get-AzAutoScaleHistory` cmdlet 来检索自动缩放历史记录。

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

有关详细信息，请参阅 [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx)。

### <a name="view-details-for-an-autoscale-setting"></a>查看自动缩放设置的详细信息
可以使用 `Get-Autoscalesetting` cmdlet 来检索有关自动缩放设置的详细信息。

以下示例显示了关于资源组 myrg1 中所有自动缩放设置的详细信息。

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

以下示例显示了关于资源组 myrg1 中所有自动缩放设置的详细信息，特别是名为 MyScaleVMSSSetting 的自动缩放设置的详细信息。

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>删除自动缩放设置
可以使用 `Remove-Autoscalesetting` cmdlet 来删除自动缩放设置。

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>管理活动日志的日志配置文件
可以创建*日志配置文件*并从活动日志中将数据导出到存储帐户，并且可以为其配置数据保留期。 也可以选择将数据流式传输到事件中心。 目前仅预览版中具有此功能，并且每个订阅只能创建一个日志配置文件。 可以对当前订阅使用以下 cmdlet 来创建和管理日志配置文件。 也可以选择特定的订阅。 虽然 PowerShell 默认为当前订阅，但可以使用 `Set-AzContext` 随时对此进行更改。 可以配置活动日志以将数据路由到该订阅中的任何存储帐户或事件中心。 以 JSON 格式将数据写为 blob 文件。

### <a name="get-a-log-profile"></a>获取日志配置文件
若要提取现有日志配置文件，请使用 `Get-AzLogProfile` cmdlet。

### <a name="add-a-log-profile-without-data-retention"></a>添加没有数据保留期的日志配置文件
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>删除日志配置文件
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>添加有数据保留期的日志配置文件
可以用天数将 **-RetentionInDays** 属性指定为一个正整数，将保留其中的数据。

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>添加具有保留期和 EventHub 的日志配置文件
除了将数据路由到存储帐户，还可以流式传输到事件中心。 在此预览版本中，存储帐户配置是必需的，但事件中心配置是可选的。

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>配置诊断日志
许多 Azure 服务提供额外的日志和遥测数据，以便执行以下一项或多项操作： 
 - 配置为将数据存储在 Azure 存储帐户中
 - 发送到事件中心
 - 发送到 Log Analytics 工作区。 

只能在资源级别执行该操作。 存储帐户或事件中心应与配置诊断设置的目标资源处于相同的区域中。

### <a name="get-diagnostic-setting"></a>获取诊断设置
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

禁用诊断设置

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

启用没有保留期的诊断设置

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

启用有保留期的诊断设置

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

为特定日志类别启用有保留期的诊断设置

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

启用事件中心的诊断设置

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

启用 Log Analytics 的诊断设置

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

请注意，WorkspaceId 属性采用工作区的“资源 ID”。 可以使用以下命令获取 Log Analytics 工作区的资源 ID：

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

可以组合这些命令以将数据发送到多个目标。

