---
title: （已弃用）使用 Docker API 管理 Azure Swarm 群集
description: 在 Azure 容器服务中将容器部署到 Docker Swarm 群集
services: container-service
author: rgardler
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 09/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 04cc9048271d653bd77fd7f2707c8f510ea8c29f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61456542"
---
# <a name="deprecated-container-management-with-docker-swarm"></a>（已弃用）使用 Docker Swarm 管理容器

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Docker Swarm 提供了一个环境，用于跨一组共用的 Docker 主机部署容器化的工作负荷。 Docker Swarm 使用本地 Docker API。 用于在 Docker Swarm 中管理容器的工作流几乎完全与单个容器主机上的工作流相同。 本文档提供了简单的示例来演示如何在 Docker Swarm 的 Azure 容器服务实例中部署容器化的工作负荷。 有关深入介绍 Docker Swarm 的更多文档，请参阅 [Docker.com 上的 Docker Swarm](https://docs.docker.com/swarm/)。

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

本文档中这些练习的先决条件：

[在 Azure 容器服务中创建 Swarm 群集](container-service-deployment.md)

[连接 Azure 容器服务中的 Swarm 群集](../container-service-connect.md)

## <a name="deploy-a-new-container"></a>部署新容器
要在 Docker Swarm 中创建一个新的容器，请使用 `docker run` 命令（根据上述的先决条件，确保已打开指向主服务的 SSH 隧道）。 此示例将基于 `yeasy/simple-web` 映像创建一个容器：

```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

创建容器后，使用 `docker ps` 返回有关容器的信息。 请注意，此处列出了托管容器的 Swarm 代理：

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

现在，可以通过 Swarm 代理负载均衡器的公用 DNS 名称访问此容器中运行的应用程序。 可以在 Azure 门户中找到此信息：  

![实际访问结果](./media/container-service-docker-swarm/real-visit.jpg)  

默认情况下，负载均衡器已打开端口 80、8080 和 443。 如果想要在另一个端口上连接，需要在 Azure 负载均衡器上为代理池打开该端口。

## <a name="deploy-multiple-containers"></a>部署多个容器
由于启动了多个容器，通过多次执行 'docker run'，可以使用 `docker ps` 命令来查看运行这些容器的主机。 在下面的示例中，三个容器均匀地分布在三个 Swarm 代理上：  

```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>使用 Docker Compose 部署容器
可以使用 Docker Compose 自动执行多个容器的部署和配置。 为此，请确保已创建安全外壳 (SSH) 隧道并已设置 DOCKER_HOST 变量（请参阅上述的先决条件）。

在本地系统上创建 docker-compose.yml 文件。 为此，请使用此 [示例](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml)。

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

运行 `docker-compose up -d` 以启动容器部署：

```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

最后，将返回运行中容器的列表。 此列表反映使用 Docker Compose 部署的容器：

```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

当然，也可以使用 `docker-compose ps` 仅检查 `compose.yml` 文件中定义的容器。

## <a name="next-steps"></a>后续步骤
[了解有关 Docker Swarm 的更多信息](https://docs.docker.com/swarm/)

