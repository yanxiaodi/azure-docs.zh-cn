---
title: Azure 容器实例和容器业务流程
description: 了解 Azure 容器实例如何与容器业务流程协调程序交互。
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 044b74e1a8683c6beb0220c1cf9fb97403286a95
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972250"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure 容器实例和容器协调器

由于规模较小并面向应用程序，容器非常适合敏捷交付环境和基于微服务的体系结构。 自动化和管理大量容器及其交互方式的任务称为*协调*。 流行的容器业务流程协调程序包括 Kubernetes、DC/OS 和 Docker Swarm。

Azure 容器实例提供了业务流程平台的一些基本调度功能。 虽然它没有涵盖那些平台提供的更高价值的服务，但 Azure 容器实例可以作为它们的补充。 本文介绍 Azure 容器实例的处理范围，以及整套容器协调器如何与它交互。

## <a name="traditional-orchestration"></a>传统的协调

协调的标准定义包括以下任务：

- **调度**：在给定容器映像和资源请求的情况下，找到适合运行容器的计算机。
- **关联/反关联**：指定一组应该彼此靠近（为提高可用性）或保持足够距离（为提高性能）运行的容器。
- **运行状况监视**：监视容器故障并自动重新调度容器。
- **故障转移**：跟踪每台计算机上运行的组件，并将容器从有故障的计算机重新调度到正常的节点。
- **缩放**：根据需要手动或自动添加或删除容器实例。
- **网络**：提供覆盖网络用于协调容器，以便跨多台主机通信。
- **服务发现**：使容器能够自动找到对方，即便它们在主机之间移动并更改了 IP 地址。
- **协调的应用程序升级**：管理容器升级，避免应用程序关闭，并在出错的情况下回滚。

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Azure 容器实例的协调：分层方法

Azure 容器实例支持分层的协调方法，提供全部所需的调度和管理功能来运行单个容器，同时允许协调器平台管理容器实例顶层的多容器任务。

由于容器实例的底层基础结构由 Azure 管理，因此容器协调器平台不需要考虑如何查找用于运行单个容器的适当主机。 云的弹性确保始终有一台可用的主机。 协调器可将重心放在简化多容器体系结构的开发的任务上，包括缩放和协调的升级。

## <a name="scenarios"></a>方案

尽管协调器与 Azure 容器实例的集成仍处于初步阶段，但我们可以预见到将来会出现一些不同的环境：

### <a name="orchestration-of-container-instances-exclusively"></a>以独占方式协调容器实例

由于 Azure 容器实例可快速启动并按秒计费，专门以容器实例为基础的环境能够以最快的速度启动，并处理较大差异的工作负荷。

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>在虚拟机中合并容器实例和容器

对于长时间运行的稳定工作负荷，协调专用虚拟机群集中的容器通常比使用 Azure 容器实例运行相同的容器更节省。 但是，容器实例提供理想的解决方案来快速扩展和缩减整体容量，以处理意外的或短期高发的使用量。

业务流程协调程序不必先增加群集中的虚拟机数目，然后将更多的容器部署到这些计算机，而可以直接在 Azure 容器实例中调度更多的容器，并删除不再需要的容器。

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>示例实现：Azure Kubernetes 服务 (AKS) 的虚拟节点

可以使用在 Azure 容器实例中动态创建的虚拟节点，来快速缩放 [Azure Kubernetes 服务](../aks/intro-kubernetes.md) (AKS) 群集中的应用程序工作负载。 ACI 和 AKS 群集中运行的 Pod 可以借助虚拟节点进行网络通信。 

目前虚拟节点支持 Linux 容器实例。 可通过 [Azure CLI](https://go.microsoft.com/fwlink/?linkid=2047538) 或 [Azure门户](https://go.microsoft.com/fwlink/?linkid=2047545)开始使用虚拟节点。

虚拟节点使用开源[虚拟 Kubelet][aci-connector-k8s] ，通过将其注册为具有无限容量的节点来模拟 Kubernetes [Kubelet][kubelet-doc] 。 Virtual Kubelet 调度在 Azure 容器实例中将 pod[创建为容器][pod-doc]组。

请参阅[虚拟 Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) 项目，以获取更多将 Kubernetes API 扩展到无服务器容器平台的示例。

## <a name="next-steps"></a>后续步骤

使用[快速入门指南](container-instances-quickstart.md)创建第一个包含 Azure 容器实例的容器。

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
