---
title: Azure Automation State Configuration 概述
description: 对 Azure Automation State Configuration (DSC) 及其术语和已知问题的概述
keywords: powershell dsc, 所需状态配置, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a3a52fbda91d19905bd6add631f536010197c4dd
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061383"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration 概述

Azure Automation State Configuration 是一种 Azure 服务，允许编写、管理和编译 PowerShell Desired State Configuration (DSC) [配置](/powershell/dsc/configurations)，导入 [DSC 资源](/powershell/dsc/resources)，并将配置分配给目标节点，所有操作均在云中进行。

## <a name="why-use-azure-automation-state-configuration"></a>为何使用 Azure Automation State Configuration

与在 Azure 之外使用 DSC 相比，Azure Automation State Configuration 具有多项优势。

### <a name="built-in-pull-server"></a>内置拉取服务器

Azure Automation State Configuration 提供了类似于 [Windows 功能 DSC 服务](/powershell/dsc/pullserver)的 DSC 拉取服务器，这样目标节点可自动接收配置，符合所需状态，并报告回其符合性。 Azure 自动化中的内置拉取服务器消除了设置和维护你自己的拉取服务器的需要。 Azure 自动化的目标可以是云中或本地的虚拟机，或物理 Windows 或 Linux 计算机。

### <a name="management-of-all-your-dsc-artifacts"></a>管理所有 DSC 项目

Azure Automation State Configuration 向 [PowerShell Desired State Configuration](/powershell/dsc/overview) 提供的管理层与 Azure 自动化为 PowerShell 脚本提供的相同。

从 Azure 门户，或从 PowerShell，你可以管理所有的 DSC 配置、资源和目标节点。

![“Azure 自动化”页的屏幕截图](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>将报表数据导入到 Azure Monitor 日志中

使用 Azure Automation State Configuration 进行管理的节点将详细的报表状态数据发送到内置拉取服务器。 可以将 Azure Automation State Configuration 配置为将此数据发送到 Log Analytics 工作区。 若要了解如何将 State Configuration 状态数据发送到 Log Analytics 工作区，请参阅[将 Azure Automation State Configuration 报表数据转发到 Azure Monitor 日志](automation-dsc-diagnostics.md)。

## <a name="prerequisites"></a>先决条件

在使用 Azure Automation State Configuration (DSC) 时，请考虑以下要求。

### <a name="operating-system-requirements"></a>操作系统要求

运行 Windows 的节点支持以下版本：

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

[Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016)独立产品 sku 不包含所需状态配置的实现, 因此无法通过 PowerShell DSC 或 Azure 自动化状态配置进行管理。

运行 Linux 的节点支持以下发行版/版本：

DSC Linux 扩展支持[支持的 Linux 发行](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)版下列出的所有 linux 分发版。

### <a name="dsc-requirements"></a>DSC 要求

对于在 Azure 中运行的所有 Windows 节点，[WMF 5.1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) 将在载入时安装。  对于运行 Windows Server 2012 和 Windows 7 的节点，[将会启用 WinRM](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency)。

对于在 Azure 中运行的所有 Linux 节点，[PowerShell DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 将在载入时安装。

### <a name="network-planning"></a>配置专用网络

如果节点位于专用网络内, 则状态配置 (DSC) 需要以下端口和 Url 才能与自动化通信:

* 端口：只需使用 TCP 443 即可进行出站 Internet 访问。
* 全局 URL：*.azure-automation.net
* 美国弗吉尼亚州政府的全局 URL：*.azure-automation.us
* 代理服务： https://\<workspaceId\>.agentsvc.azure-automation.net

这为托管节点提供网络连接, 以与 Azure 自动化通信。
如果使用在节点之间进行通信的 DSC 资源 (如[WaitFor * 资源](https://docs.microsoft.com/powershell/dsc/reference/resources/windows/waitForAllResource)), 则还需要允许节点之间的流量。
请参阅每个 DSC 资源的文档, 以了解这些网络要求。

#### <a name="proxy-support"></a>代理支持

Windows 版本1809及更高版本中提供了适用于 DSC 代理的代理支持。
若要配置此选项, 请在用于注册节点的元配置[脚本](automation-dsc-onboarding.md#generating-dsc-metaconfigurations)中设置**ProxyURL**和**ProxyCredential**的值。
代理在以前版本的 Windows 的 DSC 中不可用。

对于 Linux 节点, DSC 代理支持代理, 并将使用 http_proxy 变量来确定 url。

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Azure 状态配置网络范围和命名空间

建议在定义异常时使用列出的地址。 对于 IP 地址，可以下载 [Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 此文件每周更新，包含当前部署的范围以及即将对 IP 范围进行的更新。

如果为特定的区域定义了自动化帐户，则可以限制与该区域数据中心之间的通信。 下表提供了每个区域的 DNS 记录：

| **区域** | **DNS 记录** |
| --- | --- |
| 美国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 美国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| East US   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| 美国东部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 西欧 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 北欧 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 东南亚 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 日本东部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亚东南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov 弗吉尼亚州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

有关区域 IP 地址列表（非区域名称列表），请从 Microsoft 下载中心下载 [Azure 数据中心 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653) XML 文件。

> [!NOTE]
> Azure 数据中心 IP 地址 XML 文件列出了 Microsoft Azure 数据中心使用的 IP 地址范围。 文件中包含计算、SQL 和存储范围。
>
>每周都将发布更新的文件。 该文件反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。
>
> 建议每周下载新的 XML 文件。 然后，更新网站以正确地标识 Azure 中运行的服务。 Azure ExpressRoute 用户应注意，此文件过去经常在每个月的第一周更新 Azure 空间的边界网关协议 (BGP) 播发。

## <a name="next-steps"></a>后续步骤

- 有关入门信息，请参阅 [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)
- 要了解如何登记节点，请参阅[登记由 Azure Automation State Configuration 管理的计算机](automation-dsc-onboarding.md)
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译配置](automation-dsc-compile.md)
- 有关 PowerShell cmdlet 参考，请参阅 [Azure Automation State Configuration cmdlet](/powershell/module/azurerm.automation/#automation)
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Azure Automation State Configuration 和 Chocolatey 进行持续部署](automation-dsc-cd-chocolatey.md)
