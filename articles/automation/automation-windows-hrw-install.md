---
title: Azure 自动化 Windows 混合 Runbook 辅助角色
description: 本文介绍如何安装 Azure 自动化混合 Runbook 辅助角色，该角色可用于在本地数据中心或云环境的基于 Windows 的计算机上运行 Runbook。
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a8f6d46b8db6761204e39f14bbb51a493445ad26
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477918"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>部署 Windows 混合 Runbook 辅助角色

利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一个或多个指定计算机。 本文介绍了如何在 Windows 计算机上安装混合 Runbook 辅助角色。

## <a name="installing-the-windows-hybrid-runbook-worker"></a>安装 Windows 混合 Runbook 辅助角色

若要安装和配置 Windows 混合 Runbook 辅助角色，可使用两种方法。 建议的方法是使用自动化 Runbook 来彻底实现配置 Windows 计算机过程的自动化。 第二种方法使用分步过程来手动安装和配置角色。

> [!NOTE]
> 为了使用所需状态配置 (DSC) 管理支持混合 Runbook 辅助角色的服务器配置，需将其添加为 DSC 节点。

Windows 混合 Runbook 辅助角色的最低要求如下：

* Windows Server 2012 或更高版本。
* Windows PowerShell 5.1 或更高版本（[下载 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)）。
* .NET Framework 4.6.2 或更高版本。
* 双核。
* 4 GB RAM。
* 端口 443（出站）。

若要查看混合 Runbook 辅助角色的更多网络要求，请参阅[配置网络](automation-hybrid-runbook-worker.md#network-planning)。

若要进一步了解如何载入服务器以供 DSC 管理，请参阅[载入由 Azure Automation DSC 管理的计算机](automation-dsc-onboarding.md)。
如果启用[更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)，任何连接到 Azure Log Analytics 工作区的 Windows 计算机将自动配置为混合 Runbook 辅助角色，以支持此解决方案中包括的 Runbook。 但是，该计算机未注册到任何已在自动化帐户中定义的混合辅助角色组。 

只要将同一个帐户同时用于解决方案和混合 Runbook 辅助角色组成员身份，即可将该计算机添加到自动化帐户的混合 Runbook 辅助角色组，以支持自动化 Runbook。 此功能已添加到 7.2.12024.0 版本的混合 Runbook 辅助角色。

成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。

### <a name="automated-deployment"></a>自动化部署

执行以下步骤，以便自动完成 Windows 混合辅助角色的安装和配置：

1. 直接从运行混合 Runbook 辅助角色的计算机或环境中的其他计算机的 [PowerShell 库](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)下载 New-OnPremiseHybridWorker.ps1 脚本， 并将该脚本复制到辅助角色。

   在执行期间，New-OnPremiseHybridWorker.ps1 脚本需要以下参数：

   * *AutomationAccountName*（必需）：自动化帐户的名称。
   * *AAResourceGroupName*（必需）：与自动化帐户关联的资源组的名称。
   * *OMSResourceGroupName*（可选）：Log Analytics 工作区的资源组的名称。 如果未指定此资源组，则使用 AAResourceGroupName  。
   * *HybridGroupName*（必需）：混合 Runbook 辅助角色组的名称，可将其指定为支持此方案的 runbook 的目标。
   * *SubscriptionID*（必需）：包含自动化帐户的 Azure 订阅 ID。
   * *WorkspaceName*（可选）：Log Analytics 工作区名称。 如果没有 Log Analytics 工作区，该脚本会创建并配置一个。

   > [!NOTE]
   > 在启用解决方案时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。
   >
   > 有关受支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。

2. 在计算机的“管理员”模式下，从“开始”屏幕打开 Windows PowerShell   。
3. 在 PowerShell 命令行外壳中，浏览到包含已下载脚本的文件夹。 更改这些参数的值：-AutomationAccountName、-AAResourceGroupName、-OMSResourceGroupName、-HybridGroupName、-SubscriptionId 和 -WorkspaceName       。 然后运行脚本。

     > [!NOTE]
     > 运行脚本后，系统会提示在 Azure 上进行身份验证。 必须以订阅管理员角色成员和订阅共同管理员的帐户登录  。

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. 系统会提示用户同意安装 NuGet 并使用 Azure 凭据进行身份验证。

5. 脚本完成后，“混合辅助角色组”页面会显示新组和成员数  。 如果这是现有的组，则成员数会递增。 可以从“混合辅助角色组”  页上的列表中选择组，并选择“混合辅助角色”  磁贴。 在“混合辅助角色”  页上，会列出组的每个成员。

### <a name="manual-deployment"></a>手动部署

针对自动化环境执行前两个步骤一次，并对每台辅助角色计算机重复其余步骤。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1.创建 Log Analytics 工作区

如果尚无 Log Analytics 工作区，请按照[管理工作区](../azure-monitor/platform/manage-access.md)中的说明创建工作区。 如果已经有一个工作区，则可以使用现有的。

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2.向 Log Analytics 工作区添加自动化解决方案

自动化 Azure Monitor 日志解决方案添加 Azure 自动化，包括对混合 Runbook 辅助角色支持的功能。 将解决方案添加到工作区时，它会自动将辅助角色组件推送到在下一步要安装的代理计算机。

若要添加**自动化**Azure Monitor 记录到工作区中，运行以下 PowerShell 解决方案。

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3.安装 Microsoft Monitoring Agent

Microsoft Monitoring Agent 将计算机连接到 Azure Monitor 日志。 在计算机本地安装代理并将其连接到工作区时，代理会自动下载混合 Runbook 辅助角色所需的组件。

若要在本地计算机上安装代理，请按照的说明[连接 Windows 计算机连接到 Azure Monitor 日志](../log-analytics/log-analytics-windows-agent.md)。 可以对多台计算机重复此过程，以将多个辅助角色添加到环境。

代理已成功连接到 Azure Monitor 日志，就会列在**连接的源**选项卡的 log analytics**设置**页。 当 C:\Program Files\Microsoft Monitoring Agent\Agent 中出现名为 **AzureAutomationFiles** 的文件夹时，可确认代理已正确下载自动化解决方案。 若要确认混合 Runbook 辅助角色的版本，可浏览到 C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ 并留意 \\version 子文件夹  。

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.安装 Runbook 环境并连接到 Azure 自动化

当代理添加到 Azure Monitor 日志时，自动化解决方案会向下推送**HybridRegistration** PowerShell 模块，其中包含**Add-hybridrunbookworker** cmdlet。 使用此 cmdlet 将 Runbook 环境安装到计算机上，并将其注册到 Azure 自动化。

在管理员模式下打开 PowerShell 会话，并运行以下命令以导入模块：

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

然后，请使用以下语法运行 Add-HybridRunbookWorker cmdlet  ：

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

可以从 Azure 门户的“管理密钥”  页获取此 cmdlet 所需的信息。 通过在自动化帐户的“设置”  页中选择“密钥”  选项，打开此页。

![“管理密钥”页](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* GroupName 是混合 Runbook 辅助角色组的名称  。 如果该组已经存在于自动化帐户中，则会将当前计算机添加到其中。 如果该组不存在，则将添加该组。
* “终结点”是“管理密钥”页上的“URL”条目    。
* “令牌”是指“管理密钥”页上的“主访问密钥”条目    。

若要接收有关安装的详细信息，请使用包含 Add-HybridRunbookWorker 的 -Verbose 开关   。

#### <a name="5-install-powershell-modules"></a>5.安装 PowerShell 模块

Runbook 可以使用在 Azure 自动化环境中安装的模块中定义的任何活动和 cmdlet。 这些模块不会自动部署到本地计算机，因此必须手动安装。 例外情况是 Azure 模块，该模块是默认安装的，并可用于访问所有 Azure 服务的 cmdlet 以及 Azure 自动化的活动。

由于混合 Runbook 辅助角色功能的主要用途是管理本地资源，很可能需要安装支持这些资源的模块。 有关安装 Windows PowerShell 模块的信息，请参阅[安装模块](/powershell/developer/windows-powershell)。 

安装的模块必须位于 PSModulePath 环境变量所引用的位置，以便混合辅助角色自动将其导入  。 有关详细信息，请参阅 [Modifying the PSModulePath Installation Path](/powershell/developer/windows-powershell)（修改 PSModulePath 安装路径）。

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。
* 有关如何删除混合 Runbook 辅助角色的说明，请参阅[删除 Azure 自动化混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)。
* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅 [Windows 混合 Runbook 辅助角色的故障排除](troubleshoot/hybrid-runbook-worker.md#windows)
* 有关如何对更新管理问题进行故障排除的其他步骤，请参阅[更新管理：故障排除](troubleshoot/update-management.md)。
