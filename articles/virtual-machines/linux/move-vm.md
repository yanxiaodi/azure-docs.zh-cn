---
title: 移动 Azure 中的 Linux VM | Microsoft 文档
description: 在 Resource Manager 部署模型中将 Linux VM 移到其他 Azure 订阅或资源组。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: 7c22fe8beea894bccb311a63a1be70c972188e59
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667289"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>将 Linux VM 移到其他订阅或资源组
本文逐步说明如何在资源组或订阅之间移动 Linux 虚拟机 (VM)。 如果在个人订阅中创建了 VM，现在想要将其移到公司的订阅，则在订阅之间移动 VM 会很方便。

> [!IMPORTANT]
>不可在此时移动 Azure 托管磁盘。 
>
>在移动过程中将创建新的资源 ID。 移动 VM 后，需要更新工具和脚本以使用新的资源 ID。 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>使用 Azure CLI 移动 VM


使用 Azure CLI 移动 VM 之前，需要确保源订阅和目标订阅存在于同一租户中。 若要检查这两个订阅是否具有相同的租户 ID，请使用 [az account show](/cli/azure/account)。

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
如果源和目标订阅的租户 ID 不相同，则必须联系[支持人员](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)才能将资源移动到新租户。

若要成功移动 VM，需要移动 VM 及其所有支持资源。 使用 [az resource list](/cli/azure/resource) 命令列出资源组中的所有资源及其 ID。 这有助于通过管道将此命令的输出发送到文件，以便将 ID 复制并粘贴到后续命令中。

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

要将 VM 及其资源移到其他资源组，请使用 [az resource move](/cli/azure/resource)。 以下示例说明如何移动 VM 及其所需的大多数通用资源。 使用 **-ids** 参数，并传入要移动的资源的 ID 的逗号分隔列表（不含空格）。

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

如果要将 VM 及其资源移到其他订阅，请添加 **--destination-subscriptionId** 参数来指定目标订阅。

系统要求确认是否要移动指定的资源时，请输入“Y”进行确认  。

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>后续步骤
可以在资源组和订阅之间移动许多不同类型的资源。 有关详细信息，请参阅[将资源移到新资源组或订阅](../../resource-group-move-resources.md)。    

