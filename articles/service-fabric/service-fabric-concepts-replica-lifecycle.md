---
title: Azure Service Fabric 中的副本和实例 | Microsoft Docs
description: 了解副本和实例 - 其功能和生命周期
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: 7f8638365b40395a5dd82457c40e5c15209ba1a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60882372"
---
# <a name="replicas-and-instances"></a>副本和实例 
本文概述了有状态服务副本和无状态服务实例的生命周期。

## <a name="instances-of-stateless-services"></a>无状态服务的实例
无状态服务的实例是在其中一个群集节点上运行的服务逻辑的副本。 分区内的实例由其 InstanceId 唯一标识  。 实例的生命周期可在以下图表中进行建模：

![实例生命周期](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
群集资源管理器确定实例的放置后，它将进入此生命周期状态。 此实例将在节点上启动。 将启动应用程序主机，创建并打开实例。 启动完成后，实例将转换为就绪状态。 

如果此实例的应用程序主机或节点发生故障，它将转换为已删除状态。

### <a name="ready-rd"></a>就绪 (RD)
在就绪状态下，实例已启动并且正在节点上运行。 如果此实例是可靠的服务，则已调用 RunAsync。  

如果此实例的应用程序主机或节点发生故障，它将转换为已删除状态。

### <a name="closing-cl"></a>关闭 (CL)
在关闭状态下，Azure Service Fabric 正在关闭此节点上的实例。 此关闭可能由多种原因所致，例如应用程序升级、负载均衡或正在删除服务。 关闭完成后，它将转换为已删除状态。

### <a name="dropped-dd"></a>已删除 (DD)
在已删除状态下，实例不再在节点上运行。 此时，Service Fabric 维护关于此实例的元数据，该元数据最终也将被删除。

> [!NOTE]
> 可通过使用 `Remove-ServiceFabricReplica` 上的 ForceRemove 选项实现从任意状态到已删除状态的转换  。
>

## <a name="replicas-of-stateful-services"></a>有状态服务的副本
有状态服务的副本是在其中一个群集节点上运行的服务逻辑的副本。 此外，副本会保留该服务的状态的副本。 以下两个相关的概念介绍了有状态副本的生命周期和行为：
- 副本生命周期
- 副本角色

以下讨论介绍了持久的有状态服务。 对于临时的（或内存中）有状态服务，关闭状态和已删除状态等效。

![副本生命周期](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
InBuild 副本是创建或准备加入副本集的副本。 根据副本角色，IB 具有不同的语义。 

如果 InBuild 副本的应用程序主机或节点发生故障，它将转换为关闭状态。

   - **主 InBuild 副本**:主 InBuild 副本是分区的第一个副本。 此副本通常发生在创建分区时。 当分区的所有副本均重启或删除时，主 InBuild 副本也会出现。

   - **空闲辅助 InBuild 副本**:这些是群集资源管理器创建的新副本或已关闭的现有副本，需要将其添加回集。 这些副本先由主要副本播种或生成，然后才能加入到副本集作为活动的次要副本并参与操作的仲裁确认。

   - **ActiveSecondary InBuild 副本**:某些查询中观察到此状态。 这是一种优化，其中副本集不会更改，但需要生成一个副本。 副本本身遵循常规状态的计算机转换（如副本角色部分所述）。

### <a name="ready-rd"></a>就绪 (RD)
就绪副本是参与复制和操作的仲裁确认的副本。 就绪状态适用于主要副本和活动的次要副本。

如果就绪副本的应用程序主机或节点发生故障，它将转换为关闭状态。

### <a name="closing-cl"></a>关闭 (CL)
在以下情况下，副本会进入关闭状态：

- **关闭副本代码**:Service Fabric 可能需要关闭副本的运行代码。 此关闭可能由多种原因导致。 例如，它可能由应用程序、构造或基础结构升级导致，或由副本报告的错误导致。 副本关闭完成后，副本将转换为关闭状态。 与存储在磁盘上的此副本关联的持久状态未得到清理。

- **从群集中删除副本**:Service Fabric 可能需要删除持久的状态并关闭副本的运行代码。 此关闭可能由多种原因导致，例如负载均衡。

### <a name="dropped-dd"></a>已删除 (DD)
在已删除状态下，实例不再在节点上运行。 节点上也没有保留任何状态。 此时，Service Fabric 维护关于此实例的元数据，该元数据最终也将被删除。

### <a name="down-d"></a>关闭 (D)
在关闭状态下，副本代码未运行，但该副本的持久状态存在于该节点上。 副本可能会因多种原因而关闭，例如节点关闭、副本代码发生故障、应用程序升级或副本错误。

关闭的副本可按需由 Service Fabric 打开，例如当完成节点上的升级时。

副本角色在关闭状态下不相关。

### <a name="opening-op"></a>打开 (OP)
当 Service Fabric 需要再次备份副本时，关闭的副本将进入打开状态。 例如，此状态可能出现在应用程序的代码升级在节点上完成后。 

如果打开的副本的应用程序主机或节点发生故障，它将转换为关闭状态。

副本角色在打开状态下不相关。

### <a name="standby-sb"></a>备用 (SB)
备用副本是在关闭后打开的持久服务的副本。 如果需要将另一副本添加到副本集，此副本可供 Service Fabric 使用（因为此副本已经具有状态的某些部分，生成过程将更快）。 在 StandByReplicaKeepDuration 到期后，备用副本将被丢弃。

如果备用副本的应用程序主机或节点发生故障，它将转换为关闭状态。

副本角色在备用状态下不相关。

> [!NOTE]
> 任何未关闭或删除的副本将被视为可启动  的副本。
>

> [!NOTE]
> 可通过使用 `Remove-ServiceFabricReplica` 上的 ForceRemove 选项实现从任意状态到已删除状态的转换  。
>

## <a name="replica-role"></a>副本角色 
副本的角色确定其在副本集内的功能：

- **主要 (P)** :还有一个主要副本集，它负责执行读取和写入操作。 
- **次要 (S)** :这些是从主接收状态更新、 应用它们，并发送回确认的副本。 副本集内有多个活动的次要副本。 这些活动次要副本的数量确定服务可以处理的错误数量。
- **空闲的次要副本 (I)** :这些副本正在生成的主数据库。 在将其升级到活动次要副本之前，它们接收来自主要副本的状态。 
- **无 (N)** :这些副本在副本集内没有责任。
- **未知 (U)** :这是一个副本的初始角色接收任何之前**ChangeRole**从 Service Fabric API 调用。

以下图表说明了副本角色转换以及可能发生此类转换的一些示例方案：

![副本角色](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U-&GT; P:创建新的主要副本。
- U-&GT; I:创建新的空闲副本。
- U-&GT; N:删除备用副本。
- I -> S:空闲副本升级辅助站点到站点的活动辅助数据库，以便其确认构成仲裁。
- I-&GT; P:主数据库到空闲的次要副本升级。 当空闲的次要副本成为主要副本的正确候选项时，在特殊的重新配置下可能发生此情况。
- I-&GT; N:删除空闲的次要副本。
- S-&GT; P:活动辅助数据库到主副本的升级。 这可能由主要副本的故障转移或由群集资源管理器启动的主要副本移动导致。 例如，主要副本可能响应应用程序升级或负载均衡。
- S-&GT; N:删除的活动的次要副本。
- P-&GT; S:主要副本降级。 这可能由群集资源管理器启动的主要副本移动导致。 例如，主要副本可能响应应用程序升级或负载均衡。
- P-&GT; N:删除主要副本。

> [!NOTE]
> 更高级别的编程模型（如 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 和 [Reliable Services](service-fabric-reliable-services-introduction.md)）对开发人员隐藏副本角色的概念。 在 Actors 中，角色的概念是不必要的。 在 Services 中，大多数情况下它进行了很大程度的简化。
>

## <a name="next-steps"></a>后续步骤
有关 Service Fabric 概念的详细信息，请参阅以下文章：

[Reliable Services 生命周期 - C#](service-fabric-reliable-services-lifecycle.md)

