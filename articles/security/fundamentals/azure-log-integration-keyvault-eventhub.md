---
title: 使用事件中心集成来自 Azure Key Vault 的日志 | Microsoft Docs
description: 本教程提供了使用 Azure 日志集成使 Key Vault 日志可供 SIEM 使用需要执行的步骤
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 15223c59f270dc562e521697186cfaf7f30073b9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004111"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure 日志集成教程：使用事件中心处理 Azure Key Vault 事件

>[!IMPORTANT]
> Azure 日志集成功能将由06/15/2019 弃用。 AzLog 下载已于 2018 年 6 月 27 日禁用。 有关下一步该怎么做的指导，请查看文章[使用 Azure Monitor 与 SIEM 工具集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

可以使用 Azure 日志集成检索已记录的事件并使其可供安全信息和事件管理 (SIEM) 系统使用。 本教程展示如何使用 Azure 日志集成处理通过 Azure 事件中心获取的日志。

集成 Azure 日志的首选方法是，使用 SIEM 供应商的 Azure Monitor 连接器并遵循这些[说明](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md)。 但是，如果你的 SIEM 供应商未提供 Azure Monitor 连接器，你或许可以使用 Azure 日志集成作为临时解决方案（如果你的 SIEM 受 Azure 日志集成支持），直到有此类连接器可用。

 
应使用本教程，按照以下示例步骤操作，了解 Azure 日志集成和事件中心如何协同工作以及每个步骤如何支持该解决方案。 然后可使用此处学到的内容，创建自己的步骤来满足公司的独特需求。

> [!WARNING]
> 本教程中的步骤和命令不适用于复制和粘贴。 他们仅为示例。 请勿在实时环境中“原封不动地”使用 PowerShell 命令。 应根据独特环境对它们进行自定义。


本教程将指导你完成将 Azure Key Vault 活动记录到事件中心内并使其作为 JSON 文件供 SIEM 系统使用的过程。 然后，可以配置 SIEM 系统来处理 JSON 文件。

>[!NOTE]
>本教程中的大多数步骤都涉及配置 key vault、 存储帐户和事件中心。 具体的 Azure 日志集成步骤位于本教程的末尾。 请勿在生产环境中执行这些步骤。 它们仅适用于实验室环境。 自定义这些步骤后，方可在生产中使用。

沿途提供的信息可帮助你了解每个步骤背后的原因。 其他文章的链接提供了有关特定主题的更多详细信息。

有关本教程提到的服务的详细信息，请参阅： 

- [Azure Key Vault](/azure/key-vault/key-vault-overview)
- [Azure 事件中心](/azure/event-hubs/event-hubs-what-is-event-hubs)
- [Azure 日志集成](azure-log-integration-overview.md)


## <a name="initial-setup"></a>初始设置

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

要完成本文中的步骤，需要以下各项：

* 一个 Azure 订阅和一个在该订阅中具有管理员权限的帐户。 如果没有订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。
 
* 一个能够访问 Internet 且满足 Azure 日志集成安装要求的系统。 该系统可以位于云服务中，也可以托管在本地。

* 已安装了 Azure 日志集成。 若要安装它，请执行以下操作：

   a. 使用远程桌面连接到步骤 2 中提到的系统。   
   b. 将 Azure 日志集成安装程序复制到该系统。 c. 启动安装程序，并接受 Microsoft 软件许可条款。

* 如果你将提供遥测信息，请使复选框保持选中状态。 如果不希望向 Microsoft 发送使用情况信息，请清除复选框。

   有关 Azure 日志集成以及如何安装它的详细信息，请参阅[包含 Azure 诊断日志记录和 Windows 事件转发功能的 Azure 日志集成](azure-log-integration-get-started.md)。

* 最新 PowerShell 版本。

   如果已安装了 Windows Server 2016，则已至少具有 PowerShell 5.0。 如果使用的是任何其他版本的 Windows Server，则可能安装了较早版本的 PowerShell。 可以通过在 PowerShell 窗口中输入 ```get-host``` 来检查版本。 如果未安装 PowerShell 5.0，可以[下载它](https://www.microsoft.com/download/details.aspx?id=50395)。

   至少安装了 PowerShell 5.0 后，可以按照[安装 Azure PowerShell](/powershell/azure/install-az-ps)中的说明继续安装最新版本。


## <a name="create-supporting-infrastructure-elements"></a>创建支持基础结构元素

1. 打开一个提升权限的 PowerShell 窗口并转至 **C:\Program Files\Microsoft Azure Log Integration**。
1. 通过运行脚本 LoadAzLogModule.ps1 导入 AzLog cmdlet。 输入 `.\LoadAzLogModule.ps1` 命令。 （请注意该命令中的“\"。）应看到与下面类似的内容：</br>

   ![已加载的模块列表](./media/azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. 输入 `Connect-AzAccount` 命令。 在登录窗口中，输入将用于本教程的订阅的凭据信息。

   >[!NOTE]
   >如果这是你首次从此计算机登录到 Azure，将会看到一条有关允许 Microsoft 收集 PowerShell 使用情况数据的消息。 建议启用此数据收集，因为该数据将用来改进 Azure PowerShell。

1. 身份验证成功后，你将登录。 请记下订阅 ID 和订阅名称，因为将需要使用它们完成后面的步骤。

1. 创建变量来存储后面将使用的值。 输入以下每个 PowerShell 行。 可能需要调整值来匹配你的环境。
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'```（你的订阅名称可能不同。 可以在前面命令的输出中看到该名称。）
    - ```$location = 'West US'```（此变量将用来传递应当在其中创建资源的位置。 可以将此变量更改为你选择的任何位置。）
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random```（名称可以是任何内容，但应当仅包含 小写字母和数字。）
    - ```$storageName = $name```（此变量将用于存储帐户名称。）
    - ```$rgname = $name```（此变量将用于资源组名称。）
    - ```$eventHubNameSpaceName = $name```（这是事件中心命名空间的名称。）
1. 指定要使用的订阅：
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. 创建资源组：
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   如果此时输入 `$rg`，则应当会看到类似于以下屏幕截图的输出：

   ![创建资源组后的输出](./media/azure-log-integration-keyvault-eventhub/create-rg.png)
1. 创建一个将用来跟踪状态信息的存储帐户：
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. 创建事件中心命名空间。 这是创建事件中心所必需的。
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. 获取将用于见解提供程序的规则 ID：
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. 获取所有可能的 Azure 位置并将名称添加到可以在后面的步骤中使用的变量：
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    如果此时输入`$locations` ，则会看到位置名称没有由 AzLocation 返回的附加信息。
1. 创建 Azure 资源管理器日志配置文件： 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    有关 Azure 日志配置文件的详细信息，请参阅 [Azure 活动日志概述](../../azure-monitor/platform/activity-logs-overview.md)。

> [!NOTE]
> 尝试创建日志配置文件时可能会收到错误消息。 然后，可以查看 AzLogProfile 和 AzLogProfile 的文档。 如果运行 AzLogProfile，则会看到日志配置文件的相关信息。 可以通过输入 ```Remove-AzLogProfile -name 'Log Profile Name'``` 命令来删除现有的日志配置文件。
>
>![Resource Manager 配置文件错误](./media/azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>创建 key vault

1. 创建 key vault：

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. 配置 key vault 的日志记录：

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>生成日志活动

需要向 Key Vault 发送请求来生成日志活动。 诸如密钥生成、存储机密或从 Key Vault 中读取机密之类的活动都将创建日志条目。

1. 显示当前的存储密钥：
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. 生成一个新的 **key2**：
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. 再次显示密钥，可以发现 **key2** 存储着一个不同的值：
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. 设置并读取机密来生成其他日志条目：
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![返回的机密](./media/azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>配置 Azure 日志集成

现在，你已配置了将 Key Vault 活动记录到事件中心内所需的所有元素，接下来需要配置 Azure 日志集成：

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

针对每个事件中心运行 AzLog 命令：

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

在运行最后两个命令大约一分钟后，应当会看到正在生成的 JSON 文件。 可以通过监视 **C:\users\AzLog\EventHubJson** 进行确认。

## <a name="next-steps"></a>后续步骤

- [Azure 日志集成常见问题解答](azure-log-integration-faq.md)
- [Azure 日志集成入门](azure-log-integration-get-started.md)
- [将来自 Azure 资源的日志集成到 SIEM 系统](azure-log-integration-overview.md)
