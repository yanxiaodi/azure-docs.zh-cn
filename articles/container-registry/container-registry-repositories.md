---
title: Azure 门户中的“Azure 容器注册表”存储库
description: 如何在 Azure 门户中查看“Azure 容器注册表”存储库。
services: container-registry
author: cristy
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: jeconnoc
ms.openlocfilehash: 22f84efee2b3996734e8e38c73d30ba891b745d9
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310583"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>在 Azure 门户中查看容器注册表存储库

使用 Azure 容器注册表可将 Docker 容器映像存储在存储库中。 通过将映像存储在存储库中，可以将映像组（或映像版本）存储在隔离环境中。 将映像推送到注册表时，可以指定这些存储库，并可在 Azure 门户中查看其内容。

## <a name="prerequisites"></a>系统必备

* **容器注册表**:在 Azure 订阅中创建容器注册表。 例如，使用 [Azure 门户](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
* **DOCKER CLI**:在本地计算机上安装[docker][docker-install] , 这为你提供 docker 命令行界面。
* **容器映像**:将映像推送到容器注册表。 有关如何推送和拉取映像的指南，请参阅[推送和拉取映像](container-registry-get-started-docker-cli.md)。

## <a name="view-repositories-in-azure-portal"></a>在 Azure 门户中查看存储库

在 Azure 门户中可以查看承载映像以及映像标记的存储库的列表。

如果已按照[推送和拉取映像](container-registry-get-started-docker-cli.md)中的步骤操作（且随后未删除映像），则应在容器注册表中有一个 Nginx 映像。 该文中的说明指定使用命名空间（`/samples/nginx` 中的“samples”）标记映像。 作为复习, 本文中指定的[docker push][docker-push]命令是:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 由于 Azure 容器注册表支持此类多级存储库命名空间，因此可以将与特定应用或应用集合相关的映像集合的范围限制为不同的开发或运营团队。 若要详细了解容器注册表中的存储库，请参阅 [Azure 中的专用 Docker 容器注册表](container-registry-intro.md)。

若要查看存储库，请执行以下操作：

1. 登录到 [Azure 门户][portal]
1. 选择向其推送了 Nginx 映像的 **Azure 容器注册表**
1. 选择“存储库”  可查看包含注册表中映像的存储库的列表
1. 选择某个存储库以查看该存储库中的映像标记

例如，如果已按照[推送和拉取映像](container-registry-get-started-docker-cli.md)中的说明推送了 Nginx 映像，应看到的内容如下所示：

![门户中的存储库](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>后续步骤

现已基本了解如何在门户中查看和使用存储库，请尝试在 [Azure Kubernetes 服务 (AKS)](../aks/tutorial-kubernetes-prepare-app.md) 群集中使用 Azure 容器注册表。

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
