---
title: 适用于 Windows 的 Azure VM 扩展和功能 | Microsoft Docs
description: 了解可为 Azure 虚拟机提供哪些扩展，这些虚拟机扩展按它们提供或改进的功能进行分组。
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a19b6bd8da82498aae45657d30883db14efd9343
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174071"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>适用于 Windows 的虚拟机扩展和功能

Azure 虚拟机 (VM) 扩展是小型应用程序，可在 Azure VM 上提供部署后配置和自动化任务。 例如，如果某个虚拟机需要安装软件、防病毒保护或运行脚本，便可以使用 VM 扩展。 可以使用 Azure CLI、PowerShell、Azure 资源管理器模板和 Azure 门户运行 Azure VM 扩展。 扩展可与新 VM 部署捆绑在一起，也可以针对任何现有系统运行。

本文提供 VM 扩展的概述、使用 Azure VM 扩展的先决条件，以及有关如何检测、管理和删除 VM 扩展的指导。 本文提供的是概况信息，因为有许多 VM 扩展可用，每个扩展可能具有独特的配置。 可以在各个扩展特定的各个文档中找到扩展特定的详细信息。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="use-cases-and-samples"></a>用例和示例

有许多不同的 Azure VM 扩展可用，每个都有特定用例。 示例包括：

- 使用适用于 Windows 的 DSC 扩展将 PowerShell 所需状态配置应用到 VM。 有关详细信息，请参阅 [Azure 所需状态配置扩展](dsc-overview.md)。
- 使用 Microsoft Monitoring Agent VM 扩展配置 VM 监视功能。 有关详细信息，请参阅[将 Azure VM 连接到 Azure Monitor 日志](../../log-analytics/log-analytics-azure-vm-extension.md)。
- 使用 Chef 配置 Azure VM。 有关详细信息，请参阅[使用 Chef 自动执行 Azure VM 部署](../windows/chef-automation.md)。
- 使用 Datadog 扩展配置 Azure 基础结构监视功能。 有关详细信息，请参阅 [Datadog 博客](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)。


除了进程特定的扩展外，“自定义脚本”扩展也可用于 Windows 和 Linux 虚拟机。 适用于 Windows 的自定义脚本扩展允许在 VM 上运行任何 PowerShell 脚本。 在设计需要本机 Azure 工具无法提供的配置的 Azure 部署时，自定义脚本很有用。 有关详细信息，请参阅 [Windows VM 自定义脚本扩展](custom-script-windows.md)。

## <a name="prerequisites"></a>先决条件

若要处理 VM 上的扩展，需要安装 Azure Windows 代理。 有些单独的扩展附带先决条件，例如，有权访问资源或依赖项。

### <a name="azure-vm-agent"></a>Azure VM 代理

Azure VM 代理可管理 Azure VM 与 Azure 结构控制器之间的交互。 VM 代理负责部署和管理 Azure VM 的许多功能层面，包括运行 VM 扩展。 Azure VM 代理预先安装在 Azure 市场映像上，并可手动安装在受支持的操作系统上。 适用于 Windows 的 Azure VM 代理称为 Windows 来宾代理。

有关受支持的操作系统以及安装说明的信息，请参阅 [Azure virtual machine agent](agent-windows.md)（Azure 虚拟机代理）。

#### <a name="supported-agent-versions"></a>支持的代理版本

为了尽量提供最佳体验，我们提供了精简版本的代理。 有关详细信息，请参阅[此文章](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)。

#### <a name="supported-oses"></a>支持的 OS

Windows 来宾代理在多个 OS 上运行，但是，扩展框架对扩展的 OS 施加限制。 有关详细信息，请参阅[此文章](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
)。

某些扩展并非在所有 OS 上均受支持，可能会发出错误代码 51“不受支持的 OS”。 请查看相应的扩展文档来了解支持情况。

#### <a name="network-access"></a>网络访问

从 Azure 存储扩展存储库下载扩展包，将扩展状态上传内容发布到 Azure 存储。 如果使用[受支持](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)版本的代理，则不需要允许对 VM 区域中 Azure 存储的访问，因为可以使用代理将通信重定向到 Azure 结构控制器，以进行代理通信。 如果使用不受支持的代理版本，则需要允许从 VM 对该区域中 Azure 存储的出站访问。

> [!IMPORTANT]
> 如果已使用来宾防火墙阻止对 168.63.129.16 的访问，则不管采用上述哪种方法，扩展都会失败。

代理只可用于下载扩展包和报告状态。 例如，如果扩展安装需要从 GitHub 下载脚本（自定义脚本），或需要访问 Azure 存储（Azure 备份），则需要打开其他防火墙/网络安全组端口。 不同的扩展具有不同的要求，因为它们本身就是应用程序。 对于需要访问 Azure 存储的扩展，可以使用[存储](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)的 Azure NSG 服务标记来允许访问。

Windows 来宾代理不提供用于重定向代理流量请求的代理服务器支持。

## <a name="discover-vm-extensions"></a>发现 VM 扩展

有许多不同的 VM 扩展可与 Azure VM 配合使用。 若要查看完整列表，请使用 [Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage)。 以下示例列出 *WestUS* 位置的所有可用扩展：

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>运行 VM 扩展

Azure VM 扩展在现有 VM 上运行，需要在已部署的 VM 上进行配置更改或恢复连接时，这很有用。 VM 扩展还可以与 Azure 资源管理器模板部署捆绑。 可将扩展与资源管理器模板配合使用来部署并配置 Azure VM，在部署后无需干预。

可使用以下方法针对现有 VM 运行扩展。

### <a name="powershell"></a>PowerShell

存在多个用于运行单个扩展的 PowerShell 命令。 若要查看列表，请使用 [Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) 并根据“扩展”筛选：

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

这会提供类似以下内容的输出：

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

以下示例使用自定义脚本扩展从 GitHub 存储库将脚本下载到目标虚拟机上，然后运行该脚本。 有关 VM 访问扩展的详细信息，请参阅[自定义脚本扩展概述](custom-script-windows.md)。

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

在以下示例中，VM 访问扩展用于将 Windows VM 的管理密码重置为临时密码。 有关 VM 访问扩展的详细信息，请参阅[重置 Windows VM 中的远程桌面服务](../windows/reset-rdp.md)。 运行此扩展后，应重置首次登录密码：

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

可以使用 `Set-AzVMExtension` 命令来启动任何 VM 扩展。 有关详细信息，请参阅 [Set-AzVMExtension 参考](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension)。


### <a name="azure-portal"></a>Azure 门户

可通过 Azure 门户将 VM 扩展应用到现有 VM。 在门户中，依次选择该 VM、“扩展”、“添加”。 从可用扩展的列表中选择所需扩展，并按向导中的说明操作。

以下示例演示如何从 Azure 门户安装 Microsoft 反恶意软件扩展。

![安装反恶意软件扩展](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

VM 扩展可添加到 Azure 资源管理器模板，并在部署模板的过程中执行。 使用模板部署扩展时，可以创建完全配置的 Azure 部署。 例如，以下 JSON 取自一个资源管理器模板，该模板会在每个 VM 上部署一组负载均衡的 VM、一个 Azure SQL 数据库，然后安装一个 .NET Core 应用程序。 VM 扩展负责安装软件。

有关详细信息，请参阅完整的 [Resource Manager 模板](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)。

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

有关创建资源管理器模板的详细信息，请参阅[使用 Windows VM 扩展创作 Azure 资源管理器模板](../windows/template-description.md#extensions)。

## <a name="secure-vm-extension-data"></a>保护 VM 扩展数据

运行 VM 扩展时，可能需要包括敏感信息，例如凭据、存储帐户名称和存储帐户访问密钥。 许多 VM 扩展包括受保护的配置，该配置对数据进行加密并且仅在目标 VM 内才对数据进行解密。 每个扩展都有一个特定的受保护的配置架构，扩展特定的文档中详述了各个架构。

以下示例显示了适用于 Windows 的自定义脚本扩展的一个实例。 要执行的命令包含一组凭据。 在此示例中，不会加密要执行的命令：

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

将“要执行的命令”属性移到“受保护的”配置可以保护执行字符串，如以下示例中所示：

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>如何更新代理和扩展？

代理和扩展使用相同的更新机制。 某些更新不需要附加的防火墙规则。

如果有更新可用，仅当发生了扩展更改或其他 VM 模型更改时，才会在 VM 上安装该项更新：

- 数据磁盘
- Extensions
- 启动诊断容器
- 来宾 OS 机密
- VM 大小
- 网络配置文件

发布者在不同的时间向不同的区域推出更新，因此，不同区域中的 VM 可能使用不同的版本。

#### <a name="listing-extensions-deployed-to-a-vm"></a>列出部署到 VM 的扩展

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>代理更新

Windows 来宾代理仅包含扩展处理代码，Windows 预配代码需要单独获取。 可以卸载 Windows 来宾代理。 无法禁用 Windows 来宾代理自动更新。

扩展处理代码负责与 Azure 结构通信，并处理各种 VM 扩展操作，例如安装、报告状态、更新单个扩展，以及删除扩展。 更新包含扩展处理代码的安全修复程序、bug 修复程序和增强功能。

若要检查运行的是哪个版本，请参阅[检测安装的 Windows 来宾代理](agent-windows.md#detect-the-vm-agent)。

#### <a name="extension-updates"></a>扩展更新

有扩展更新可用时，Windows 来宾代理会下载并升级扩展。 自动扩展更新以次要版本或修补程序的形式提供。 预配扩展时，可以选择安装或不安装扩展的次要版本更新。 以下示例演示如何在资源管理器模板中使用 autoUpgradeMinorVersion": true,' 自动升级次要版本：

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

若要获取最新的次要版本 bug 修复，我们强烈建议始终在扩展部署中选择自动更新。 无法选择不安装包含安全或关键 bug 修复的修补程序更新。

### <a name="how-to-identify-extension-updates"></a>如何识别扩展更新

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>在 VM 上使用 autoUpgradeMinorVersion 识别是否设置了扩展

如果使用“autoUpgradeMinorVersion”预配了扩展，则可以从 VM 模型查看信息。 若要检查，请使用 [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) 并提供资源组和 VM 名称，如下所示：

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

以下示例输出显示 autoUpgradeMinorVersion 设置为 true：

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>识别何时执行了 autoUpgradeMinorVersion

若要查看何时对扩展执行了更新，请查看 VM 上的代理日志，路径为 *C:\WindowsAzure\Logs\WaAppAgent.log*

在以下示例中，VM 上安装了 *Microsoft.Compute.CustomScriptExtension 1.8*。 为版本 *1.9* 提供了修补程序：

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>代理权限

若要执行任务，代理需要作为本地系统运行。

## <a name="troubleshoot-vm-extensions"></a>排查 VM 扩展的问题

每个 VM 扩展都有特定于扩展的故障排除步骤。 例如，使用自定义脚本扩展时，可在运行该扩展的 VM 本地找到脚本执行详细信息。 任何特定于扩展的故障排除步骤均在特定于扩展的文档中详细说明。

以下故障排除步骤适用于所有 VM 扩展。

1. 若要查看 Windows 来宾代理日志，请在预配扩展时查看 *C:\WindowsAzure\Logs\WaAppAgent.txt* 中的活动

2. 查看 *C:\WindowsAzure\Logs\Plugins\<extensionName>* 中的实际扩展日志，以了解更多详细信息

3. 查看特定扩展文档中有关错误代码和已知问题等的故障排除部分。

4. 查看系统日志。 检查其他可能影响了扩展的操作，例如，长时间安装另一个需要包管理器独占访问权限的应用程序。

### <a name="common-reasons-for-extension-failures"></a>扩展失败的常见原因

1. 运行扩展的时间不能超过 20 分钟（CustomScript 扩展、Chef 和 DSC 除外，其运行时间不能超过 90 分钟）。 如果部署超过此时间，则会将它标记为超时。 超时的原因可能包括 VM 资源不足、在扩展尝试预配时其他 VM 配置/启动任务消耗了大量资源。

2. 不符合最低先决条件。 某些扩展依赖于 VM SKU，例如 HPC 映像。 扩展可能需要满足特定的网络访问要求，例如，能够与 Azure 存储或公共服务通信。 其他原因包括访问包存储库、磁盘空间耗尽或安全限制。

3. 包管理器独占访问权限。 在某些情况下，可能会遇到长时间运行的 VM 配置与扩展安装相冲突的问题，两者都需要包管理器的独占访问权限。

### <a name="view-extension-status"></a>查看扩展状态

针对 VM 运行 VM 扩展后，请使用 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) 返回扩展状态。 *Substatuses[0]* 显示扩展预配成功，这意味着，该扩展已成功部署到 VM，但 VM 中的扩展执行失败 (*Substatuses[1]* )。

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

输出类似于以下示例输出：

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

此外，还可以在 Azure 门户中找到扩展执行状态。 若要查看扩展的状态，请依次选择 VM、“扩展”、所需的扩展。

### <a name="rerun-vm-extensions"></a>重新运行 VM 扩展

在某些情况下，可能需要重新运行 VM 扩展。 要重新运行扩展，可以先删除扩展，然后使用所选执行方法重新运行扩展。 若要删除扩展，请使用 [Remove-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension)，如下所示：

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

也可以在 Azure 门户中删除扩展，如下所示：

1. 选择 VM。
2. 选择“扩展”。
3. 选择所需的扩展。
4. 选择“卸载”。

## <a name="common-vm-extensions-reference"></a>常见 VM 扩展参考
| 扩展名称 | 描述 | 详细信息 |
| --- | --- | --- |
| 适用于 Windows 的自定义脚本扩展 |针对 Azure 虚拟机运行脚本 |[适用于 Windows 的自定义脚本扩展](custom-script-windows.md) |
| 适用于 Windows 的 DSC 扩展 |PowerShell DSC（所需状态配置）扩展 |[适用于 Windows 的 DSC 扩展](dsc-overview.md) |
| Azure 诊断扩展 |管理 Azure 诊断 |[Azure 诊断扩展](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Azure VM 访问扩展 |管理用户和凭据 |[适用于 Linux 的 VM 访问扩展](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>后续步骤

有关 VM 扩展的详细信息，请参阅 [Azure 虚拟机扩展和功能概述](overview.md)。
