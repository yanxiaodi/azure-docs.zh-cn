---
title: 对 Azure 更改跟踪问题进行故障排除
description: 本文提供了有关对更改跟踪进行故障排除的信息
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: beb0b89bdbf143c89a83c0813313a8bbda7235d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564850"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>对更改跟踪和清单进行故障排除

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>场景：更改跟踪记录不在 Windows 计算机中显示

#### <a name="issue"></a>问题

不会看到已加入更改跟踪的 Windows 计算机的任何清单或更改跟踪结果。

#### <a name="cause"></a>原因

此错误可能由以下原因引起：

1. **Microsoft Monitoring Agent** 未运行
2. 返回到自动化帐户的通信被阻止。
3. 用于更改跟踪的管理包未下载。
4. 正在加入的 VM 可能来自未在安装 Microsoft Monitoring Agent 的情况下进行系统准备的克隆计算机。

#### <a name="resolution"></a>解决

1. 验证 **Microsoft Monitoring Agent** (HealthService.exe) 是否正在计算机上运行。
1. 检查计算机上的**事件查看器**并查看包含 `changetracking` 一词的任何事件。
1. 访问[网络规划](../automation-hybrid-runbook-worker.md#network-planning)，了解需要允许哪些地址和端口才能使更改跟踪正常工作。
1. 验证本地是否存在以下更改跟踪和清单管理包：
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. 如果使用的是克隆的映像，请首先对映像进行系统准备，然后在事后安装 Microsoft Monitoring Agent 代理。

如果这些解决方案未解决你的问题并且你联系了客户支持，则可以运行以下命令在代理上收集诊断信息

在代理计算机上，导航到 `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` 并运行以下命令：

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 默认情况下启用的是错误跟踪，如果希望像前面的示例一样启用详细的错误消息，请使用 `VER` 参数。 要进行信息跟踪，请在调用 `StartTracing.cmd` 时使用 `INF`。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
