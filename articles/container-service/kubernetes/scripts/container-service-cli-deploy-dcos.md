---
title: Azure CLI 脚本示例 - 创建 ACS DC/OS 群集 | Microsoft Docs
description: Azure CLI 脚本示例 - 创建 ACS DC/OS 群集
services: container-service
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: acs, azure-container-service
keywords: Docker, 容器, 微服务, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: iainfou
ms.openlocfilehash: 2d9f58527908a17dd445b6afecbcd7e93f70de2c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001632"
---
# <a name="deprecated-create-an-azure-container-service-dcos-cluster"></a>（已弃用）创建 Azure 容器服务 DC/OS 群集

[!INCLUDE [ACS deprecation](../../../../includes/container-service-kubernetes-deprecation.md)]

本示例创建可运行 DCOS 的 Azure 容器服务群集。

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

```azurecli
az group create --name myResourceGroup --location eastus

az acs create \
  --orchestrator-type dcos \
  --resource-group myResourceGroup \
  --name myDCOSCluster \
  --generate-ssh-keys
```

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az acs create](https://docs.microsoft.com/cli/azure/acs#az-acs-create) | 创建 ACS 群集。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure 容器服务文档](../cli-samples.md)中找到其他 Azure 容器服务 CLI 脚本示例。