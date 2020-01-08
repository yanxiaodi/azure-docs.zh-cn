---
title: 使用 PowerShell 监视和管理 Azure 流分析作业
description: 本文说明如何使用 Azure PowerShell 和 cmdlet 监视和管理 Azure 流分析作业。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: cdc09973a192924c5b9a81cd4ed49b9f36fc0eb1
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "67612297"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>使用 Azure PowerShell cmdlet 监视和管理流分析作业
了解如何使用可执行基本流分析任务的 Azure PowerShell cmdlet 和 PowerShell 脚本来监视和管理流分析资源。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>针对流分析运行 Azure PowerShell cmdlet 的先决条件
* 在订阅中创建 Azure 资源组。 下面是 Azure PowerShell 脚本示例。 有关 Azure PowerShell 的信息，请参阅[安装和配置 Azure PowerShell](/powershell/azure/overview)；  

Azure PowerShell 0.9.8：  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0：

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription –SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> 以编程方式创建的流分析作业默认情况下并不启用监视功能。  可以在 Azure 门户中手动启用监视功能，只需导航到作业的“监视”页，并单击“启用”按钮即可；也可以通过编程方式来执行此操作，只需遵循 [Azure 流分析 - 以编程方式监视流分析作业](stream-analytics-monitor-jobs.md)中的步骤即可。
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>适用于流分析的 Azure PowerShell cmdlet
下面的 Azure PowerShell cmdlet 可用于监视和管理 Azure 流分析作业。 请注意，Azure PowerShell 具有不同版本。 
**在所列示例中，第一个命令用于 Azure PowerShell 0.9.8，第二个命令用于 Azure PowerShell 1.0。** 命令中的 Azure PowerShell 1.0 命令将始终具有 "Az"。

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
列出所有在 Azure 订阅或指定资源组中定义的流分析作业，或者获取有关某个资源组中特定作业的作业信息。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsJob
```

此 PowerShell 命令返回有关在 Azure 订阅中所有流分析作业的信息。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

此 PowerShell 命令在资源组 StreamAnalytics-Default-Central-US 中返回有关所有流分析作业的信息。

**示例 3**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

此 PowerShell 命令在资源组 StreamAnalytics-Default-Central-US 中返回有关流分析作业 StreamingJob 的信息。

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
列出在指定流分析作业中定义的所有输入，或获取有关特定输入的信息。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

此 PowerShell 命令返回在作业 StreamingJob 中定义的所有输入的相关信息。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
```

此 PowerShell 命令返回在作业 StreamingJob 中定义的名为 EntryStream 的输入的相关信息。

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
列出在指定流分析作业中定义的所有输出，或获取有关特定输出的信息。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

此 PowerShell 命令返回在作业 StreamingJob 中定义的输出的相关信息。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
```

此 PowerShell 命令返回在作业 StreamingJob 中定义的名为 Output 的输出的相关信息。

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
获取指定区域中流式处理单元配额的相关信息。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsQuota –Location "Central US" 
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsQuota –Location "Central US" 
```

此 PowerShell 命令返回有关美中地区配额和流式处理单位使用情况的信息。

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
获取在流分析作业中定义的特定转换的相关信息。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
```

Azure PowerShell 1.0：  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
```

此 PowerShell 命令返回作业 StreamingJob 中名为 StreamingJob 的转换的相关信息。

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
在流分析作业中创建新的输入，或者更新现有的指定输入。

输入的名称可以在 .json 文件中指定，也可以在命令行中指定。 如果在两处同时指定，则命令行的名称必须与文件中的名称相同。

如果所指定的输入已存在，而且并未指定 -Force 参数，则该 cmdlet 会询问是否要替换现有输入。

如果指定了 –Force 参数，同时又指定了一个现有的输入名称，则会在不进行确认的情况下替换该输入。

有关 JSON 文件结构和内容的详细信息, 请参阅[流分析管理 REST API 引用库][stream.analytics.rest.api.reference]的[创建输入 (Azure 流分析)][msdn-rest-api-create-stream-analytics-input]部分。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
```

此 PowerShell 命令从文件 Input.json 创建新的输入。 如果已定义了现有的输入，在输入定义文件中指定了名称，则该 cmdlet 会询问是否替换该输入。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
```

此 PowerShell 命令在名为 EntryStream 的作业中创建新的输入。 如果已定义了使用此名称的现有输入，则该 cmdlet 会询问是否替换该输入。

**示例 3**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
```

此 PowerShell 命令会使用文件中的定义来替换名为 EntryStream 的现有输入源的定义。

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
在 Microsoft Azure 中创建新的流分析作业，或者更新现有的指定作业的定义。

作业的名称可以在 .json 文件中指定，也可以在命令行中指定。 如果在两处同时指定，则命令行的名称必须与文件中的名称相同。

如果所指定的作业名称已存在，而且并未指定 -Force 参数，则该 cmdlet 会询问是否要替换现有作业。

如果指定了 –Force 参数，同时又指定了一个现有的作业名称，则会在不进行确认的情况下替换作业定义。

有关 JSON 文件结构和内容的详细信息, 请参阅[流分析管理 REST API 引用库][stream.analytics.rest.api.reference]的[创建流分析作业][msdn-rest-api-create-stream-analytics-job]部分。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
```

此 PowerShell 命令在 JobDefinition.json 的定义中创建新的作业。 如果已定义了现有的作业，在作业定义文件中指定了名称，则该 cmdlet 会询问是否替换该作业。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
```

此 PowerShell 命令替换 StreamingJob 的作业定义。

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
在流分析作业中创建新的输出，或者更新现有的输出。  

输出的名称可以在 .json 文件中指定，也可以在命令行中指定。 如果在两处同时指定，则命令行的名称必须与文件中的名称相同。

如果所指定的输出已存在，而且未指定 -Force 参数，则该 cmdlet 会询问是否要替换现有输出。

如果指定了 –Force 参数，同时又指定了一个现有的输出名称，则会在不进行确认的情况下替换该输出。

有关 JSON 文件结构和内容的详细信息, 请参阅[流分析管理 REST API 引用库][stream.analytics.rest.api.reference]的[创建输出 (Azure 流分析)][msdn-rest-api-create-stream-analytics-output]部分。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
```

此 PowerShell 命令在 StreamingJob 作业中创建新的名为“output”的输出。 如果已定义了使用此名称的现有输出，则该 cmdlet 会询问是否替换该输出。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
```

此 PowerShell 命令替换作业 StreamingJob 中“output”的定义。

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
在流分析作业中创建新的转换，或者更新现有转换。

转换的名称可以在 .json 文件中指定，也可以在命令行中指定。 如果在两处同时指定，则命令行的名称必须与文件中的名称相同。

如果所指定的转换已存在，而且未指定 -Force 参数，则该 cmdlet 会询问是否要替换现有转换。

如果指定了 –Force 参数，同时又指定了一个现有的转换名称，则会在不进行确认的情况下替换转换。

有关 JSON 文件结构和内容的详细信息, 请参阅[流分析管理 REST API 引用库][stream.analytics.rest.api.reference]的[创建转换 (Azure 流分析)][msdn-rest-api-create-stream-analytics-transformation]部分。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
```

此 PowerShell 命令在作业 StreamingJob 中创建名为 StreamingJobTransform 的新转换。 如果已定义了使用此名称的现有转换，则该 cmdlet 会询问是否替换该转换。

**示例 2**

Azure PowerShell 0.9.8：  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
```

Azure PowerShell 1.0：  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
```

 此 PowerShell 命令替换作业 StreamingJob 中 StreamingJobTransform 的定义。

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
以异步方式从 Microsoft Azure 的流分析作业中删除特定的输入。  
如果指定 -Force 参数，则会在不确认的情况下删除输入。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
```

Azure PowerShell 1.0：  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
```

此 PowerShell 命令删除作业 StreamingJob 中的输入 EventStream。  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
以异步方式删除 Microsoft Azure 中的特定流分析作业。  
如果指定 -Force 参数，则会在不确认的情况下删除作业。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Azure PowerShell 1.0：  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

此 PowerShell 命令删除作业 StreamingJob。  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
以异步方式从 Microsoft Azure 的流分析作业中删除特定的输出。  
如果指定 -Force 参数，则会在不确认的情况下删除输出。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Azure PowerShell 1.0：  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

此 PowerShell 命令删除作业 StreamingJob 中的输出 Output。  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
以异步方式部署和启动 Microsoft Azure 中的流分析作业。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0：  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

此 PowerShell 命令启动作业 StreamingJob，并将自定义输出开始时间设置为 2012 年 12 月 12 日 12:12:12（UTC）。

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
以异步方式停止流分析作业在 Microsoft Azure 中的运行，并取消分配所使用的资源。 通过 Azure 门户和管理 API，仍可获取订阅中的作业定义和元数据，因此作业可以编辑和重启。 处于停止状态的作业不收费。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

Azure PowerShell 1.0：  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
```

此 PowerShell 命令停止作业 StreamingJob。  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
测试流分析能否连接到指定的输入。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
```

Azure PowerShell 1.0：  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
```

此 PowerShell 命令测试 StreamingJob 中输入 EntryStream 的连接状态。  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Get-azurestreamanalyticsoutput |AzStreamAnalyticsOutput
测试流分析能否连接到指定的输出。

**示例 1**

Azure PowerShell 0.9.8：  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

Azure PowerShell 1.0：  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
```

此 PowerShell 命令测试 StreamingJob 中输出 Output 的连接状态。  

## <a name="get-support"></a>获取支持
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。 

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

