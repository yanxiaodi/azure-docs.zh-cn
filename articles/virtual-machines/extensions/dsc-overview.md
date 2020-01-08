---
title: 适用于 Azure 的 Desired State Configuration 概述
description: 了解如何使用 PowerShell Desired State Configuration (DSC) 的 Microsoft Azure 扩展处理程序。 本文包括先决条件、体系结构和 cmdlet。
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: c759567e4d8c183452eccbbdca8459c8993d1361
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092424"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Azure Desired State Configuration 扩展处理程序简介

Azure VM 代理和关联的扩展是 Microsoft Azure 基础结构服务的一部分。 VM 扩展是软件组件，可以扩展 VM 功能并简化各种 VM 管理操作。

Azure Desired State Configuration (DSC) 扩展的主要用例是让 VM 启动到 [Azure Automation State Configuration (DSC) 服务](../../automation/automation-dsc-overview.md)。
该服务带来的[好处](/powershell/dsc/metaconfig#pull-service)包括：持续管理 VM 的配置，并与其他操作工具（例如 Azure 监视）集成。
使用扩展将 VM 注册到该服务可以提供一个甚至可跨 Azure 订阅工作的灵活解决方案。

可以独立于 Automation DSC 服务使用 DSC 扩展。
但是，这只会将配置推送到 VM。
系统不会提供持续的报告，只能在 VM 本地执行此类操作。

本文提供有关两种方案的信息：使用 DSC 扩展进行自动化加入，以及使用 DSC 扩展作为工具，通过 Azure SDK 将配置分配给 VM。

## <a name="prerequisites"></a>先决条件

- **本地计算机**：若要与 Azure VM 扩展交互，必须使用 Azure 门户或 Azure PowerShell SDK。
- **来宾代理**：使用 DSC 配置进行配置的 Azure VM 必须采用支持 Windows Management Framework (WMF) 4.0 或更高版本的 OS。 有关支持的 OS 版本的完整列表，请参阅 [DSC 扩展版本历史记录](/powershell/dsc/azuredscexthistory)。

## <a name="terms-and-concepts"></a>术语和概念

本指南假设读者熟悉以下概念：

- **配置**：DSC 配置文档。
- **节点**：DSC 配置的目标。 在本文档中，“节点”一律指 Azure VM。
- **配置数据**：包含配置的环境数据的 psd1 文件。

## <a name="architecture"></a>体系结构

Azure DSC 扩展使用 Azure VM 代理框架来传送、启用和报告 Azure VM 上运行的 DSC 配置。 DSC 扩展接受配置文档和一组参数。 如果未提供任何文件，[默认配置脚本](#default-configuration-script)会嵌入到扩展中。 默认配置脚本仅用于在[本地配置管理器](/powershell/dsc/metaconfig)中设置元数据。

首次调用扩展时，该扩展使用以下逻辑安装某个版本的 WMF：

- 如果 Azure VM OS 是 Windows Server 2016，则不执行任何操作。 Windows Server 2016 上已安装最新版本的 PowerShell。
- 如果已指定 **wmfVersion** 属性，除非该 WMF 版本与 VM 的 OS 不兼容，否则将安装该版本。
- 如果未指定 **wmfVersion** 属性，则安装 WMF 的最新适用版本。

安装 WMF 需要重启。 重启后，扩展将下载 **modulesUrl** 属性中指定的 .zip 文件（若已提供）。 如果此位置在 Azure Blob 存储中，则可以在 **sasToken** 属性中指定 SAS 令牌来访问该文件。 下载并解压缩 .zip 之后，将运行 **configurationFunction** 中定义的配置函数以生成 .mof 文件。 扩展然后通过使用生成的 .mof 文件来运行 `Start-DscConfiguration -Force`。 扩展将捕获输出并将其写入 Azure 状态通道。

### <a name="default-configuration-script"></a>默认配置脚本

Azure DSC 扩展包括一个默认配置脚本，该脚本计划在对 Azure Automation DSC 服务载入 VM 时使用。 脚本参数符合[本地配置管理器](/powershell/dsc/metaconfig)的可配置属性。 有关脚本参数，请参阅 [Desired State Configuration 扩展与 Azure 资源管理器模板](dsc-template.md)中的[默认配置脚本](dsc-template.md#default-configuration-script)。 有关完整脚本，请参阅 [GitHub 中的 Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true)。

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>有关注册到 Azure Automation State Configuration (DSC) 服务的信息

使用 DSC 扩展将节点注册到 State Configuration 服务时，需要提供三个值。

- RegistrationUrl - Azure 自动化帐户的 https 地址
- RegistrationKey - 用于将节点注册到服务的共享机密
- NodeConfigurationName - 从服务中提取的，用于配置服务器角色的节点配置 (MOF) 的名称

可以在 [Azure 门户](../../automation/automation-dsc-onboarding.md#azure-portal)中或者使用 PowerShell 查看此信息。

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

对于节点配置名称，请确保在 Azure State Configuration 中存在节点配置。  如果不存在，扩展部署将返回失败。  另外，请确保使用“节点配置”的名称而不是“配置”的名称。
配置在用于[编译节点配置（MOF 文件）](https://docs.microsoft.com/azure/automation/automation-dsc-compile)的脚本中定义。
该名称始终为 Configuration 后接句点 `.` 以及 `localhost` 或特定计算机名。

## <a name="dsc-extension-in-resource-manager-templates"></a>资源管理器模板中的 DSC 扩展

在大多数情况下，应该通过 Azure 资源管理器部署模板来使用 DSC 扩展。 有关详细信息以及如何在资源管理器部署模板中包含 DSC 扩展的示例，请参阅 [Desired State Configuration 扩展与 Azure 资源管理器模板](dsc-template.md)。

## <a name="dsc-extension-powershell-cmdlets"></a>DSC 扩展 PowerShell cmdlet

用于管理 DSC 扩展的 PowerShell cmdlet 最适合用于交互式故障排除和信息收集方案。 可以使用 cmdlet 来打包、发布和监视 DSC 扩展部署。 DSC 扩展的 cmdlet 尚未更新，无法使用[默认配置脚本](#default-configuration-script)。

**Publish-AzVMDscConfiguration** cmdlet 检索配置文件，扫描其中是否有依赖的 DSC 资源，然后创建一个 .zip 文件。 该 .zip 文件包含启用配置所需的配置和 DSC 资源。 该 cmdlet 还可以使用 -OutputArchivePath 参数在本地创建包。 否则，该 cmdlet 会将 .zip 文件发布到 Blob 存储，然后使用 SAS 令牌保护该文件。

该 cmdlet 创建的 .ps1 配置脚本位于存档文件夹根目录中的 .zip 文件内。 模块文件夹位于资源的存档文件夹中。

**Set-AzVMDscExtension** cmdlet 将 PowerShell DSC 扩展所需的设置注入 VM 配置对象。

**Get-AzVMDscExtension** cmdlet 检索特定 VM 的 DSC 扩展状态。

**Get-AzVMDscExtensionStatus** cmdlet 检索由 DSC 扩展处理程序启用的 DSC 配置的状态。 可在一个或一组 VM 上执行此操作。

**Remove-AzVMDscExtension** cmdlet 从特定的 VM 中删除扩展处理程序。 此 cmdlet 不会删除配置、卸载 WMF 或更改 VM 上已应用的设置。 而只删除扩展处理程序。 

有关资源管理器 DSC 扩展 cmdlet 的重要信息：

- Azure 资源管理器 cmdlet 是同步的。
- *ResourceGroupName*、*VMName*、*ArchiveStorageAccountName*、*Version* 和 *Location* 都是必需的参数。
- *ArchiveResourceGroupName* 是可选参数。 如果用户的存储帐户所属的资源组与创建 VM 的资源组不同，用户可以指定此参数。
- 使用 **AutoUpdate** 开关可在有最新版本可用时将扩展处理程序自动更新为最新版本。 当发布了新版本的 WMF 时，此参数可能会导致 VM 重启。

### <a name="get-started-with-cmdlets"></a>cmdlet 入门

Azure DSC 扩展可在部署过程中使用 DSC 配置文档直接配置 Azure VM。 此步骤不会将节点注册到自动化。 不会集中管理节点。

下面是一个简单的配置示例。 以 IisInstall.ps1 为名称将配置保存在本地。

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

以下命令将 IisInstall.ps1 脚本放在指定的 VM 上。 这些命令还会执行配置，然后报告状态。

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

可以使用 Azure CLI 将 DSC 扩展部署到现有的虚拟机。

对于运行 Windows 的虚拟机：

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

对于运行 Linux 的虚拟机：

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Azure 门户功能

在门户中设置 DSC：

1. 转到某个 VM。
2. 在“设置”下选择“扩展”。
3. 在创建的新页面中，依次选择“添加”、“PowerShell Desired State Configuration”。
4. 在扩展信息页面底部，单击“创建”。

门户收集以下输入：

- **配置模块或脚本**：这是必填字段（[默认配置脚本](#default-configuration-script)的窗体尚未更新。 配置模块和脚本需要一个包含配置脚本的 .ps1 文件，或者需要一个 .zip 文件，其中的 .ps1 配置脚本位于根目录。 如果使用 .zip 文件，则必须将所有依赖资源包含在 .zip 的模块文件夹中。 可以使用 Azure PowerShell SDK 随附的 Publish-AzureVMDscConfiguration -OutputArchivePath cmdlet 来创建 .zip 文件。 系统会将 .zip 文件上传到用户 Blob 存储中，并使用 SAS 令牌对其进行保护。

- **配置的模块限定名称**：可以在 .ps1 文件中包含多个配置函数。 请输入配置 .ps1 脚本的名称，后跟 \\ 和配置函数的名称。 例如，如果 .ps1 脚本的名称为 configuration.ps1，而配置为 **IisInstall**，请输入 **configuration.ps1\IisInstall**。

- **配置参数**：如果配置函数采用参数，请使用 **argumentName1=value1,argumentName2=value2** 格式在此处输入。 此格式与 PowerShell cmdlet 或资源管理器模板中接受的配置参数格式不同。

- **配置数据 PSD1 文件**：此字段可选。 如果配置要求 .psd1 中有配置数据文件，请使用此字段来选择数据字段，然后将它上传到用户 Blob 存储。 配置数据文件在 Blob 存储中受 SAS 令牌的保护。

- **WMF 版本**：指定应在 VM 上安装的 Windows Management Framework (WMF) 版本。 将此属性设置为“latest”可安装最新版本的 WMF。 目前，此属性的可能值只有“4.0”、“5.0”、“5.1”和“latest”。 这些可能值将来可能会更新。 默认值为 **latest**。

- **数据收集**：确定扩展是否将收集遥测数据。 有关详细信息，请参阅 [Azure DSC 扩展数据集合](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/)。

- **版本**：指定要安装的 DSC 扩展的版本。 有关版本信息，请参阅 [DSC 扩展版本历史记录](/powershell/dsc/azuredscexthistory)。

- **自动升级次要版本**：此字段映射到 cmdlet 中的 **AutoUpdate** 开关，使扩展能够在安装过程中自动更新到最新版本。 “是”将指示扩展处理程序使用最新可用版本，“否”将强制安装指定的版本。 既不选择“是”也不选择“否”相当于选择“否”。

## <a name="logs"></a>日志

扩展的日志存储在以下位置：`C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>后续步骤

- 有关 PowerShell DSC 的详细信息，请转到 [PowerShell 文档中心](/powershell/dsc/overview)。
- 查看[适用于 DSC 扩展的资源管理器模板](dsc-template.md)。
- 若要了解可以使用 PowerShell DSC 管理的其他功能并获取更多 DSC 资源，请浏览 [PowerShell 库](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)。
- 有关将敏感参数传入配置的详细信息，请参阅[使用 DSC 扩展处理程序安全管理凭据](dsc-credentials.md)。
