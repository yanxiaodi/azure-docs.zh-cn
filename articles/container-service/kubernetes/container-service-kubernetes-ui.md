---
title: （已弃用）使用 Web UI 管理 Azure Kubernetes 群集
description: 在 Azure 容器服务中使用 Kubernetes Web UI
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: c3a79b2e4fab807613a54d2792f5f5b97570293b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60309606"
---
# <a name="deprecated-using-the-kubernetes-web-ui-with-azure-container-service"></a>（已弃用）在 Azure 容器服务中使用 Kubernetes Web UI

> [!TIP]
> 有关本文中使用 Azure Kubernetes 服务的更新版本，请参阅[访问 Azure Kubernetes 服务 (AKS) 中的 Kubernetes Web 仪表板](../../aks/kubernetes-dashboard.md)。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>必备组件
本演练假定用户已[使用 Azure 容器服务创建 Kubernetes 群集](container-service-kubernetes-walkthrough.md)，


并假设已安装 Azure CLI 和 `kubectl` 工具。

可以通过运行以下语句测试是否已安装 `az` 工具：

```console
$ az --version
```

如果尚未安装 `az` 工具，请参阅[此处](https://github.com/azure/azure-cli#installation)的说明。

可以通过运行以下语句测试是否已安装 `kubectl` 工具：

```console
$ kubectl version
```

如果尚未安装 `kubectl`，则可运行：

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>概述

### <a name="connect-to-the-web-ui"></a>连接到 Web UI
可以通过运行以下语句启动 Kubernetes Web UI：

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

此时会打开一个 Web 浏览器，该浏览器已配置为与安全代理通信，将本地计算机连接到 Kubernetes Web UI。

### <a name="create-and-expose-a-service"></a>创建和公开服务
1. 在 Kubernetes Web UI 中，单击右上方窗口中的“创建”按钮。 

    ![Kubernetes“创建”UI](./media/container-service-kubernetes-ui/create.png)

    此时会打开一个对话框，用户可以开始在其中创建应用程序。

2. 将其命名为 `hello-nginx`。 使用[`nginx` Docker 中的容器](https://hub.docker.com/_/nginx/)，部署此 Web 服务的三个副本。

    ![Kubernetes Pod“创建”对话框](./media/container-service-kubernetes-ui/nginx.png)

3. 在“服务”下面，选择“外部”并输入端口 80。  

    此设置将流量负载均衡到三个副本。

    ![Kubernetes 服务“创建”对话框](./media/container-service-kubernetes-ui/service.png)

4. 单击“部署”  以部署这些容器和服务。

    ![Kubernetes 部署](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>查看容器
单击“部署”后，UI 会显示部署的服务的视图： 

![Kubernetes 状态](./media/container-service-kubernetes-ui/status.png)

可以在 UI 的“Pod”下面左侧圆圈中查看每个 Kubernetes 对象的状态。  如果该圆圈不完整，则对象仍然处于部署状态。 对象在完全部署以后会显示绿色的复选标记：

![Kubernetes 已部署](./media/container-service-kubernetes-ui/deployed.png)

所有项都运行以后，单击某个 Pod，可查看正在运行的 Web 服务的详细信息。

![Kubernetes Pod](./media/container-service-kubernetes-ui/pods.png)

在“Pod”  视图中，用户可以查看 Pod 中容器以及这些容器所使用的 CPU 和内存资源的相关信息：

![Kubernetes 资源](./media/container-service-kubernetes-ui/resources.png)

如果看不到资源，则可能需要等待几分钟时间，以便监视数据完成传播。

若要查看容器的日志，请单击“查看日志”  。

![Kubernetes 日志](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>查看服务
除了运行容器，Kubernetes UI 还创建外部 `Service`，用于预配负载均衡器，以便将流量带到群集中的容器。

在左侧的导航窗格中，单击“服务”  以查看所有服务（应该只有一个）。

![Kubernetes 服务](./media/container-service-kubernetes-ui/service-deployed.png)

在该视图中，可以看到分配给服务的外部终结点（IP 地址）。
如果单击该 IP 地址，可看到在负载均衡器后面运行的 Nginx 容器。

![nginx 视图](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>调整服务大小
除了在 UI 中查看对象，还可以编辑和更新 Kubernetes API 对象。

首先，单击左侧导航窗格中的“部署”以查看针对服务的部署。 

进入该视图后，单击副本集，并单击上方导航栏中的“编辑”： 

![Kubernetes 编辑](./media/container-service-kubernetes-ui/edit.png)

将 `spec.replicas` 字段设为 `2`，并单击“更新”  。

这样会删除一个 Pod，导致副本数降到 2。

 

