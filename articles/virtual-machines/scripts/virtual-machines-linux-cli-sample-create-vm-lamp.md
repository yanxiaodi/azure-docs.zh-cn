---
title: Azure CLI 脚本示例 - 在负载均衡虚拟机规模集中部署 LAMP 堆栈 | Microsoft Docs
description: 使用自定义脚本扩展在 Azure 上的负载均衡虚拟机规模集中部署 LAMP 堆栈。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.author: cynthn
tags: azure-service-management
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.custom: mvc
ms.date: 04/05/2017
ms.openlocfilehash: ffbece620ae038307d30ae3b330854280f09bd28
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68963248"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>在负载均衡虚拟机规模集中部署 LAMP 堆栈

此示例创建一个虚拟机规模集，并应用运行自定义脚本的扩展在规模集中每个虚拟机上部署 LAMP 堆栈。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>连接

使用此代码查看如何连接到 VM 和规模集。

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>清理部署 

运行如下命令来删除资源组、规模集和 VM 以及所有相关资源。

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机、可用性集、负载均衡器和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az vmss create](https://docs.microsoft.com/cli/azure/vmss) | 创建虚拟机规模集 |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | 添加负载均衡终结点 |
| [az vmss extension set](https://docs.microsoft.com/cli/azure/vmss/extension) | 创建对 VM 部署运行自定义脚本的扩展 |
| [az vmss update-instances](https://docs.microsoft.com/cli/azure/vmss) | 在将扩展应用到规模集之前部署的 VM 实例上运行此自定义脚本。 |
| [az vmss scale](https://docs.microsoft.com/cli/azure/vmss) | 通过添加更多 VM 实例来扩大规模集。 部署实例时，在实例上运行此自定义脚本。 |
| [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip) | 获取示例创建的 VM 的 IP 地址。 |
| [az network lb show](https://docs.microsoft.com/cli/azure/network/lb) | 获取负载均衡器使用的前端和后端端口。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure Linux VM 文档](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
