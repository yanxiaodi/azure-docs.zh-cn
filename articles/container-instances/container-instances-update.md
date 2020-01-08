---
title: 更新 Azure 容器实例中的容器
description: 了解如何更新 Azure 容器实例容器组中正在运行的容器。
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 09/03/2019
ms.author: danlep
ms.openlocfilehash: 3103fe7fbf7dcd587f43b673ef53f32893908ecb
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307715"
---
# <a name="update-containers-in-azure-container-instances"></a>更新 Azure 容器实例中的容器

在容器实例的正常操作期间，你可能会发现需要更新[容器组](container-instances-container-groups.md)中正在运行的容器。 例如，你可能想要更新映像版本、更改 DNS 名称、更新环境变量，或刷新其应用程序已崩溃的容器的状态。

> [!NOTE]
> 已终止或已删除的容器组无法更新。 一旦容器组终止（处于成功或失败状态）或已被删除，该组必须部署为新组。

## <a name="update-a-container-group"></a>更新容器组

通过重新部署包含至少一个已修改属性的现有组来更新正在运行的容器组中的容器。 更新容器组时，组中的所有正在运行的容器（通常在同一基础容器主机上）都将重新启动。

通过发出 create 命令（或使用 Azure 门户）并指定现有组的名称，来重新部署现有的容器组。 发出 create 命令以触发重新部署时，至少修改组的一个有效属性，并使其余的属性保持不变（或继续使用默认值）。 并非所有容器组属性都可用于重新部署。 有关不支持的属性列表，请参阅[需要删除操作的属性](#properties-that-require-container-delete)。

以下 Azure CLI 示例更新具有新 DNS 名称标签的容器组。 因为组的 DNS 名称标签属性是可以更新的，所以会重新部署容器组，并重新启动其容器。

具有 DNS 名称标签 *myapplication-staging* 的初始部署：

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

使用新的 DNS 名称标签*myapplication*更新容器组，并保持其余属性不变：

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>更新的好处

更新现有容器组的主要好处是加快部署速度。 重新部署现有容器组时，会从前一部署缓存的配置中提取该组的容器映像层。 此过程不会像新部署中一样从注册表中提取所有全新的映像层，而只会提取修改的层（如果有）。

如果更新容器组而不是删除再部署新的容器组，则基于大型容器映像的应用程序（例如 Windows Server Core）的部署速度会有明显的改善。

## <a name="limitations"></a>限制

并非容器组的所有属性都支持更新。 若要更改某个容器组的某些属性，必须先删除，再重新部署该组。 有关详细信息，请参阅[需要容器删除操作的属性](#properties-that-require-container-delete)。

更新某个容器组时，该容器组中的所有容器会重启。 无法对多容器组中的特定容器执行更新或就地重启。

每次更新后，容器的 IP 地址通常不会更改，但也不能保证该地址保持不变。 只要将容器组部署到相同的基础主机，容器组就会保留其 IP 地址。 尽管 Azure 容器实例会尽量重新部署到同一主机，但某些 Azure 内部事件可能导致重新部署到不同的主机（这种情况很少见）。 为了避免此问题，请始终对容器实例使用 DNS 名称标签。

已终止或已删除的容器组无法更新。 停止（处于“已终止”状态）或删除某个容器组后，将全新部署该组。

## <a name="properties-that-require-container-delete"></a>需要容器删除操作的属性

如前所述，并非所有容器组属性都可更新。 例如，若要更改端口或重启容器的策略，必须先删除容器组，然后重新创建。

以下属性在重新部署之前需要执行容器组删除操作：

* OS 类型
* CPU
* 内存
* 重新启动策略
* 端口

删除再重新创建某个容器组时，不是“重新部署”该组，而是新建一个容器组。 将从注册表中全新提取所有映像层，而不是从前一个部署缓存的配置中提取。 由于部署到不同的基础主机，容器的 IP 地址也可能会更改。

## <a name="next-steps"></a>后续步骤

本文中多次提到了**容器组**。 Azure 容器实例中的每个容器部署在容器组中，容器组可以包含多个容器。

[Azure 容器实例中的容器组](container-instances-container-groups.md)

[部署多容器组](container-instances-multi-container-group.md)

[手动停止或启动 Azure 容器实例中的容器](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
