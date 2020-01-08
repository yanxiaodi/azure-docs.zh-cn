---
title: 如何使用 Azure CLI 调整 Linux VM 的大小 | Microsoft Docs
description: 如何通过更改 VM 大小来扩展或缩减 Linux 虚拟机。
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: gwallace
editor: ''
tags: ''
ms.assetid: e163f878-b919-45c5-9f5a-75a64f3b14a0
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 46baa3d4dbcd466944d7a91e446e380c89f53f2b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671743"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>使用 Azure CLI 重设 Linux 虚拟机大小 

预配虚拟机 (VM) 后，您可以扩展 VM 向上或向下通过更改[VM 大小][vm-sizes]。 在某些情况下，必须先解除分配 VM。 如果所需大小在托管 VM 的硬件群集上不可用，则需要解除分配 VM。 本文详细介绍了如何使用 Azure CLI 重设 Linux VM 大小。 

## <a name="resize-a-vm"></a>调整 VM 的大小
若要重设 VM 大小，需要安装最新版 [Azure CLI](/cli/azure/install-az-cli2)，并使用 [az login](/cli/azure/reference-index) 登录 Azure 帐户。

1. 使用 [az vm list-vm-resize-options](/cli/azure/vm) 查看托管 VM 的硬件群集上可用的 VM 大小的列表。 以下示例列出资源组 `myResourceGroup` 区域中名为 `myVM` 的 VM 的 VM 大小：
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. 如果列出了所需的 VM 大小，则使用 [az vm resize](/cli/azure/vm) 调整 VM 大小。 以下示例将名为 `myVM` 的 VM 调整为 `Standard_DS3_v2` 大小：
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    在此过程中，VM 将重新启动。 重启后，现有 OS 和数据磁盘将重新映射。 临时磁盘上的所有内容会丢失。

3. 如果未列出所需的 VM 大小，则需要先使用 [az vm deallocate](/cli/azure/vm) 解除分配 VM。 此过程允许将 VM 调整为该区域支持的任何可用大小然后将其启动。 以下步骤会将名为 `myResourceGroup` 的资源组中名为 `myVM` 的 VM 解除分配、调整大小并启动：
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > 解除分配 VM 也会释放分配给该 VM 的所有动态 IP 地址。 OS 和数据磁盘不受影响。

## <a name="next-steps"></a>后续步骤
若要提高伸缩性，请运行多个 VM 实例并进行横向扩展。有关详细信息，请参阅[在虚拟机规模集自动缩放 Linux 计算机][scale-set]。 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
