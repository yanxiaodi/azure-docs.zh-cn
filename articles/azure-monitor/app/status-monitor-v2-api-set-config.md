---
title: Azure 状态监视器 v2 API 参考：设置配置 |Microsoft Docs
description: 状态监视器 v2 API 参考。 Set-ApplicationInsightsMonitoringConfig. 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 6d93be5b01be63a75041b939f6b8deb9106c4262
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200451"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig"></a>状态监视器 v2 API：Set-ApplicationInsightsMonitoringConfig

本文档介绍属于 [Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的 cmdlet。

## <a name="description"></a>描述

在不进行完全重新安装的情况下设置配置文件。
重启 IIS 以使更改生效。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。


## <a name="examples"></a>示例

### <a name="example-with-a-single-instrumentation-key"></a>使用单个检测密钥的示例
在此示例中，将为当前计算机上的所有应用分配一个检测密钥。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>使用检测密钥映射的示例
在本示例中：
- `MachineFilter` 使用 `'.*'` 通配符匹配当前计算机。
- `AppFilter='WebAppExclude'` 提供 `null` 检测密钥。 不会检测指定的应用。
- `AppFilter='WebAppOne'` 为指定的应用分配唯一的检测密钥。
- `AppFilter='WebAppTwo'` 为指定的应用分配唯一的检测密钥。
- 最后，`AppFilter` 还使用 `'.*'` 通配符来匹配按以前规则不匹配的所有 Web 应用，并分配一个默认的检测密钥。
- 添加空格以提高可读性。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**必需。** 使用此参数提供单个检测密钥以供目标计算机上的所有应用使用。

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必需。** 使用此参数可提供多个检测密钥以及每个应用使用的检测密钥映射。
通过设置 `MachineFilter`，可以为多台计算机创建单个安装脚本。

> [!IMPORTANT]
> 应用将按照提供规则的顺序与规则相匹配。 因此，你应该首先指定最特定的规则，最后指定最通用的规则。

#### <a name="schema"></a>架构
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** 是计算机或 VM 名称所需的 C# 正则表达式。
    - “.*”将匹配所有项
    - “ComputerName”将仅匹配具有指定名称的计算机。
- **AppFilter** 是计算机或 VM 名称所需的 C# 正则表达式。
    - “.*”将匹配所有项
    - “ApplicationName”将仅匹配具有指定名称的 IIS 应用。
- 需要 **InstrumentationKey** 才能监视与上述两个筛选器匹配的应用。
    - 如果要定义排除监视的规则，请将此值保留为 null。


### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关可显示详细日志。


## <a name="output"></a>Output

默认情况下没有输出。

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>通过 -InstrumentationKey 设置配置文件的示例详细输出

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>通过 -InstrumentationKeyMap 设置配置文件的示例详细输出

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>后续步骤

  查看遥测：
 - [浏览指标](../../azure-monitor/app/metrics-explorer.md)，以便监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)以诊断问题。
- [使用分析](../../azure-monitor/app/analytics.md)，以便进行更高级的查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。
 
 添加更多遥测：
 - [创建 Web 测试](monitor-web-app-availability.md)，以确保站点保持活动状态。
- [添加 Web 客户端遥测](../../azure-monitor/app/javascript.md)，以查看网页代码中的异常并启用跟踪调用。
- [将 Application Insights SDK 添加到代码](../../azure-monitor/app/asp-net.md)，以便插入跟踪和日志调用
 
 使用状态监视器 v2 执行更多操作：
 - 使用我们的指南可[排查状态监视器 v2 问题](status-monitor-v2-troubleshoot.md)。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认是否正确记录了你的设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
