---
title: Azure 自动化 Linux 混合 Runbook 辅助角色
description: 本文介绍如何安装 Azure 自动化混合 Runbook 辅助角色，以便在本地数据中心或云环境的基于 Linux 的计算机上运行 Runbook。
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/28/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 360fa750da054f9b126a8694f3dd2ce4b0b417b7
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240299"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>部署 Linux 混合 Runbook 辅助角色

利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Linux 混合 Runbook 辅助角色以特殊用户身份执行 Runbook，该用户身份可进行权限提升，以运行需要提升权限的命令。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一个或多个指定计算机。

本文介绍如何在 Linux 计算机上安装混合 Runbook 辅助角色。

## <a name="supported-linux-operating-systems"></a>受支持的 Linux 操作系统

混合 Runbook 辅助角色功能支持以下分发版：

* Amazon Linux 2012.09 到 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS 和 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 和 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>安装 Linux 混合 Runbook 辅助角色

若要在 Linux 计算机上安装和配置混合 Runbook 辅助角色，请按照一个简单明了的过程手动安装和配置此角色。 它需要启用 Azure Log Analytics 工作区中的“自动化混合辅助角色”解决方案，然后运行一组命令将计算机注册为辅助角色，并且将它添加到组中。

Linux 混合 Runbook 辅助角色的最低要求如下：

* 双核
* 4 GB RAM
* 端口 443（出站）

### <a name="package-requirements"></a>程序包要求

| **必需的程序包** | **说明** | **最低版本**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 库| 2.5-12 |
|Openssl| OpenSSL 库 | 1.0（支持 TLS 1.1 和 TLS 1.2）|
|Curl | cURL Web 客户端 | 7.15.5|
|Python-ctype | 需要 Python 2。x |
|PAM | 可插入验证模块|
| **可选包** | **说明** | **最低版本**|
| PowerShell Core | 若要运行 PowerShell Runbook，需要安装 PowerShell，请参阅[在 Linux 上安装 PowerShell Core](/powershell/scripting/setup/installing-powershell-core-on-linux) 了解如何安装。  | 6.0.0 |

### <a name="installation"></a>安装

在继续操作之前，请记下自动化帐户链接到的 Log Analytics 工作区。 另请记下自动化帐户的主密钥。 在 Azure 门户中选择自己的自动化帐户，选择工作区 ID 对应的“工作区”，然后选择主密钥对应的“密钥”，即可找到这两个值。 有关混合 Runbook 辅助角色所需的端口和地址的信息，请参阅[配置网络](automation-hybrid-runbook-worker.md#network-planning)。

1. 使用以下方法之一，在 Azure 中启用“自动化混合辅助角色”解决方案：

   * 使用[将 Azure Monitor 日志解决方案添加到工作区](../log-analytics/log-analytics-add-solutions.md)中所述的过程，将“自动化混合辅助角色”解决方案添加到订阅。
   * 运行以下 cmdlet：

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. 运行以下命令，安装 Log Analytics Linux 代理。 请将 \<WorkspaceID\> 和 \<WorkspaceKey\> 替换为工作区中的相应值。

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. 运行以下命令，更改 *-w*、 *-k*、 *-g* 和 *-e* 参数的值。 对于 *-g* 参数，请将值替换为新的 Linux 混合 Runbook 辅助角色应加入的混合 Runbook 辅助角色组的名称。 如果自动化帐户中尚不存在该名称，系统会使用该名称生成一个新的混合 Runbook 辅助角色组。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. 命令完成后，Azure 门户中的“混合辅助角色组”页面会显示新组和成员数。 如果这是现有的组，则成员数会递增。 可以从“混合辅助角色组”页上的列表中选择组，并选择“混合辅助角色”磁贴。 在“混合辅助角色”页上，会列出组的每个成员。

> [!NOTE]
> 如果要对 Azure VM 使用用于 Linux 的 Azure Monitor 虚拟机扩展，我们建议将 `autoUpgradeMinorVersion` 设置为 false，因为自动升级版本可能会导致混合 Runbook 辅助角色出现问题。 若要了解如何手动升级扩展，请参阅 [Azure CLI 部署](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)。

## <a name="turning-off-signature-validation"></a>关闭签名验证

默认情况下，Linux 混合 Runbook 辅助角色需要签名验证。 如果针对辅助角色运行未签名的 runbook，将看到显示“签名验证失败”字样的错误。 若要禁用签名验证，请运行以下命令。 将第二个参数替换为 Log Analytics 工作区 ID。

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>支持的 runbook 类型

Linux 混合 Runbook 辅助角色并非支持 Azure 自动化中的全套 Runbook 类型。

以下 runbook 类型可以在 Linux 混合辅助角色上工作：

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell Runbook 要求在 Linux 计算机上安装 PowerShell Core。 请参阅[在 Linux 上安装 PowerShell Core](/powershell/scripting/setup/installing-powershell-core-on-linux) 了解如何安装。

以下 Runbook 类型不能在 Linux 混合辅助角色上运行：

* PowerShell 工作流
* 图形
* 图形 PowerShell 工作流

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。
* 有关如何删除混合 Runbook 辅助角色的说明，请参阅[删除 Azure 自动化混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)。
* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅 [Linux 混合 Runbook 辅助角色的故障排除](troubleshoot/hybrid-runbook-worker.md#linux)
