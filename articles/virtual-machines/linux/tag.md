---
title: 如何标记 Azure Linux 虚拟机 | Microsoft Docs
description: 了解如何标记使用 Resource Manager 部署模型在 Azure 中创建的 Azure Linux 虚拟机。
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: c232fc80ea63cd2e1d37bc380fb09c512bb7a517
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081910"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>如何在 Azure 中标记 Linux 虚拟机
本文介绍在 Azure 中通过 Resource Manager 部署模型标记 Linux 虚拟机的不同方式。 标记是用户定义的键/值对，可直接放置在资源或资源组中。 针对每个资源和资源组，Azure 当前支持最多 15 个标记。 标记可以在创建时放置在资源中或添加到现有资源中。 请注意，只有通过 Resource Manager 部署模型创建的资源支持标记。

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>使用 Azure CLI 进行标记

若要开始，需要安装最新的 [Azure CLI](/cli/azure/install-azure-cli) 并已使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure 帐户。

可以使用此命令查看给定虚拟机的所有属性，包括标记：

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

若要通过 Azure CLI 添加新的 VM 标记，可以使用 `azure vm update` 命令以及标记参数 **--set**：

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

若要删除标记，可以在 `azure vm update` 命令中使用 **--remove** 参数。

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

既然我们已通过 Azure CLI 和门户将标记应用到资源中，那就让我们看一看使用情况详细信息，以在计费门户中查看标记。

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何标记 Azure 资源，请参阅 [Azure 资源管理器概述][Azure Resource Manager Overview]和 [使用标记来组织 Azure 资源][Using Tags to organize your Azure Resources]。
* 要了解标记如何帮助你管理 Azure 资源的使用，请参阅 [Understanding your Azure Bill][Understanding your Azure Bill]（了解 Azure 帐单）和 [Gain insights into your Microsoft Azure resource consumption][Gain insights into your Microsoft Azure resource consumption]（深入了解 Microsoft Azure 资源消耗）。

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../../billing/billing-usage-rate-card-overview.md
