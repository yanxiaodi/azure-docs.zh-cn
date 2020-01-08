---
title: 监视 Windows 桌面应用的使用情况和性能
description: 使用 Application Insights 分析 Windows 桌面应用的使用情况和性能。
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: mbullwin
ms.openlocfilehash: ed6df8b4724dbb297a0c64fd869d3377545a7595
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932334"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>监视经典 Windows 桌面应用中的使用情况和性能

在 Azure 和其他云中本地托管的应用程序都可以利用 Application Insights。 唯一限制是需要[允许与 Application Insights 服务通信](../../azure-monitor/app/ip-addresses.md)。 若要监视通用 Windows 平台 (UWP) 应用程序，我们建议使用 [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md)。

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>将遥测从经典 Windows 应用程序发送到 Application Insights
1. 在 [Azure 门户](https://portal.azure.com)中，[创建 Application Insights 资源](../../azure-monitor/app/create-new-resource.md )。 对于应用程序类型，选择 ASP.NET 应用。
2. 获取检测密钥的副本。 在刚创建的新资源的 Essentials 下拉列表中找到该密钥。 
3. 在 Visual Studio 中，编辑应用项目的 NuGet 包，并添加 Microsoft.ApplicationInsights.WindowsServer。 （或者，如果只需要逻辑 API，而无需标准遥测收集模块，则选择 Microsoft.ApplicationInsights。）
4. 在代码中设置检测密钥：
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *你的密钥* `";`
   
    或在 ApplicationInsights.config 中设置检测密钥（如果已安装标准遥测包之一）：
   
    `<InstrumentationKey>`*你的密钥*`</InstrumentationKey>` 
   
    如果使用 ApplicationInsights.config，请确保它在解决方案资源管理器中的属性设置为“生成操作”=“内容”、“复制到输出目录”=“复制”。
5. [使用 API](../../azure-monitor/app/api-custom-events-metrics.md) 发送遥测。
6. 运行你的应用, 并在 Azure 门户中创建的资源中查看遥测数据。

## <a name="telemetry"></a>示例代码
```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>后续步骤
* [创建仪表板](../../azure-monitor/app/overview-dashboard.md)
* [诊断搜索](../../azure-monitor/app/diagnostic-search.md)
* [探索指标](../../azure-monitor/app/metrics-explorer.md)
* [编写分析查询](../../azure-monitor/app/analytics.md)

