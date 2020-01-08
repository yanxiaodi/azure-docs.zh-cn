---
title: 将容器实例部署到 Azure 虚拟网络
description: 了解如何将容器组部署到新的或现有的 Azure 虚拟网络。
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: ad7f93bb3934ca01b7f45c0bd4b5cc8be81ea54b
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325525"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>将容器实例部署到 Azure 虚拟网络

[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)为 azure 和本地资源提供安全的专用网络。 将容器组部署到 Azure 虚拟网络后，容器可与该虚拟网络中的其他资源安全通信。

部署到 Azure 虚拟网络的容器组可实现如下所述的方案：

* 在同一子网中的容器组之间直接通信
* 将容器实例的[基于任务](container-instances-restart-policy.md)的工作负荷输出发送到虚拟网络中的数据库
* 从虚拟网络中的[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)检索容器实例的内容
* 容器与虚拟网络中的虚拟机通信
* 容器通过 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)或 [ExpressRoute](../expressroute/expressroute-introduction.md) 与本地资源通信

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="virtual-network-deployment-limitations"></a>虚拟网络部署限制

将容器组部署到虚拟网络时存在某些限制。

* 若要将容器组部署到某个子网，该子网不能包含其他任何资源类型。 将容器组部署到现有子网之前，请从该子网中删除所有现有资源，或创建新的子网。
* 不能在部署到虚拟网络的容器组中使用[托管标识](container-instances-managed-identity.md)。
* 由于涉及到其他网络资源，将容器组部署到虚拟网络通常是比部署标准容器实例要慢一些。

## <a name="preview-limitations"></a>预览版限制

尽管此功能处于预览阶段, 但在将容器组部署到虚拟网络时有以下限制。 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

容器资源限制可能与这些区域中非网络容器实例的限制不同。 目前，仅 Linux 容器支持此功能。 计划提供 Windows 支持。

### <a name="unsupported-networking-scenarios"></a>不支持的网络方案 

* **Azure 负载均衡器**-不支持在网络容器组中的容器实例前面放置 Azure 负载均衡器
* **虚拟网络对等互连**-不能将包含委派给 Azure 容器实例的子网的虚拟网络对等互连到另一个虚拟网络
* **路由表**-无法在委托给 Azure 容器实例的子网中设置用户定义的路由
* **网络安全组**-应用于委托给 Azure 容器实例的子网的 nsg 中的出站安全规则当前未强制执行 
* **公共 ip 或 DNS 标签**-部署到虚拟网络的容器组目前不支持使用公共 IP 地址或完全限定的域名直接向 internet 公开容器
* **内部名称解析**-不支持通过内部 Azure DNS 对虚拟网络中的 Azure 资源进行名称解析

将容器组部署到虚拟网络后，**删除网络资源**需要执行[额外的步骤](#delete-network-resources)。

## <a name="required-network-resources"></a>所需的网络资源

将容器组部署到虚拟网络需要三个 Azure 虚拟网络资源：[虚拟网络](#virtual-network)本身、虚拟网络中[委托的子网](#subnet-delegated)，以及[网络配置文件](#network-profile)。 

### <a name="virtual-network"></a>虚拟网络

虚拟网络定义要在其中创建一个或多个子网的地址空间。 然后，可将 Azure 资源（例如容器组）部署到虚拟网络中的子网。

### <a name="subnet-delegated"></a>子网（委托）

子网将部署资源的虚拟网络分段成可由 Azure 资源使用的独立地址空间。 在一个虚拟网络中创建一个或多个子网。

用于容器组的子网只能包含容器组。 首次将容器组部署到某个子网时，Azure 会将该子网委托给 Azure 容器实例。 委托后，该子网只可用于容器组。 如果尝试将除容器组以外的资源部署到委派的子网，该操作将会失败。

### <a name="network-profile"></a>网络配置文件

网络配置文件是 Azure 资源的网络配置模板。 它指定资源的某些网络属性，例如，该资源应部署到哪个子网。 首次使用[az container create][az-container-create]命令将容器组部署到子网 (以及虚拟网络) 时, Azure 会为你创建一个网络配置文件。 以后在部署到该子网时，可以使用该网络配置文件。 

若要使用资源管理器模板、YAML 文件或编程方法将容器组部署到子网，则需要提供网络配置文件的完整资源管理器资源 ID。 可以使用使用[az container create][az-container-create]创建的配置文件, 也可以使用资源管理器模板创建配置文件 (请参阅[模板示例](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet)和[参考](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles))。 若要获取以前创建的配置文件的 ID, 请使用[az network profile list][az-network-profile-list]命令。 

在下图中，有多个容器组已部署到委派给 Azure 容器实例的子网。 将一个容器组部署到某个子网后，可以通过指定相同的网络配置文件，将其他容器组部署到该子网。

![虚拟网络中的容器组][aci-vnet-01]

## <a name="deployment-scenarios"></a>部署方案

你可以使用[az container create][az-container-create]将容器组部署到新的虚拟网络, 并允许 Azure 为你创建所需的网络资源, 或将其部署到现有虚拟网络。 

### <a name="new-virtual-network"></a>新建虚拟网络

若要部署到新的虚拟网络并让 Azure 自动为你创建网络资源, 请在执行[az container create][az-container-create]时指定以下内容:

* 虚拟网络名称
* 采用 CIDR 格式的虚拟网络地址前缀
* 子网名称
* 采用 CIDR 格式的子网地址前缀

虚拟网络和子网地址前缀分别指定虚拟网络和子网的地址空间。 这些值以无类域间路由 (CIDR) 表示法表示，例如 `10.0.0.0/16`。 有关使用子网的详细信息，请参阅[添加、更改或删除虚拟网络子网](../virtual-network/virtual-network-manage-subnet.md)。

使用此方法部署第一个容器组后，可以通过指定虚拟网络和子网名称或者 Azure 自动创建的网络配置文件，来部署到同一子网。 由于 Azure 将该子网委托给了 Azure 容器实例，因此只能将容器组部署到该子网。 

### <a name="existing-virtual-network"></a>现有虚拟网络

将容器组部署到现有虚拟网络：

1. 在现有虚拟网络中创建一个子网，或者在现有子网中清空其他所有资源 
1. 使用[az container create][az-container-create]部署容器组, 并指定下列其中一项:
   * 虚拟网络名称和子网名称
   * 虚拟网络资源 ID 和子网资源 ID，它允许使用其他资源组中的虚拟网络
   * 网络配置文件名称或 ID, 可以使用[az 网络配置文件列表][az-network-profile-list]获取

将第一个容器组部署到现有子网后，Azure 会将该子网委托给 Azure 容器实例。 以后不再可以将除容器组以外的资源部署到该子网。

## <a name="deployment-examples"></a>部署示例

以下部分介绍如何使用 Azure CLI 将容器组部署到虚拟网络。 命令示例的格式适用于 **Bash** shell。 若要使用其他 shell（例如 PowerShell 或命令提示符），请相应地调整续行符。

### <a name="deploy-to-a-new-virtual-network"></a>部署到新的虚拟网络

首先，部署容器组并指定新虚拟网络和子网的参数。 指定这些参数时，Azure 会创建虚拟网络和子网、将子网委托给 Azure 容器实例，并创建网络配置文件。 创建这些资源后，容器组将部署到子网。

运行以下[az container create][az-container-create]命令, 指定新虚拟网络和子网的设置。 你需要提供在[支持](#preview-limitations)虚拟网络中的容器组的区域中创建的资源组的名称。 此命令部署运行小型 node.js web 服务的[helloworld][aci-helloworld]容器, 该容器为静态网页提供服务。 在下一部分，我们要将另一个容器组部署到同一子网，并测试这两个容器实例之间的通信。

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

使用此方法部署到新虚拟网络时，部署可能需要花费几分钟时间，因为在此期间需要创建网络资源。 完成初始部署后，后续的容器组部署可以更快地完成。

### <a name="deploy-to-existing-virtual-network"></a>部署到现有虚拟网络

将容器组部署到新虚拟网络后，请将另一个容器组部署到同一子网，并验证这两个容器实例之间的通信。

首先，获取部署的第一个容器组 *appcontainer* 的 IP 地址：

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

输出应会显示专用子网中容器组的 IP 地址：

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

现在，请将 `CONTAINER_GROUP_IP` 设置为使用 `az container show` 命令检索到的 IP，并执行以下 `az container create` 命令。 这第二个容器 *commchecker* 运行基于 Alpine Linux 的映像，并针对第一个容器组的专用子网 IP 地址执行 `wget`。

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

完成第二个容器的部署后，请提取其日志，以查看它执行的 `wget` 命令的输出：

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

如果第二个容器与第一个容器成功通信，则输出应类似于：

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

日志输出应显示 `wget` 可以在本地子网中使用第一个容器的专用 IP 地址连接到该容器，并可从中下载索引文件。 两个容器组之间的网络流量保留在虚拟网络中。

### <a name="deploy-to-existing-virtual-network---yaml"></a>部署到现有虚拟网络 - YAML

还可以使用 YAML 文件将容器组部署到现有虚拟网络。 若要部署到虚拟网络中的子网，请到 YAML 中指定几个附加的属性：

* `ipAddress`：容器组的 IP 地址设置。
  * `ports`：要打开的端口（如果有）。
  * `protocol`：打开的端口的协议（TCP 或 UDP）。
* `networkProfile`：指定网络设置，例如，Azure 资源的虚拟网络和子网。
  * `id`：`networkProfile` 的完整资源管理器资源 ID。

若要使用 YAML 文件将容器组部署到虚拟网络，首先需要获取网络配置文件的 ID。 执行[az network profile list][az-network-profile-list]命令, 并指定包含虚拟网络和委托子网的资源组的名称。

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

该命令的输出会显示网络配置文件的完整资源 ID：

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

获取网络配置文件 ID 后，将以下 YAML 复制到名为 *vnet-deploy-aci.yaml* 的新文件中。 在 `networkProfile` 下，将 `id` 值替换为刚刚检索到的 ID，然后保存文件。 此 YAML 会在虚拟网络中创建名为 *appcontaineryaml* 的容器组。

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

用[az container create][az-container-create]命令部署容器组, 并为`--file`参数指定 YAML 文件名:

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

完成部署后, 运行[az container show][az-container-show]命令以显示其状态:

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>清理资源

### <a name="delete-container-instances"></a>删除容器实例

处理完所创建的容器实例后，使用以下命令将其删除：

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>删除网络资源

在此功能的初始预览版中，需要额外执行几个命令才能删除前面创建的网络资源。 如果你在本文的前面几个部分中使用示例命令创建了虚拟网络和子网，则可以使用以下脚本来删除这些网络资源。

执行该脚本之前，请将 `RES_GROUP` 变量设置为包含所要删除的虚拟网络和子网的资源组的名称。 如果未使用之前建议的`aci-vnet`名称, 请更新虚拟网络的名称。 此脚本已针对 Bash Shell 格式化。 若要使用其他 shell（例如 PowerShell 或命令提示符），需要相应地调整变量赋值和访问器。

> [!WARNING]
> 此脚本会删除资源！ 它会删除虚拟网络及其包含的所有子网。 运行此脚本之前，请确认你不再需要该虚拟网络中的任何资源，包括其中的任何子网。  一旦删除，**这些资源就不可恢复**。

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>后续步骤

若要使用资源管理器模板部署新的虚拟网络、子网、网络配置文件和容器组，请参阅 [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)（使用 VNet 创建 Azure 容器组）。

本文简要介绍了多个虚拟网络资源和功能。 Azure 虚拟网络文档全面介绍了这些主题：

* [虚拟网络](../virtual-network/manage-virtual-network.md)
* [子网](../virtual-network/virtual-network-manage-subnet.md)
* [服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
