---
title: Azure Service Fabric 性能监视 | Microsoft Docs
description: 了解用于监视和诊断 Azure Service Fabric 群集的性能计数器。
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
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: ee1608c40801f568b38ace4670b0d5ea7f73003c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60392885"
---
# <a name="performance-metrics"></a>性能指标

应收集指标，以了解群集及其中运行的应用程序的性能。 对于 Service Fabric 群集，建议收集以下性能计数器。

## <a name="nodes"></a>Nodes

对于群集中的计算机，建议收集以下性能计数器，以便更好地了解每台计算机上的负载，并制定相应的群集缩放决策。

| 计数器类别 | 计数器名称 |
| --- | --- |
| 逻辑磁盘 | 逻辑磁盘可用空间 |
| PhysicalDisk(per Disk) | 平均值磁盘读取队列长度 |
| PhysicalDisk(per Disk) | 平均磁盘写入队列长度 |
| PhysicalDisk(per Disk) | 平均磁盘秒数/读取 |
| PhysicalDisk(per Disk) | 平均磁盘秒数/写入 |
| PhysicalDisk(per Disk) | 磁盘读取数/秒 |
| PhysicalDisk(per Disk) | 磁盘读取字节数/秒 |
| PhysicalDisk(per Disk) | 磁盘写入数/秒 |
| PhysicalDisk(per Disk) | 磁盘写入字节数/秒 |
| 内存 | 可用兆字节数 |
| PagingFile | 使用百分比 |
| Processor(Total) | 处理器时间百分比 |
| Process (per service) | 处理器时间百分比 |
| Process (per service) | ID 进程 |
| Process (per service) | 专用字节 |
| Process (per service) | 线程计数 |
| Process (per service) | 虚拟字节 |
| Process (per service) | 工作集 |
| Process (per service) | 工作集 - 专用 |
| Network Interface(all-instances) | 读取的字节数 |
| Network Interface(all-instances) | 发送的字节数 |
| Network Interface(all-instances) | 总字节数 |
| Network Interface(all-instances) | 输出队列长度 |
| Network Interface(all-instances) | 放弃的出站数据包 |
| Network Interface(all-instances) | 放弃的已接收数据包 |
| Network Interface(all-instances) | 出站数据包错误 |
| Network Interface(all-instances) | 已接收的数据包错误 |

## <a name="net-applications-and-services"></a>.NET 应用程序和服务

若要将 .NET 服务部署到群集，请收集以下计数器。 

| 计数器类别 | 计数器名称 |
| --- | --- |
| .NET CLR Memory (per service) | 进程 ID |
| .NET CLR Memory (per service) | 提交的字节总数 |
| .NET CLR Memory (per service) | 保留的字节总数 |
| .NET CLR Memory (per service) | 所有堆中的字节数 |
| .NET CLR Memory (per service) | 大型对象堆大小 |
| .NET CLR Memory (per service) | GC 句柄数 |
| .NET CLR Memory (per service) | 第 0 代集合数 |
| .NET CLR Memory (per service) | 第 1 代集合数 |
| .NET CLR Memory (per service) | 第 2 代集合数 |
| .NET CLR Memory (per service) | GC 中的时间百分比 |

### <a name="service-fabrics-custom-performance-counters"></a>Service Fabric 的自定义性能计数器

Service Fabric 生成大量自定义性能计数器。 如果已安装 SDK，可以在 Windows 计算机上的“性能监视器”应用程序（“开始”>“性能监视器”）中看到完整列表。 

在要部署到群集的应用程序中，如果使用的是 Reliable Actors，请添加 `Service Fabric Actor` 和 `Service Fabric Actor Method` 类别的计数器（请参阅 [Service Fabric Reliable Actors 诊断](service-fabric-reliable-actors-diagnostics.md)）。

如果使用 Reliable Services 或服务远程处理，可同样获得应从其中收集计数器的 `Service Fabric Service` 和 `Service Fabric Service Method` 计数器类别。请参阅[使用服务远程处理进行监视](service-fabric-reliable-serviceremoting-diagnostics.md)和 [Reliable Services 性能计数器](service-fabric-reliable-services-diagnostics.md#performance-counters)。 

如果使用 Reliable Collections，建议通过 `Service Fabric Transactional Replicator` 添加 `Avg. Transaction ms/Commit`，以收集每个事务指标的平均提交延迟。


## <a name="next-steps"></a>后续步骤

* 详细了解 Service Fabric 中的[平台级事件生成情况](service-fabric-diagnostics-event-generation-infra.md)
* 通过 [Log Analytics 代理](service-fabric-diagnostics-oms-agent.md)收集性能指标
