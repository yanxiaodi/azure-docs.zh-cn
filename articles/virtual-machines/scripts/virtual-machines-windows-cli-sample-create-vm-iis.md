---
title: Azure CLI 脚本示例 - 安装 IIS | Microsoft 文档
description: Azure CLI 脚本示例 - 安装 IIS
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: cynthn
ms.openlocfilehash: 6e9e54c164ed3112cddd7f92304eb44ee86abef6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708263"
---
# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>使用 Azure CLI 快速创建虚拟机

此脚本创建一个运行 Windows Server 2016 的 Azure 虚拟机，并使用 Azure 虚拟机自定义脚本扩展安装 IIS。 运行此脚本后，可通过虚拟机的公共 IP 地址访问默认 IIS 网站。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-windows-iis/create-vm-windows-iis.sh "Quick Create VM")]

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
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和网络安全组。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule) | 创建网络安全组规则，以允许入站流量。 在此示例中，将为 HTTP 流量打开端口 80。 |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension) | 将虚拟机扩展添加到 VM 并运行该扩展。 在此示例中，使用自定义脚本扩展来安装 IIS。|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure Windows VM 文档](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
