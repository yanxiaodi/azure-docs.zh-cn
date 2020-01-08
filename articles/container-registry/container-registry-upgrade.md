---
title: 升级经典 Azure 容器注册表
description: 通过升级非托管的经典容器注册表，利用基本、标准和高级托管容器注册表的扩展功能集。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 05c227e7de078c6bb371049f16e191598b9ca4e5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68310378"
---
# <a name="upgrade-a-classic-container-registry"></a>升级经典容器注册表

Azure 容器注册表 (ACR) 分为多个服务层级（[称为“SKU”](container-registry-skus.md)）。 ACR 的初始版本提供的单一 SKU（即经典 SKU）缺乏基本 SKU、标准 SKU 和高级 SKU（统称托管注册表）固有的多项功能。

经典 SKU 即将弃用，2019 年 4 月之后将不可用。 本文详述了如何将非托管的经典注册表迁移到某个托管的 SKU，以便利用其增强的功能集。

## <a name="why-upgrade"></a>为什么升级？

经典注册表 SKU 即将**弃用**，**2019 年 4 月**之后将不可用。 应在 2019 年 4 月之前升级所有现有的经典注册表。 经典注册表的门户管理功能将被淘汰。在 2019 年 4 月之后，用于创建新的经典注册表的功能将被禁用。

由于经典非托管注册表的计划弃用和有限功能，所有经典注册表应升级到托管注册表（基本、标准或高级）。 这些更高级 SKU 将注册表更深入地集成到 Azure 功能中。 有关不同服务层级的定价和功能的详细信息，请参阅[容器注册表 SKU](container-registry-skus.md)。

经典注册表依赖 Azure 在你创建注册表时在 Azure 订阅中自动预配的存储帐户。 相比之下，基本、标准和高级 SKU 通过以透明方式处理映像的存储来利用 Azure 的[高级存储功能](container-registry-storage.md)。 不在自己的订阅中创建单独的存储帐户。

托管注册表存储有以下优点：

* 容器映像[静态加密](container-registry-storage.md#encryption-at-rest)。
* 映像是使用[异地冗余存储](container-registry-storage.md#geo-redundant-storage)进行存储的, 可确保使用多区域复制来备份映像 (仅适用于高级 SKU)。
* 可以自由地[在 SKU 之间切换](container-registry-skus.md#changing-skus)，在选择更高级 SKU 时支持更大吞吐量。 对于每个 SKU，ACR 都可以用户需求增加时满足吞吐量需求。
* 注册表及其存储具有统一的安全模型，可以简化权限管理。 只需为容器注册表管理权限，不需为单独的存储帐户管理权限。

有关 ACR 中映像存储的其他详细信息，请参阅 [Azure 容器注册表中的容器映像存储](container-registry-storage.md)。

## <a name="migration-considerations"></a>迁移注意事项

将经典注册表升级到托管注册表时，Azure 必须将所有现有的容器映像从订阅中 ACR 创建的存储帐户复制到 Azure 托管的存储帐户中。 此过程可能需要数分钟到数小时，具体取决于注册表的大小。 迁移时间是根据每分钟大约迁移 0.5 GiB 估算的。

在转换过程中，迁移操作还剩 10% 时，将禁用 `docker push` 操作。 `docker pull` 仍可正常运行。

在转换过程中，请勿删除或修改充当经典注册表后备的存储帐户的内容， 否则可能导致容器映像损坏。

迁移完成后，Azure 容器注册表不再使用订阅中最初充当经典注册表后备的存储帐户。 验证迁移成功以后，考虑删除存储帐户，尽量降低成本。

>[!IMPORTANT]
> 从经典注册表升级到某个托管的 SKU 属于**单向过程**。 将经典注册表转换为基本、标准或高级注册表以后，不能还原为经典注册表。 但是，可以在不同的托管 SKU 之间自由转换，只要有足够的容量用于注册表。

## <a name="how-to-upgrade"></a>如何升级

可以通过多种方式将非托管的经典注册表升级为某个托管的 SKU。 在以下部分中, 我们将介绍使用[Azure CLI][azure-cli]和[Azure 门户][azure-portal]的过程。

## <a name="upgrade-in-azure-cli"></a>在 Azure CLI 中升级

若要在 Azure CLI 中升级经典注册表, 请执行[az acr update][az-acr-update]命令并为注册表指定新的 SKU。 在以下示例中，名为 *myclassicregistry* 的经典注册表将升级为高级 SKU：

```azurecli-interactive
az acr update --name myclassicregistry --sku Premium
```

迁移完成后，会看到如下所示的输出。 请注意，`sku` 为“高级”，`storageAccount` 为 `null`，表示 Azure 现在为此注册表管理映像存储。

```JSON
{
  "adminUserEnabled": false,
  "creationDate": "2017-12-12T21:23:29.300547+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "name": "Premium",
    "tier": "Premium"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

如果指定的托管注册表 SKU 的最大容量小于经典注册表的大小，则会收到如下所示的错误消息。

`Cannot update the registry SKU due to reason: Registry size 12936251113 bytes exceeds the quota value 10737418240 bytes for SKU Basic. The suggested SKU is Standard.`

如果收到类似错误, 请再次运行[az acr update][az-acr-update]命令并指定建议的 sku, 这是可容纳你的映像的下一个最高级别的 sku。

## <a name="upgrade-in-azure-portal"></a>在 Azure 门户中升级

使用 Azure 门户升级经典注册表时，Azure 会自动选择标准或高级 SKU，具体取决于哪个 SKU 可以容纳映像。 例如，如果注册表包含 100 GiB 以下的映像，Azure 会自动选择经典注册表并将其转换为标准注册表（最大容量为 100 GiB）。

若要使用 Azure 门户升级经典注册表，请导航到容器注册表的“概览”页，然后选择“升级到托管的注册表”。

![Azure 门户 UI 中的经典注册表升级按钮][update-classic-01-upgrade]

选择“确定”，确认要升级到托管的注册表。

在迁移过程中，门户会指示注册表的**预配状态**为“正在更新”。 如前所述，迁移操作还剩 10% 时，将禁用 `docker push` 操作。 迁移正在进行时，请不得删除或更新经典注册表使用的存储帐户，否则可能导致映像损坏。

![Azure 门户 UI 中的经典注册表升级进度][update-classic-03-updating]

迁移完成后，“预配状态”会指示“成功”，此时可以恢复注册表的正常操作。

![Azure 门户 UI 中的经典注册表升级完成状态][update-classic-04-updated]

## <a name="next-steps"></a>后续步骤

将经典注册表升级到托管注册表后，Azure 不再使用一开始充当经典注册表后备的存储帐户。 为了降低成本，可以考虑删除存储帐户或帐户中的 Blob 容器（其中包含旧的容器映像）。

<!-- IMAGES -->
[update-classic-01-upgrade]: ./media/container-registry-upgrade/update-classic-01-upgrade.png
[update-classic-02-confirm]: ./media/container-registry-upgrade/update-classic-02-confirm.png
[update-classic-03-updating]: ./media/container-registry-upgrade/update-classic-03-updating.png
[update-classic-04-updated]: ./media/container-registry-upgrade/update-classic-04-updated.png

<!-- LINKS - internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[azure-cli]: /cli/azure/install-azure-cli
[azure-portal]: https://portal.azure.com