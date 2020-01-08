---
title: 云服务与 Service Fabric 之间的差异 | Microsoft 文档
description: 有关将应用程序从云服务迁移到 Service Fabric 的概念性概述。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 0b87b1d3-88ad-4658-a465-9f05a3376dee
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 8b486e617389e1611dfebf3d347d2d64df088593
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258649"
---
# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>迁移应用程序之前了解云服务与 Service Fabric 之间的差异。
Microsoft Azure Service Fabric 是面向高度可缩放、高度可靠分布式应用程序的下一代云应用程序平台。 其中引入了许多用于打包、部署、更新和管理分布式云应用程序的新功能。 

本简介指南介绍如何将应用程序从云服务迁移到 Service Fabric。 内容侧重于云服务与 Service Fabric 之间的体系结构与设计差异。

## <a name="applications-and-infrastructure"></a>应用程序和基础结构
云服务与 Service Fabric 之间的最基本差异在于 VM、工作负荷及应用程序之间的关系。 此处的工作负荷定义为编写的、用来执行特定任务或提供服务的代码。

* **云服务涉及到将应用程序部署为 VM。** 编写的代码与 VM 实例（如 Web 角色或辅助角色）密切结合。 在云服务中部署工作负荷就是要部署一个或多个运行该工作负荷的 VM 实例。 应用程序与 VM 没有区别，因此对于应用程序没有正式的定义。 可将应用程序视为云服务部署中的一组 Web 角色或辅助角色实例，或视为整个云服务部署。 在此示例中，应用程序显示为一组角色实例。

![云服务应用程序和拓扑][1]

* **Service Fabric 涉及到将应用程序部署至现有的 VM 或运行 Service Fabric 的 Windows 或 Linux 计算机。** 编写的服务完全与底层的基础结构分离（由 Service Fabric 应用程序平台抽象化），因此可将应用程序部署在多个环境。 Service Fabric 中的工作负载称为“服务”，一个或多个服务在 Service Fabric 应用程序平台上运行的且正式定义的应用程序中进行分组。 可将多个应用程序部署到单个 Service Fabric 群集。

![Service Fabric 应用程序和拓扑][2]

Service Fabric 本身是在 Windows 或 Linux 中运行的应用程序平台层，而云服务是用于部署 Azure 托管的且附加了工作负荷的 VM。
Service Fabric 应用程序模型有许多优点：

* 快速部署。 创建 VM 实例可能非常耗时。 在 Service Fabric 中，只需部署 VM 一次即可构成托管 Service Fabric 应用程序平台的群集。 此后，可将应用程序包快速部署到该群集。
* 高密度托管。 在云服务中，一个辅助角色 VM 托管一个工作负荷。 在 Service Fabric 中，应用程序与运行它们的 VM 分离，这意味着可以将大量应用程序部署到少量 VM，从而降低大量部署所需的总体成本。
* Service Fabric 平台可在任何配备 Windows Server 或 Linux 计算机的位置（无论是 Azure 还是本地）运行。 该平台在底层基础结构上面提供一个抽象层，因此应用程序可在不同的环境中运行。 
* 分布式应用程序管理。 Service Fabric 平台不仅能够托管分布式应用程序，而且能够帮助管理这些应用程序的生命周期，不管托管 VM 或计算机的生命周期如何。

## <a name="application-architecture"></a>应用程序体系结构
云服务应用程序的基础结构通常包含众多的外部服务依赖项（例如服务总线、Azure 表和 Blob 存储、SQL、Redis 等），以管理应用程序的状态与数据，并与云服务部署中的 Web 角色和辅助角色通信。 完整云服务应用程序的示例如下：  

![云服务体系结构][9]

Service Fabric 应用程序还可以选择在整个应用程序中使用相同的外部服务。 在此示例云服务体系结构中，从云服务迁移到 Service Fabric 的最简单路径是只将云服务部署替换为 Service Fabric 应用程序，并将整个体系结构保持相同。 只需进行少量的代码更改，即可将 Web 角色和辅助角色移植到 Service Fabric 无状态服务。

![简单迁移后的 Service Fabric 体系结构][10]

在此阶段，系统应会像往常一样继续工作。 利用 Service Fabric 的有状态服务，在适当的情况下，可将外部状态存储内部化为有状态服务。 这比单纯将 Web 角色和辅助角色迁移到 Service Fabric 无状态服务更复杂，因为需要编写为应用程序提供同等功能（就像外部服务所做的那样）的自定义服务。 这样做的好处包括： 

* 删除外部依赖项 
* 统一部署、管理和升级模型。 

内部化这些服务后，生成的示例基础结构如下所示：

![完整迁移后的 Service Fabric 体系结构][11]

## <a name="communication-and-workflow"></a>通信和工作流
大多数云服务应用程序由多个层组成。 同样，Service Fabric 应用程序由多个服务组成（通常是许多的服务）。 二种常见的通信模型是直接通信，以及通过外部持久性存储通信。

### <a name="direct-communication"></a>直接通信
使用直接通信时，层之间可以通过各自公开的终结点直接通信。 在无状态环境（例如云服务）中，这意味着需要选择 VM 角色的实例（随机或者通过循环机制来平衡负载），并直接连接到其终结点。

![云服务直接通信][5]

 直接通信是 Service Fabric 中常见的通信模型。 Service Fabric 和云服务的重要差别在于，在云服务中是连接到 VM，而在 Service Fabric 中是连接到服务。 这种差别之所以重要，其原因如下：

* 在 Service Fabric 中的服务不受限于托管它们; 的 Vm服务可能在群集中四处移动，实际上，需出于各种原因而移动：资源平衡、 故障转移、 应用程序和基础结构升级和放置或负载约束。 这意味着服务实例的地址可随时更改。 
* Service Fabric 中的一个 VM 可以托管多个服务，且每个服务有其独特的终结点。

Service Fabric 提供服务发现机制（称为“命名服务”），用于解析服务的终结点地址。 

![Service Fabric 直接通信][6]

### <a name="queues"></a>队列
无状态环境（如云服务）中层之间的常见通信机制是使用外部存储队列将一个层的工作任务持久存储到另一个层。 一种常见方案是将作业发送到 Azure 队列或服务总线的 Web 层，辅助角色实例可在其中取消排队和处理作业。

![云服务队列通信][7]

在 Service Fabric 中可以使用同样的通信模型。 这有助于将现有的云服务应用程序迁移到 Service Fabric。 

![Service Fabric 直接通信][8]

## <a name="parity"></a>奇偶校验
[云服务是类似于 Service Fabric 中的控制和易用性程度，但它现在是旧版服务和 Service Fabric 建议用于新开发](https://docs.microsoft.com/azure/app-service/overview-compare); 下面是 API 比较：


| **云服务 API** | **Service Fabric API** | **说明** |
| --- | --- | --- |
| RoleInstance.GetID | FabricRuntime.GetNodeContext.NodeId 或。节点名称 | ID 是节点名称的属性 |
| RoleInstance.GetFaultDomain | FabricClient.QueryManager.GetNodeList | 对节点名称进行筛选，并使用 FD 属性 |
| RoleInstance.GetUpgradeDomain | FabricClient.QueryManager.GetNodeList | 筛选节点名称，并使用升级属性 |
| RoleInstance.GetInstanceEndpoints | FabricRuntime.GetActivationContext 或命名 (ResolveService) | CodePackageActivationContext FabricRuntime.GetActivationContext 和通过 ServiceInitializationParameters.CodePackageActivationContext 期间提供副本中提供。初始化 |
| RoleEnvironment.GetRoles | FabricClient.QueryManager.GetNodeList | 如果你想要执行相同的筛选的类型可获取的列表按从群集的节点类型通过 FabricClient.ClusterManager.GetClusterManifest 清单并抓取从那里的角色/节点类型。 |
| RoleEnvironment.GetIsAvailable | 连接 WindowsFabricCluster 或创建 fabricruntime 会指向一个特定的节点 | * |
| RoleEnvironment.GetLocalResource | CodePackageActivationContext.Log/Temp/Work | * |
| RoleEnvironment.GetCurrentRoleInstance | CodePackageActivationContext.Log/Temp/Work | * |
| LocalResource.GetRootPath | CodePackageActivationContext.Log/Temp/Work | * |
| Role.GetInstances | FabricClient.QueryManager.GetNodeList or ResolveService | * |
| RoleInstanceEndpoint.GetIPEndpoint | FabricRuntime.GetActivationContext 或命名 (ResolveService) | * |

## <a name="next-steps"></a>后续步骤
从云服务迁移到 Service Fabric 的最简单路径是只将云服务部署替换为 Service Fabric 应用程序，并将应用程序的整个基础结构保持大致相同。 以下文章提供了帮助将 Web 角色或辅助角色转换为 Service Fabric 无状态服务的指南。

* [Simple migration: convert a Web or Worker Role to a Service Fabric stateless service](service-fabric-cloud-services-migration-worker-role-stateless-service.md)（简单迁移：将 Web 角色或辅助角色转换为 Service Fabric 无状态服务）

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
