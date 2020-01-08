---
title: Jenkins 和 Azure 概述
description: 在 Azure 中托管 Jenkins 生成和部署自动化服务器，并使用 Azure 计算和存储资源来扩展持续集成及部署 (CI/CD) 管道。
ms.service: jenkins
keywords: jenkins, azure, devops, 概述
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: overview
ms.date: 07/25/2018
ms.openlocfilehash: 86d32726280cce12888f125c65254a7b02166704
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "60641239"
---
# <a name="azure-and-jenkins"></a>Azure 和 Jenkins

[Jenkins](https://jenkins.io/) 是一个受欢迎的开源自动化服务器，用于设置软件项目的持续集成和交付 (CI/CD)。 可以使用 Azure 资源在 Azure 中托管 Jenkins 部署或扩展现有的 Jenkins 配置。 此外，Jenkins 插件还可用来简化应用程序在 Azure 中的 CI/CD 过程。

本文将介绍如何将 Azure 用于 Jenkins，详细说明可供 Jenkins 用户使用的核心 Azure 功能。 若要详细了解如何在 Azure 中完成自己的 Jenkins 服务器的入门，请参阅[在 Azure 上创建 Jenkins 服务器](install-jenkins-solution-template.md)。

## <a name="host-your-jenkins-servers-in-azure"></a>在 Azure 中托管 Jenkins 服务器

在 Azure 中托管 Jenkins，以集中执行生成自动化，并根据软件项目增长的需要来扩展部署。 可以使用以下项在 Azure 中部署 Jenkins：
 
- Azure 市场中的 [Jenkins 解决方案模板](install-jenkins-solution-template.md)。
- [Azure 虚拟机](/azure/virtual-machines/linux/overview)。 请参阅我们的[教程](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd)，在 VM 上创建 Jenkins 实例。
- 有关在 [Azure 容器服务](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)中运行的 Kubernetes 群集，请参阅我们的[操作指南](/azure/container-service/kubernetes/container-service-kubernetes-jenkins)。

使用 [Azure Monitor 日志](/azure/log-analytics/log-analytics-overview)和 [Azure CLI](/cli/azure) 来监视和管理 Azure Jenkins 部署。

## <a name="scale-your-build-automation-on-demand"></a>按需扩展生成自动化

将生成代理添加到现有 Jenkins 部署来扩展 Jenkins 生成能力，因为作业和管道的生成数量及复杂性都在增加。 你可以通过使用 [Azure VM 代理插件](jenkins-azure-vm-agents.md)在 Azure 虚拟机上运行这些生成代理。 请参阅我们的[教程](/azure/jenkins/jenkins-azure-vm-agents)，了解详细信息。

配置 [Azure 服务主体](/azure/azure-resource-manager/resource-group-overview)后，Jenkins 作业和管道可以使用此凭据执行以下操作：

- 使用 [Azure 存储插件](https://plugins.jenkins.io/windows-azure-storage)在 [Azure 存储](/azure/storage/common/storage-introduction)中安全存储和存档生成项目。 查看 [Jenkins 存储操作方法](/azure/storage/common/storage-java-jenkins-continuous-integration-solution)了解详细信息。
- 使用 [Azure CLI](/azure/jenkins/execute-cli-jenkins-pipeline) 管理和配置 Azure 资源。

## <a name="deploy-your-code-into-azure-services"></a>将代码部署到 Azure 服务

使用 Jenkins 插件将应用程序作为 Jenkins CI/CD 管道的一部分部署到 Azure。 通过部署到 [Azure 应用服务](/azure/app-service/)和 [Azure 容器服务](/azure/container-service/kubernetes/)，可以暂存和测试更新，并将其发布到应用程序，而无需管理基础结构。

 插件可用于部署到以下服务和环境：

- [Linux 上的 Azure 应用服务](/azure/app-service/containers/app-service-linux-intro)。 请参阅[教程](java-deploy-webapp-tutorial.md)以开始使用。
- [Azure 应用服务](/azure/app-service/overview)。 请参阅[操作指南](deploy-Jenkins-app-service-plugin.md)以开始使用。