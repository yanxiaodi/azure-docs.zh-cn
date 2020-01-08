---
title: 从 Azure 自动化 runbook 发送电子邮件
description: 了解如何使用 SendGrid 从 runbook 中发送电子邮件。
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce05aadb53cc3ad24ed65ea139594010e1aef047
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235088"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>教程：从 Azure 自动化 runbook 发送电子邮件

可以使用 PowerShell 通过 [SendGrid](https://sendgrid.com/solutions) 从 runbook 发送电子邮件。 本教程介绍如何创建一个可重用的 runbook，以便使用在 [Azure KeyVault](/azure/key-vault/) 中存储的 API 密钥发送电子邮件。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
>
> * 创建 Azure KeyVault
> * 将 SendGrid API 密钥存储在 KeyVault 中
> * 创建一个 runbook，用于检索 API 密钥和发送电子邮件

## <a name="prerequisites"></a>先决条件

完成本教程需要以下各项：

* Azure 订阅：如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [创建 SendGrid 帐户](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account)。
* [自动化帐户](automation-offering-get-started.md)和 **Az** 模块以及[运行方式连接](automation-create-runas-account.md)，用于存储和执行 runbook。

## <a name="create-an-azure-keyvault"></a>创建 Azure KeyVault

可以使用以下 PowerShell 脚本创建 Azure KeyVault。 将变量值替换为特定于环境的值。 单击代码块右上角的<kbd>试运行</kbd>按钮，使用嵌入的 Azure Cloud Shell。 也可复制代码并在本地运行它，前提是已在本地计算机上安装 [Azure PowerShell 模块](/powershell/azure/install-az-ps)。

> [!NOTE]
> 若要检索 API 密钥，请使用在[查找 SendGrid API 密钥](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key)中发现的步骤。

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

若要通过其他方式创建 Azure KeyVault 并存储机密，请参阅 [KeyVault 快速入门](/azure/key-vault/)。

## <a name="import-required-modules-to-your-automation-account"></a>将所需模块导入自动化帐户

若要在 runbook 中使用 Azure KeyVault，自动化帐户需要以下模块：

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)。
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)。

在“Azure 自动化”选项卡的“安装选项”下，单击<kbd>部署到 Azure 自动化</kbd>。 此操作会打开 Azure 门户。 在“导入”页上，选择自动化帐户并单击“确定”<kbd></kbd>。

如需通过其他方法来添加所需模块，请参阅[导入模块](/azure/automation/shared-resources/modules#import-modules)。

## <a name="create-the-runbook-to-send-an-email"></a>创建用于发送电子邮件的 runbook

在创建 KeyVault 并存储 SendGrid API 密钥以后，即可创建 runbook 来检索 API 密钥和发送电子邮件。

该 Runbook 使用 AzureRunAsConnection [运行方式帐户](automation-create-runas-account.md)通过 Azure 进行身份验证，以便从 Azure KeyVault 检索机密。

使用此示例创建名为 **Send-GridMailMessage** 的 Runbook。 可以修改此 PowerShell 脚本，并将其重用于不同的方案。

1. 转到 Azure 自动化帐户。
2. 在“过程自动化”下，选择“Runbook”。
3. 在 Runbook 列表的顶部选择“+ 创建 Runbook”。
4. 在“添加 Runbook”页上，输入 **Send-GridMailMessage** 作为 Runbook 名称。 对于 runbook 类型，选择“PowerShell”。 然后选择“创建”。
   ![创建 Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. 此时会创建 Runbook 并打开“编辑 PowerShell Runbook”页面。
   ![编辑 Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. 将以下 PowerShell 示例复制到“编辑”页中。 确保 `$VaultName` 是在创建 KeyVault 时指定的名称。

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. 选择“发布”以保存并发布 Runbook。

若要验证 runbook 是否成功执行，可以按[测试 runbook](manage-runbooks.md#test-a-runbook) 或[启动 runbook](start-runbooks.md) 下的步骤操作。
如果一开始看不到测试电子邮件，请检查 **Junk** 和 **Spam** 文件夹。

## <a name="clean-up"></a>清理

不再需要 Runbook 时，即可将其删除。 为此，请在 Runbook 列表中选择 Runbook，然后单击“删除”。

使用 [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps) cmdlet 删除密钥保管库。

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>后续步骤

* 有关如何创建或启动 Runbook 的问题，请参阅[排查 Runbook 错误](./troubleshoot/runbooks.md)。
* 若要更新自动化帐户中的模块，请参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)。
* 若要监视 runbook 执行，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。
* 若要使用警报触发 Runbook，请参阅[使用警报触发 Azure 自动化 Runbook](automation-create-alert-triggered-runbook.md)。
