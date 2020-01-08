---
title: include 文件
description: include 文件
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/18/2018
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 458782011623721bb44771d42caa32130e23bbc9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173437"
---
[!INCLUDE [resource group intro text](resource-group.md)]

在 Cloud Shell 中，使用 [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) 命令创建资源组。 下面的示例命令在“美国中南部”位置创建名为 *myResourceGroup* 的资源组。  要查看“免费”层中应用服务支持的所有位置，请运行 [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) 命令。 

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

通常在附近的区域中创建资源组和资源。 

此命令完成后，JSON 输出会显示资源组属性。