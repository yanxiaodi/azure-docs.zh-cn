---
title: （已弃用）用于 Kubernetes 的 Azure 容器服务简介
description: 有了用于 Kubernetes 的 Azure 容器服务，即可在 Azure 上轻松部署和管理基于容器的应用程序。
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: e00ac57cc36b3331cfb847ecedc6c75132cdeb6b
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999174"
---
# <a name="deprecated-introduction-to-azure-container-service-for-kubernetes"></a>（已弃用）用于 Kubernetes 的 Azure 容器服务简介

> [!TIP]
> 有关本文中使用 Azure Kubernetes 服务的更新版本，请参阅 [Azure Kubernetes 服务 (AKS) 概览](../../aks/intro-kubernetes.md)。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

有了用于 Kubernetes 的 Azure 容器服务，即可轻松创建、配置和管理虚拟机群集，这些虚拟机已经过预配置，可以运行容器化应用程序。 通过此服务，用户可使用现有技能或利用不断增加的大量社区专业知识，在 Microsoft Azure 上部署和管理基于容器的应用程序。

使用 Azure 容器服务时，可利用 Azure 的企业级功能，并且仍可通过 Kubernetes 以及 Docker 映像格式保留应用程序的可移植性。

## <a name="using-azure-container-service-for-kubernetes"></a>使用用于 Kubernetes 的 Azure 容器服务
Azure 容器服务旨在通过使用当今客户中热门的开源工具和技术提供容器托管环境。 为此，我们公开标准 Kubernetes API 终结点。 通过使用这些标准终结点，可利用能够与 Kubernetes 群集通信的任何软件。 例如，可以选择 [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)、[helm](https://helm.sh/) 或 [draft](https://github.com/Azure/draft)。

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>使用 Azure 容器服务创建 Kubernetes 群集
若要开始使用 Azure 容器服务，可通过 [Azure CLI](container-service-kubernetes-walkthrough.md) 或门户（在市场中搜索“Azure 容器服务”）部署 Azure 容器服务群集。 如果你是需要对 Azure 资源管理器模板进行更多控制的高级用户，可以使用开源的 [acs-engine](https://github.com/Azure/acs-engine) 项目来生成自己的自定义 Kubernetes 群集，然后通过 `az` CLI 进行部署。

### <a name="using-kubernetes"></a>使用 Kubernetes
Kubernetes 对容器化应用程序自动进行部署、扩展和管理。 它具有一组丰富的功能，包括：
* 自动装箱
* 自我修复
* 水平扩展
* 服务发现和负载均衡
* 自动推出和回退
* 机密和配置管理
* 存储业务流程
* Batch 执行

通过 Azure 容器服务部署的 Kubernetes 的体系结构图：

![配置为使用 Kubernetes 的 Azure 容器服务。](media/acs-intro/kubernetes.png)

## <a name="videos"></a>视频

Azure 容器服务中的 Kubernetes 支持（Azure Friday，2017 年 1 月）：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

用于在 Kubernetes 上开发和部署应用程序的工具（Azure OpenDev，2017 年 6 月）：

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>后续步骤

浏览 [Kubernetes 快速入门](container-service-kubernetes-walkthrough.md)，现在就开始了解 Azure 容器服务。
