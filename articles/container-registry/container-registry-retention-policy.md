---
title: 用于在 Azure 容器注册表中保留未标记清单的策略
description: 了解如何启用 Azure 容器注册表中的保留策略，以在定义的时间段后自动删除未标记清单。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.openlocfilehash: 36d27bc6089bbe3f4ada6862a9c1be1fa0bdbae7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305998"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>设置未标记清单的保留策略

Azure 容器注册表使你可以选择为不包含任何关联标记（未标记*清单*）的已存储映像清单设置*保留策略*。 启用保留策略后，会在你设置的天数后自动删除注册表中的未标记清单。 此功能可防止注册表填满不需要的项目，并有助于节省存储成本。 如果未标记的清单的`false`属性设置为，则无法删除清单，且不应用保留策略。`delete-enabled`

您可以使用 Azure CLI 的 Azure Cloud Shell 或本地安装运行本文中的命令示例。 如果要在本地使用，则需要版本2.0.74 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

> [!WARNING]
> 设置保留策略--删除的映像数据不可恢复。 如果你的系统按清单摘要（而不是映像名称）提取映像，则不应为未标记的清单设置保留策略。 删除无标的记映像后，这些系统即无法从注册表拉取映像。 不按清单拉取，而是考虑采用[建议的最佳做法](container-registry-image-tag-version.md)，即“唯一标记”方案。

如果要使用 Azure CLI 命令删除单个映像标记或清单，请参阅[在 Azure 容器注册表中删除容器映像](container-registry-delete.md)。

## <a name="preview-limitations"></a>预览版限制

* 只有**高级**容器注册表才能使用保留策略进行配置。 有关注册表服务层的信息, 请参阅[Azure 容器注册表 sku](container-registry-skus.md)。
* 只能为未标记的清单设置保留策略。

## <a name="set-a-retention-policy---cli"></a>设置保留策略-CLI

下面的示例演示如何使用 Azure CLI 为注册表中未标记的清单设置保留策略。

### <a name="enable-a-retention-policy"></a>启用保留策略

默认情况下，容器注册表中未设置保留策略。 若要设置或更新保留策略，请在 Azure CLI 中运行[az acr config 留成 update][az-acr-config-retention-update]命令。 可以指定介于0到365之间的天数，以保留未标记清单。 如果未指定天数，则该命令会将默认值设置为7天。 保持期结束后，注册表中所有未标记的清单将自动删除。

以下示例在注册表*myregistry*中为未标记的清单设置30天的保留策略：

```azurecli
az acr config retention update --name myregistry --status enabled --days 30 --type UntaggedManifests
```

下面的示例将策略设置为在无标记后立即删除注册表中的所有清单。 通过设置保留期0天来创建此策略：

```azurecli
az acr config retention update --name myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="disable-a-retention-policy"></a>禁用保留策略

若要查看注册表中设置的保留策略，请运行[az acr config 保持显示][az-acr-config-retention-show]命令：

```azurecli
az acr config retention show --name myregistry
```

若要在注册表中禁用保留策略，请运行[az acr config 留成 update][az-acr-config-retention-update]命令并设置`--status disabled`：

```azurecli
az acr config retention update --name myregistry --status disabled
```

## <a name="set-a-retention-policy---portal"></a>设置保留策略-门户

你还可以在[Azure 门户](https://portal.azure.com)中设置注册表的保留策略。 下面的示例演示如何使用门户为注册表中的未标记清单设置保留策略。

### <a name="enable-a-retention-policy"></a>启用保留策略

1. 导航到 Azure 容器注册表。 在 "**策略**" 下，选择 "**保留**（预览版）"。
1. 在 "**状态**" 中，选择 "**已启用**"。
1. 选择0到365之间的天数，以保留未标记清单。 选择“保存”。

![启用 Azure 门户的保留策略](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>禁用保留策略

1. 导航到 Azure 容器注册表。 在 "**策略**" 下，选择 "**保留**（预览版）"。
1. 在 "**状态**" 中，选择 "**禁用**"。 选择“保存”。

## <a name="next-steps"></a>后续步骤

* 了解有关删除 Azure 容器注册表中的[映像和存储库](container-registry-delete.md)的选项的详细信息

* 了解如何从注册表[自动清除](container-registry-auto-purge.md)所选的映像和清单

* 了解有关在注册表中[锁定图像和清单](container-registry-image-lock.md)的选项的详细信息

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
