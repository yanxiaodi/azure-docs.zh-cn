---
title: 创建 & 查询 Azure Data Lake Analytics-PowerShell
description: 使用 Azure PowerShell 创建 Azure Data Lake Analytics 帐户，并提交 U-SQL 作业。
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.topic: conceptual
ms.date: 05/04/2017
ms.openlocfilehash: 8b176434ce450382cc6463e7d21d341a74581ed8
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316611"
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>通过 Azure PowerShell 开始使用 Azure Data Lake Analytics

[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

了解如何使用 Azure PowerShell 创建 Azure Data Lake Analytics 帐户，并提交并运行 U-SQL 作业。 有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

开始学习本教程之前，必须做好以下准备：

* **一个 Azure Data Lake Analytics 帐户**。 请参阅 [Data Lake Analytics 入门](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)。
* **配备 Azure PowerShell 的工作站**。 请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

## <a name="log-in-to-azure"></a>登录 Azure

本教程假定观看者已熟悉如何使用 Azure PowerShell。 具体而言，观看者需要知道如何登录到 Azure。 如需帮助，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

使用订阅名称登录：

```powershell
Connect-AzAccount -SubscriptionName "ContosoSubscription"
```

除订阅名称外，还可使用订阅 ID 登录：

```powershell
Connect-AzAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

如果成功，此命令的输出将类似于以下文本：

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>准备教程

本教程中的 PowerShell 代码片段使用上述变量来存储该信息：

```powershell
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>获取有关 Data Lake Analytics 帐户的信息

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>提交 U-SQL 作业

创建一个 PowerShell 变量以保存 U-SQL 脚本。

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

使用 `Submit-AdlJob` cmdlet 和 `-Script` 参数提交脚本文本。

```powershell
$job = Submit-AdlJob -Account $adla -Name "My Job" -Script $script
```

作为替代方法，可以使用 `-ScriptPath` 参数提交脚本文件：

```powershell
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" -ScriptPath $filename
```

使用 `Get-AdlJob` 获取作业的状态。 

```powershell
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

可以使用 `Wait-AdlJob` cmdlet，而不必反复调用 Get-AdlJob 直至作业完成。

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

使用 `Export-AdlStoreItem` 下载输出文件。

```powershell
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>请参阅

* 若要了解使用其他工具来完成此教程，请单击页面顶部的选项卡选择器。
* 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
* 有关管理任务，请参阅[使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)。
