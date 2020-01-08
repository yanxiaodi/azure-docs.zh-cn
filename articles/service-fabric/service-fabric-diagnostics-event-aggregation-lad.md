---
title: Azure Service Fabric 事件聚合与 Linux Azure 诊断 |Microsoft Docs
description: 了解有关聚合和使用 LAD 监视和诊断 Azure Service Fabric 群集的收集事件。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: 212158d9a76fa2e49c60be0b5c52f281497c155b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393123"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>使用 Linux Azure 诊断的事件聚合和集合
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

当你运行 Azure Service Fabric 群集时，最好是从一个中心位置的所有节点中收集日志。 将日志放在中心位置可帮助分析和排查群集中的问题，或该群集中运行的应用程序与服务的问题。

上传和收集日志的方式之一是使用 Linux Azure 诊断 (LAD) 扩展，它可将日志上传到 Azure 存储，并且还提供了将日志发送到 Azure Application Insights 或事件中心的选项。 此外可以使用外部进程读取存储中的事件并将它们放在分析平台产品，例如[Azure Monitor 日志](../log-analytics/log-analytics-service-fabric.md)或其他日志分析解决方案。

## <a name="log-and-event-sources"></a>日志和事件源

### <a name="service-fabric-platform-events"></a>Service Fabric 平台事件
Service Fabric 通过 [LTTng](https://lttng.org) 发出几个现成可用的日志，包括操作事件或运行时事件。 这些日志存储在群集的资源管理器模板指定的位置。 若要获取存储帐户的详细信息，请搜索 AzureTableWinFabETWQueryable  标记，然后查找 StoreConnectionString  。

### <a name="application-events"></a>应用程序事件
 检测软件时，事件按指定从应用程序和服务的代码中发出。 可以使用任何能够写入基于文本的日志的日志记录解决方案，例如 LTTng。 有关详细信息，请参阅有关跟踪应用程序的 LTTng 文档。

[在本地计算机开发安装过程中监视和诊断服务](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)。

## <a name="deploy-the-diagnostics-extension"></a>部署诊断扩展
收集日志的第一个步骤是将诊断扩展部署在 Service Fabric 群集中的每个 VM 上。 诊断扩展将收集每个 VM 上的日志，并将它们上传到指定的存储帐户。 

要在创建群集期间将诊断扩展部署到群集中的 VM，请将“**诊断**”设置为“**打开**”。 创建群集后，无法使用门户更改此设置，因此必须在资源管理器模板中进行相应的更改。

这会将 LAD 代理配置为监视指定的日志文件。 每当在文件中追加新行时，该代理将创建一个 syslog 条目并将其发送到指定的存储（表）。


## <a name="next-steps"></a>后续步骤

1. 若要更详细了解在排查问题时应检查哪些事件，请参阅 [LTTng 文档](https://lttng.org/docs)和[使用 LAD](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux)。
2. [设置 Log Analytics 代理](service-fabric-diagnostics-event-analysis-oms.md)来帮助收集指标、监视群集上部署的容器和直观显示日志 
