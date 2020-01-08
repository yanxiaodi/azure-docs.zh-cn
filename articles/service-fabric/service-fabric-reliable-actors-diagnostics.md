---
title: 执行组件诊断和监视 | Microsoft Docs
description: 本文描述了 Service Fabric Reliable Actors 运行时中的诊断和性能监视功能，包括由其发出的事件和性能计数器。
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: chackdan
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 5f573db887b3acc2c4a668a8c19c7f8e3cb25019
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60726564"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Reliable Actors 的诊断和性能监视
Reliable Actors 运行时发出 [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) 事件和[性能计数器](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)。 这些有助于深入了解运行时的运行状况以及进行故障排除和性能监视。

## <a name="eventsource-events"></a>EventSource 事件
Reliable Actors 运行时的 EventSource 提供程序名称为“Microsoft-ServiceFabric-Actors”。 [在 Visual Studio 中调试](service-fabric-debugging-your-application.md)执行组件应用程序时，来自此事件源的事件显示在“[诊断事件](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)”窗口中。

有助于收集和/或查看 EventSource 事件的工具和技术示例包括 [PerfView](https://www.microsoft.com/download/details.aspx?id=28567)、[Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)、[语义日志记录](https://msdn.microsoft.com/library/dn774980.aspx)和 [Microsoft TraceEvent 库](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

### <a name="keywords"></a>关键字
属于 Reliable Actors EventSource 的所有事件都与一个或多个关键字相关联。 这样能够对收集的事件进行筛选。 定义了以下关键字位。

| 位 | 描述 |
| --- | --- |
| 0x1 |汇总 Fabric 执行组件运行时的操作的重要事件集。 |
| 0x2 |描述执行组件方法调用的事件集。 有关详细信息，请参阅[执行组件主题简介](service-fabric-reliable-actors-introduction.md)。 |
| 0x4 |与执行组件状态相关的事件集。 有关详细信息，请参阅[执行组件状态管理](service-fabric-reliable-actors-state-management.md)主题。 |
| 0x8 |与执行组件中基于轮次的并发相关的事件集。 有关详细信息，请参阅[并发](service-fabric-reliable-actors-introduction.md#concurrency)主题。 |

## <a name="performance-counters"></a>性能计数器
Reliable Actors 运行时定义以下性能计数器类别。

| 类别 | 描述 |
| --- | --- |
| Service Fabric 执行组件 |特定于 Azure Service Fabric 执行组件的计数器，例如保存执行组件状态所用的时间。 |
| Service Fabric 执行组件方法 |特定于由 Service Fabric 执行组件实现的方法的计数器，例如调用执行组件方法的频率。 |

以上每个类别都有一个或多个计数器。

Windows 操作系统中默认可用的 [Windows 性能监视器](https://technet.microsoft.com/library/cc749249.aspx)应用程序可用于收集和查看性能计数器数据。 [Azure 诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)是另一种用于收集性能计数器数据并将其上传到 Azure 表的工具。

### <a name="performance-counter-instance-names"></a>性能计数器实例名称
具有大量执行组件服务或执行组件服务分区的群集将具有大量执行组件性能计数器实例。 性能计数器实例名称有助于标识与性能计数器实例相关联的特定[分区](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)和执行组件方法（如果适用）。

#### <a name="service-fabric-actor-category"></a>Service Fabric 执行组件类别
对于类别 `Service Fabric Actor`，计数器实例名称采用以下格式：

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* 是与性能计数器实例相关联的 Service Fabric 分区 ID 的字符串表示。 分区 ID 是 GUID，并且其字符串表示是通过使用格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 方法生成的。

*ActorRuntimeInternalID* 是由 Fabric 执行组件运行时生成的供内部使用的 64 位整数的字符串表示。 这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。 用户不应尝试解释此部分的性能计数器实例名称。

下面是属于 `Service Fabric Actor` 类别的计数器的计数器实例名称的示例：

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

在上述示例中，`2740af29-78aa-44bc-a20b-7e60fb783264` 是 Service Fabric 分区 ID 的字符串表示，`635650083799324046` 是运行时生成的供内部使用的 64 位 ID。

#### <a name="service-fabric-actor-method-category"></a>Service Fabric 执行组件方法类别
对于类别 `Service Fabric Actor Method`，计数器实例名称采用以下格式：

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* 是与性能计数器实例相关联的执行组件方法的名称。 方法名称的格式是根据 Fabric 执行组件运行时中的一些逻辑来确定的，该逻辑可以平衡名称的可读性和 Windows 上对性能计数器实例名称的最大长度的约束。

*ActorsRuntimeMethodId* 是由 Fabric 执行组件运行时生成的供内部使用的 32 位整数的字符串表示。 这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。 用户不应尝试解释此部分的性能计数器实例名称。

*ServiceFabricPartitionID* 是与性能计数器实例关联的 Service Fabric 分区 ID 的字符串表示。 分区 ID 是 GUID，并且其字符串表示是通过使用格式说明符“D”的 [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) 方法生成的。

*ActorRuntimeInternalID* 是由 Fabric 执行组件运行时生成的供内部使用的 64 位整数的字符串表示。 这包括在性能计数器实例名称中，以确保其唯一性并避免与其他性能计数器实例名称发生冲突。 用户不应尝试解释此部分的性能计数器实例名称。

下面的示例展示了属于 `Service Fabric Actor Method` 类别的计数器的计数器实例名称：

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

在上述示例中，`ivoicemailboxactor.leavemessageasync` 是方法名称，`2` 是运行时生成的供内部使用的 32 位 ID，`89383d32-e57e-4a9b-a6ad-57c6792aa521` 是 Service Fabric 分区 ID 的字符串表示，`635650083804480486` 是运行时生成的供内部使用的 64 位 ID。

## <a name="list-of-events-and-performance-counters"></a>事件和性能计数器的列表
### <a name="actor-method-events-and-performance-counters"></a>执行组件方法事件和性能计数器
Reliable Actors 运行时发出以下与[执行组件方法](service-fabric-reliable-actors-introduction.md)相关的事件。

| 事件名称 | 事件 ID | 级别 | 关键字 | 描述 |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |详细 |0x2 |执行组件运行时即将调用执行组件方法。 |
| ActorMethodStop |8 |详细 |0x2 |执行组件方法已执行完毕。 也就是说，已返回运行时的对执行组件方法的异步调用，此执行组件方法返回的任务已完成。 |
| ActorMethodThrewException |9 |警告 |0x3 |在执行执行组件方法时，即在运行时的对执行组件方法的异步调用过程中或在执行执行组件方法返回的任务过程中引发异常。 此事件表示执行组件代码中出现某种形式的故障，需要调查。 |

Reliable Actors 运行时发布与执行执行组件方法相关的下列性能计数器。

| 类别名称 | 计数器名称 | 描述 |
| --- | --- | --- |
| Service Fabric 执行组件方法 |调用/秒 |每秒调用执行组件服务方法的次数 |
| Service Fabric 执行组件方法 |每次调用的平均毫秒数 |以毫秒为单位执行执行组件服务方法所用的时间 |
| Service Fabric 执行组件方法 |引发的异常数/秒 |执行组件服务方法每秒引发异常的次数 |

### <a name="concurrency-events-and-performance-counters"></a>并发事件和性能计数器
Reliable Actors 运行时发出以下与[并发](service-fabric-reliable-actors-introduction.md#concurrency)相关的事件。

| 事件名称 | 事件 ID | 级别 | 关键字 | 描述 |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |详细 |0x8 |在执行组件中每次新的轮次开始时写入此事件。 其中包含挂起的执行组件调用数。这些调用稍后将获取用于强制执行基于轮次的并发的每执行组件锁定。 |

Reliable Actors 运行时发布与并发相关的以下性能计数器。

| 类别名称 | 计数器名称 | 描述 |
| --- | --- | --- |
| Service Fabric 执行组件 |等待角色锁定的角色调用数 |等待获取强制执行基于轮次的并发的每个执行组件锁的待处理执行组件调用次数。 |
| Service Fabric 执行组件 |每个锁等待的平均毫秒数 |获取强制执行基于轮次的并发的每个执行组件锁所用的时间（以毫秒为单位） |
| Service Fabric 执行组件 |持有执行组件锁的平均毫秒数 |持有每个执行组件锁的时间（以毫秒为单位） |

### <a name="actor-state-management-events-and-performance-counters"></a>执行组件状态管理事件和性能计数器
Reliable Actors 运行时发出以下与[执行组件状态管理](service-fabric-reliable-actors-state-management.md)相关的事件。

| 事件名称 | 事件 ID | 级别 | 关键字 | 描述 |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |详细 |0x4 |执行组件运行时即将保存执行组件状态。 |
| ActorSaveStateStop |11 |详细 |0x4 |执行组件运行时已完成保存执行组件状态。 |

Reliable Actors 运行时发布与执行组件状态管理相关的以下性能计数器。

| 类别名称 | 计数器名称 | 描述 |
| --- | --- | --- |
| Service Fabric 执行组件 |每个保存状态操作的平均毫秒数 |以毫秒为单位保存执行组件状态所用的时间 |
| Service Fabric 执行组件 |每个加载状态操作的平均毫秒数 |加载执行组件状态所用的时间（以毫秒为单位） |

### <a name="events-related-to-actor-replicas"></a>与执行组件副本相关的事件
Reliable Actors 运行时发出以下与[执行组件副本](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)相关的事件。

| 事件名称 | 事件 ID | 级别 | 关键字 | 描述 |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |第 |信息性 |0x1 |执行组件副本将角色更改为“主要”。 这意味着在此副本内创建此分区的执行组件。 |
| ReplicaChangeRoleFromPrimary |2 |信息性 |0x1 |执行组件副本将角色更改为“非主要”。 这意味着不再在此副本内创建此分区的执行组件。 不会将任何新请求传送到此副本中已创建的执行组件。 正在进行中的任何请求完成后会销毁执行组件。 |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>执行组件激活和停用事件以及性能计数器
Reliable Actors 运行时发出以下与[执行组件激活和停用](service-fabric-reliable-actors-lifecycle.md)相关的事件。

| 事件名称 | 事件 ID | 级别 | 关键字 | 描述 |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |信息性 |0x1 |执行组件已激活。 |
| ActorDeactivated |6 |信息性 |0x1 |执行组件已停用。 |

Reliable Actors 运行时发布以下与执行组件激活和停用相关的性能计数器。

| 类别名称 | 计数器名称 | 描述 |
| --- | --- | --- |
| Service Fabric 执行组件 |OnActivateAsync 平均毫秒数 |执行 OnActivateAsync 方法所花时间（以毫秒为单位） |

### <a name="actor-request-processing-performance-counters"></a>执行组件请求处理的性能计数器
当客户端通过执行组件代理对象调用方法时，会通过网络向执行组件服务发送请求消息。 该服务处理此请求消息并向客户端返回响应。 Reliable Actors 运行时发布以下与执行组件请求处理相关的性能计数器。

| 类别名称 | 计数器名称 | 描述 |
| --- | --- | --- |
| Service Fabric 执行组件 |未完成的请求数 |正在服务中处理的请求数 |
| Service Fabric 执行组件 |每个请求的平均毫秒数 |服务处理请求所用时间（以毫秒为单位） |
| Service Fabric 执行组件 |反序列化请求的平均毫秒数 |当服务收到执行组件请求消息时对此请求消息进行反序列化所用的时间（以毫秒为单位） |
| Service Fabric 执行组件 |序列化响应的平均毫秒数 |在将响应发送到客户端之前，在服务中序列化执行组件响应消息所用的时间（以毫秒为单位） |

## <a name="next-steps"></a>后续步骤
* [Reliable Actors 如何使用 Service Fabric 平台](service-fabric-reliable-actors-platform.md)
* [执行组件 API 参考文档](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [代码示例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [PerfView 中的 EventSource 提供程序](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
