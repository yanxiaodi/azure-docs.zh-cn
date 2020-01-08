---
title: Azure CLI 脚本示例 - 使用 VHD 创建 VM | Microsoft 文档
description: Azure CLI 脚本示例 - 使用虚拟硬盘创建 VM。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 93a94e9a32b33ffd1f52231546d6aa6a71c78df0
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841419"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>使用虚拟硬盘创建 VM

本示例使用 VHD 创建虚拟机。
本示例创建资源组、存储帐户、容器，然后通过将 VHD 上传到容器来创建 VM。
本示例将 ssh 公钥替换为用户的公钥，因此用户可以访问 VM。

用户需要可引导 VHD。 脚本会查找 `~/sample.vhd`。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机、可用性集、负载均衡器和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account) | 列出存储帐户 |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account) | 检查存储帐户名称是否有效且目前还不存在 |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys) | 列出存储帐户的密钥 |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob) | 检查 Blob 是否存在 |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container) | 在存储帐户中创建一个容器。 |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob) | 通过上传 VHD，在容器中创建一个 Blob。 |
| [az vm list](https://docs.microsoft.com/cli/azure/vm) | 与 `--query` 一起使用，用于检查 VM 名称是否已使用。 | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | 创建虚拟机。 |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az-vm-list-ip-addresses) | 获取已创建虚拟机的 IP 地址。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure Linux VM 文档](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
