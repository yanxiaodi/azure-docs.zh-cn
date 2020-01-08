---
title: Azure CLI 脚本示例 - 使用内部和外部 NSG 创建两个 VM | Microsoft 文档
description: Azure CLI 脚本示例 - 使用内部和外部 NSG 创建两个 VM
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: gwallace
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.custom: mvc
ms.openlocfilehash: cc2b7182b0e9191b830ffc32bcc479cc06c1718a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708240"
---
# <a name="secure-network-traffic-between-virtual-machines"></a>保护虚拟机之间的网络流量

此脚本创建两个虚拟机，并保护这两个虚拟机的传入流量。 一个虚拟机可在 Internet 上访问，其网络安全组 (NSG) 配置为允许端口 3389 和端口 80 上的流量。 第二个虚拟机无法在 Internet 上访问，其 NSG 配置为仅允许来自第一个虚拟机的流量。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Create VM with NSG")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | 创建 Azure 虚拟网络和子网。 |
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet) | 创建子网。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule) | 更新 NSG 规则。 在本例中，将更新后端规则，仅从前端子网传递流量。 |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule) | 返回有关网络安全组规则的信息。 在此示例中，规则名称存储在变量中，以便以后在脚本中使用。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure Windows VM 文档](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
