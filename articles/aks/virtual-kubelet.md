---
title: 在 Azure Kubernetes 服务 (AKS) 群集中运行虚拟 kubelet
description: 了解如何结合使用虚拟 Kubelet 和 Azure Kubernetes 服务 (AKS) 以在 Azure 容器实例上运行 Linux 和 Windows 容器。
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613858"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>结合使用虚拟 Kubelet 和 Azure Kubernetes 服务 (AKS)

Azure 容器实例 (ACI) 提供托管环境，以便在 Azure 中运行容器。 使用 ACI 时，无需管理基础计算基础结构，Azure 会处理此管理。 在 ACI 中运行容器时，每个正在运行的容器将按秒收费。

在 Azure 容器实例中使用虚拟 Kubelet 提供程序时，可以在容器实例上安排 Linux 和 Windows 容器，就像容器实例是一个标准的 Kubernetes 节点一样。 此配置允许你利用 Kubernetes 的功能以及容器实例的管理价值和成本优势。

> [!NOTE]
> AKS 现在对 ACI 上的计划容器（称为“虚拟节点”）提供内置支持。 目前虚拟节点支持 Linux 容器实例。 如果需要计划 Windows 容器实例，可以继续使用虚拟 Kubelet。 否则，应使用虚拟节点，而不是本文中所述的手动虚拟 Kubelet 说明。 你可以使用[Azure CLI][virtual-nodes-cli]或[Azure 门户][virtual-nodes-portal]开始使用虚拟节点。
>
> 虚拟 Kubelet 是实验性开放源代码项目，并且应该这样使用。 若要参与问题讨论、提交问题以及阅读有关虚拟 kubelet 的详细信息，请参阅[虚拟 Kubelet GitHub 项目][vk-github]。

## <a name="before-you-begin"></a>开始之前

本文档假定你有 AKS 群集。 如果需要 AKS 群集，请参阅 [Azure Kubernetes 服务 (AKS) 快速入门][aks-quick-start]。

还需要 Azure CLI 版本**2.0.65**或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

若要安装 Virtual Kubelet, 请在 AKS 群集中安装并配置[Helm][aks-helm] 。 如果需要, 请确保将 Tiller[配置为与 KUBERNETES RBAC 一起使用](#for-rbac-enabled-clusters)。

### <a name="register-container-instances-feature-provider"></a>注册容器实例功能提供程序

如果你之前未使用过 Azure 容器实例 (ACI) 服务, 请将服务提供程序注册到你的订阅。 你可以使用[az provider list][az-provider-list]命令检查 ACI 提供程序注册的状态, 如以下示例中所示:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Microsoft.ContainerInstance 提供程序应报告为“已注册”，如下面的示例输出所示：

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

如果提供程序显示为*NotRegistered*, 请使用[az provider register][az-provider-register]注册提供程序, 如以下示例中所示:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>对于启用了 RBAC 的群集

如果 AKS 群集已启用 RBAC，则必须创建服务帐户和角色绑定以便与 Tiller 一起使用。 有关详细信息，请参阅 [Helm 基于角色的访问控制][helm-rbac]。 要创建服务帐户和角色绑定，请创建名为 rbac-virtual-kubelet.yaml 的文件并粘贴以下定义：

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

应用服务帐户并使用 [kubectl apply][kubectl-apply] 绑定，然后指定 rbac-virtual-kubelet.yaml 文件，如下例所示：

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

配置 Helm 以使用 Tiller 服务帐户：

```console
helm init --service-account tiller
```

现在可以继续将虚拟 Kubelet 安装到 AKS 群集。

## <a name="installation"></a>安装

使用 [az aks install-connector][aks-install-connector] 命令安装虚拟 Kubelet。 以下示例将部署 Linux 和 Windows 连接器。

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

此参数可用于[az aks 安装连接器][aks-install-connector]命令。

| 参数： | 描述 | 必填 |
|---|---|:---:|
| `--connector-name` | ACI 连接器的名称。| 是 |
| `--name` `-n` | 托管群集的名称。 | 是 |
| `--resource-group` `-g` | 资源组的名称。 | 是 |
| `--os-type` | 容器实例操作系统类型。 允许的值：Linux、Windows、这两者。 默认：Linux。 | 否 |
| `--aci-resource-group` | 要在其中创建 ACI 容器组的资源组。 | 否 |
| `--location` `-l` | 要创建 ACI 容器组的位置。 | 否 |
| `--service-principal` | 用于对 Azure API 进行身份验证的服务主体。 | 否 |
| `--client-secret` | 与服务主体关联的机密。 | 否 |
| `--chart-url` | 安装 ACI 连接器的 Helm 图表的 URL。 | 否 |
| `--image-tag` | 虚拟 kubelet 容器映像的映像标记。 | 否 |

## <a name="validate-virtual-kubelet"></a>验证虚拟 Kubelet

若要验证是否已安装虚拟 Kubelet, 请使用[kubectl get 节点][kubectl-get]命令返回 Kubernetes 节点的列表:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>运行 Linux 容器

创建名为 `virtual-kubelet-linux.yaml` 的文件，并将其复制到以下 YAML 中。 请注意，正在使用 [nodeSelector][node-selector] 和 [toleration][toleration] 来计划节点上的容器。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

使用 [kubectl create][kubectl-create] 命令运行该应用程序。

```console
kubectl create -f virtual-kubelet-linux.yaml
```

使用带有 `-o wide` 参数的 [kubectl get pods][kubectl-get] 命令输出具有计划节点的 pod 列表。 请注意，已在 `virtual-kubelet-virtual-kubelet-linux` 节点上计划 `aci-helloworld` pod。

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>运行 Windows 容器

创建名为 `virtual-kubelet-windows.yaml` 的文件，并将其复制到以下 YAML 中。 请注意，正在使用 [nodeSelector][node-selector] 和 [toleration][toleration] 来计划节点上的容器。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

使用 [kubectl create][kubectl-create] 命令运行该应用程序。

```console
kubectl create -f virtual-kubelet-windows.yaml
```

使用带有 `-o wide` 参数的 [kubectl get pods][kubectl-get] 命令输出具有计划节点的 pod 列表。 请注意，已在 `virtual-kubelet-virtual-kubelet-windows` 节点上计划 `nanoserver-iis` pod。

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>删除虚拟 Kubelet

使用 [az aks remove-connector][aks-remove-connector] 命令删除虚拟 Kubelet。 将参数值替换为连接器、AKS 群集和 AKS 群集资源组的名称。

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> 如果在删除两个 OS 连接器时遇到错误，或者只想删除 Windows 或 Linux OS 连接器，则可以手动指定 OS 类型。 将 `--os-type` 参数添加到上一个 `az aks remove-connector` 命令，并指定 `Windows` 或 `Linux`。

## <a name="next-steps"></a>后续步骤

有关虚拟 Kubelet 可能出现的问题，请参阅[已知问题和解决方法][vk-troubleshooting]。 若要报告虚拟 Kubelet 出现的问题，请[打开 GitHub 问题][vk-issues]。

有关虚拟 Kubelet 的详细信息，请参阅[虚拟 Kubelet GitHub 项目][vk-github]。

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
