---
title: Azure Key Vault 日志记录 | Microsoft Docs
description: 借助本教程开始使用 Azure 密钥保管库日志记录。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 997651887c3c378e4791553d5ff05f585ad169ea
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000662"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 日志记录

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在创建一个或多个 Key Vault 之后，可能需要监视 Key Vault 的访问方式、时间和访问者。 为此，可以启用 Azure Key Vault 日志记录，以便在提供的 Azure 存储帐户中保存信息。 系统会自动为指定的存储帐户创建名为 **insights-logs-auditevent** 的新容器。 可以使用此同一个存储帐户来收集多个 Key Vault 的日志。

最多在执行 Key Vault 操作 10 分钟后，就能访问其日志记录信息。 但大多数情况下不用等待这么长时间。  存储帐户中的日志完全由你管理：

* 请使用标准的 Azure 访问控制方法限制可访问日志的人员，以此保护日志。
* 删除不想继续保留在存储帐户中的日志。

借助本教程开始使用 Azure 密钥保管库日志记录。 你将创建一个存储帐户，启用日志记录，并解释收集的日志信息。  

> [!NOTE]
> 本教程不包含有关如何创建密钥保管库、密钥或机密的说明。 有关信息，请参阅[什么是 Azure 密钥保管库？](key-vault-overview.md)。 或者，有关跨平台 Azure CLI 的说明，请参阅[此对应教程](key-vault-manage-with-cli2.md)。
>
> 本文提供有关更新诊断日志记录的 Azure PowerShell 说明。 也可以使用 Azure 门户的“诊断日志”部分的 Azure Monitor 来更新诊断日志记录。  
>

有关 Key Vault的概述信息，请参阅[什么是 Azure Key Vault？](key-vault-overview.md)。 有关 Key Vault 可用位置的信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="prerequisites"></a>先决条件

要完成本教程，必须准备好以下各项：

* 正在使用的现有密钥保管库。  
* Azure PowerShell，最低版本为 1.0.0。 要安装 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 如果已安装了 Azure PowerShell，但不知道版本，请在 Azure PowerShell 控制台中输入 `$PSVersionTable.PSVersion`。  
* 足够的 Azure 存储用于保存密钥保管库日志。

## <a id="connect"></a>连接到 Key Vault 订阅

设置密钥日志记录的第一步是将 Azure PowerShell 指向要记录的 Key Vault。

使用以下命令启动 Azure PowerShell 会话，并登录 Azure 帐户：  

```powershell
Connect-AzAccount
```

在弹出的浏览器窗口中，输入 Azure 帐户用户名和密码。 Azure PowerShell 将获取与此帐户关联的所有订阅。 PowerShell 默认使用第一个订阅。

可能需要指定用于创建 Key Vault 的订阅。 输入以下命令以查看帐户的订阅：

```powershell
Get-AzSubscription
```

然后，若要指定与要记录的 Key Vault 关联的订阅，请输入：

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

将 PowerShell 指向正确的订阅是一个重要步骤，尤其是在有多个订阅与帐户关联的情况下。 有关配置 Azure PowerShell 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

## <a id="storage"></a>为日志创建存储帐户

尽管可以使用现有的存储帐户来保存日志，但我们将专门创建一个存储帐户来保存 Key Vault 日志。 为方便起见，在稍后遇到必须指定此帐户的情况时，我们会将详细信息存储到名为 **sa**的变量中。

为了进一步简化管理，我们还使用了包含 Key Vault 的同一个资源组。 在 [入门教程](key-vault-get-started.md)中，此资源组的名称为 **ContosoResourceGroup**，我们将继续使用“东亚”位置。 在适当的情况下，请将这些值替换为自己的值：

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> 如果你决定使用现有存储帐户，该帐户必须使用与 Key Vault 相同的订阅。 该帐户还必须使用 Azure 资源管理器部署模型，而不是经典部署模型。
>
>

## <a id="identify"></a>标识用于保存日志的密钥保管库

在[入门教程](key-vault-get-started.md)中，Key Vault 名称为 **ContosoKeyVault**。 我们将继续使用该名称，并将详细信息存储在名为 **kv** 的变量中：

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>启用日志记录

为了启用 Key Vault 日志记录，我们将使用 **Set-AzDiagnosticSetting** cmdlet 并配合针对新存储帐户和 Key Vault 创建的变量。 还将 **-Enabled** 标志设置为 **$true**，并将类别设置为 **AuditEvent**（Key Vault 日志记录的唯一类别）：

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

输出如下所示：

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

此输出确认 Key Vault 日志记录现已启用，会将信息保存到存储帐户。

还可以选择性地为日志设置保留期策略，以便自动删除较旧的日志。 例如，通过将 **-RetentionEnabled** 标志设置为 **$true** 来设置保留期策略，并将 **-RetentionInDays** 参数设置为 **90**，以便自动删除 90 天以上的日志。

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

记录的内容：

* 所有已经过身份验证的 REST API 请求，包括由于访问权限、系统错误或错误请求而发生的失败请求。
* 对 Key Vault 本身执行的操作，包括创建、删除、设置 Key Vault 访问策略，以及更新 Key Vault 属性（例如标记）。
* 对 Key Vault 中的密钥和机密执行的操作，包括：
  * 创建、修改或删除这些密钥或机密。
  * 签名、验证、加密、解密、包装和解包密钥、获取机密、列出密钥和机密（及其版本）。
* 导致出现 401 响应的未经身份验证的请求。 例如，请求不包含持有者令牌、格式不正确或已过期，或者包含无效的令牌。  

## <a id="access"></a>访问日志

Key Vault 日志存储在提供的存储帐户的 **insights-logs-auditevent** 容器中。 若要查看这些日志，必须下载 Blob。

首先，请为容器名称创建一个变量。 本演练的剩余部分将使用此变量。

```powershell
$container = 'insights-logs-auditevent'
```

若要列出此容器中的所有 Blob，请输入：

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

输出与下面类似：

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

可从此输出中看出，blob 遵循以下命名约定：`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

日期和时间值使用 UTC。

由于可以使用相同的存储帐户来收集多个资源的日志，Blob 名称中的完整资源 ID 适合用于仅访问或下载所需 Blob。 但在这样做之前，让我们先了解如何下载所有 Blob。

创建一个文件夹用于下载 Blob。 例如：

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

然后获取所有 blob 的列表：  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

通过 **Get-AzStorageBlobContent** 以管道传送此列表，将 Blob 下载到目标文件夹：

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

运行第二个命令时，blob 名称中的 / 分隔符会在目标文件夹下创建完整的文件夹结构  。 你将使用此结构下载 Blob 并将其存储为文件。

若要选择性地下载 Blob，请使用通配符。 例如：

* 如果有多个密钥保管库，并只想要下载其中名为 CONTOSOKEYVAULT3 的密钥保管库的日志：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 如果有多个资源组，并只想要下载其中某个资源组的日志，请使用 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 如果要下载 2019 年 1 月份的所有日志，请使用 `-Blob '*/year=2019/m=01/*'`：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

现在已准备就绪，可以开始查看日志中的内容。 但在开始之前，应该了解另外两个命令：

* 若要查询密钥保管库资源的诊断设置状态：`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* 若要禁用密钥保管库资源的日志记录： `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>解释 Key Vault 日志

每个 Blob 存储为文本，并格式化为 JSON Blob。 让我们看一个示例日志项。 运行以下命令：

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

该命令将返回类似于下面的日志项：

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

下表列出了字段的名称和描述：

| 字段名 | 说明 |
| --- | --- |
| **time** |日期和时间 (UTC)。 |
| **resourceId** |Azure 资源管理器资源 ID。 对于密钥保管库日志而言，这始终是密钥保管库资源 ID。 |
| **operationName** |下一份表格中所述操作的名称。 |
| **operationVersion** |客户端请求的 REST API 版本。 |
| **category** |结果的类型。 对于 Key Vault 日志而言，**AuditEvent** 是唯一可用值。 |
| **resultType** |REST API 请求的结果。 |
| **resultSignature** |HTTP 状态。 |
| **resultDescription** |有关结果的其他描述（如果有）。 |
| **durationMs** |为 REST API 请求提供服务所花费的时间，以毫秒为单位。 此时间不包括网络延迟，因此在客户端上测得的时间可能与此时间不匹配。 |
| **callerIpAddress** |发出请求的客户端的 IP 地址。 |
| **correlationId** |一个可选 GUID，客户端可传递此 GUID 来使客户端日志与服务端 (Key Vault) 日志相关联。 |
| **identity** |在 REST API 请求中提供的令牌中的标识。 与通过 Azure PowerShell cmdlet 发出请求一样，这通常是“用户”、“服务主体”，或者“用户+应用 ID”的组合。 |
| **properties** |此字段根据操作 (**operationName**) 包含不同的信息。 在大多数情况下，此字段包含客户端信息（客户端传递的用户代理字符串）、具体 REST API 请求 URI 和 HTTP 状态代码。 此外，在根据请求（例如，**KeyCreate** 或 **VaultGet**）返回对象时，此字段还将包含密钥 URI（“id”形式）、保管库 URI 或机密 URI。 |

**operationName** 字段值采用 *ObjectVerb* 格式。 例如：

* 所有 Key Vault 操作采用 `Vault<action>` 格式，例如 `VaultGet` 和 `VaultCreate`。
* 所有密钥操作采用 `Key<action>` 格式，例如 `KeySign` 和 `KeyList`。
* 所有机密操作采用 `Secret<action>` 格式，例如 `SecretGet` 和 `SecretListVersions`。

下表列出了 **operationName** 值和对应的 REST API 命令：

| operationName | REST API 命令 |
| --- | --- |
| **身份验证** |通过 Azure Active Directory 终结点进行身份验证 |
| **VaultGet** |[获取有关密钥保管库的信息](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[创建或更新密钥保管库](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[删除密钥保管库](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[更新密钥保管库](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[列出资源组中的所有密钥保管库](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[创建密钥](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[获取有关密钥的信息](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[将密钥导入保管库](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[备份密钥](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[删除密钥](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[还原密钥](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[使用密钥签名](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[使用密钥验证](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[包装密钥](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[解包密钥](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[使用密钥加密](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[使用密钥解密](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[更新密钥](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[列出保管库中的密钥](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[列出密钥的版本](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[创建机密](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[获取机密](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[更新机密](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[删除机密](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[列出保管库中的机密](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[列出机密的版本](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>使用 Azure Monitor 日志

可以使用 Azure Monitor 日志中的 Key Vault 解决方案查看 Key Vault **AuditEvent** 日志。 在 Azure Monitor 日志中，可以使用日志查询来分析数据并获取所需的信息。 

有关详细信息，包括如何进行设置，请参阅 [Azure Monitor 日志中的Azure Key Vault 解决方案](../azure-monitor/insights/azure-key-vault.md)。 如果需要从 Azure Monitor 日志预览版提供的旧 Key Vault 解决方案进行迁移，且之前在该方案中，首先将日志路由到了 Azure 存储帐户，并将 Azure Monitor 日志配置为了从此处读取，则本文也可提供指导。

## <a id="next"></a>后续步骤

有关在 .NET Web 应用程序中使用 Azure Key Vault 的教程，请参阅[从 Web 应用程序使用 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)。

有关编程参考，请参阅 [Azure 密钥保管库开发人员指南](key-vault-developers-guide.md)。

有关 Azure Key Vault 的 Azure PowerShell 1.0 cmdlet 列表，请参阅 [Azure Key Vault cmdlet](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)。

有关使用 Azure Key Vault 进行密钥轮换和日志审核的教程，请参阅[为 Key Vault 设置端到端密钥转换和审核](key-vault-key-rotation-log-monitoring.md)。
