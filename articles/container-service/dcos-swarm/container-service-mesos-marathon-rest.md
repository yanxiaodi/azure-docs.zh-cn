---
title: （已弃用）使用 Marathon REST API 管理 Azure DC/OS 群集
description: 使用 Marathon REST API 将容器部署到 Azure 容器服务 DC/OS 群集。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 73fa9c4433a2af780798f0439c0a119bc32a678f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64916687"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>（已弃用）通过 Marathon REST API 管理 DC/OS 容器

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS 提供了一种环境，可进行群集工作负荷的部署和缩放，以及底层硬件的抽象化。 DC/OS 上方是一种管理计划和执行计算工作负荷的框架。 尽管框架可用于许多常见的工作负荷，本文档逐步讲解如何使用 Marathon REST API 创建和缩放容器部署。 

## <a name="prerequisites"></a>必备组件

开始了解这些示例之前，需要一个在 Azure 容器服务中配置的 DC/OS 群集。 还需要具有到此群集的远程连接。 有关这些项目的详细信息，请参阅以下文章：

* [部署 Azure 容器服务群集](container-service-deployment.md)
* [连接到 Azure 容器服务群集](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>访问 DC/OS API
在连接到 Azure 容器服务群集后，您可以通过 http 访问 DC/OS 和相关的 REST Api:\//localhost:local 的端口。 本文档中的示例假定在端口 80 上实现隧道连接。 例如，Uri 上访问 Marathon 终结点从 http: \/ /localhost/marathon/v2 /。 

有关 [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) 和 [Chronos API](https://mesos.github.io/chronos/docs/api.html) 的详细信息，请参阅 Mesosphere 文档；有关 [Mesos 计划程序 API](https://mesos.apache.org/documentation/latest/scheduler-http-api/) 的详细信息，请参阅 Apache 文档。

## <a name="gather-information-from-dcos-and-marathon"></a>从 DC/OS 和 Marathon 收集信息
在将容器部署到 DC/OS 群集之前，收集一些有关 DC/OS 的信息，例如 DC/OS 代理的名称和状态。 为此，请查询 DC/OS REST API 的 `master/slaves` 终结点。 如果一切正常，则查询会返回 DC/OS 代理列表和每个代理的数个属性。

```bash
curl http://localhost/mesos/master/slaves
```

现在，使用 Marathon `/apps` 终结点检查 DC/OS 群集的当前应用程序部署。 如果是新群集，则会看到应用为一个空数组。

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>部署 Docker 格式容器
使用描述预期部署的 JSON 文件通过 Marathon REST API 部署 Docker 格式容器。 以下示例将 Nginx 容器部署到群集中的专用代理。 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

要部署 Docker 格式的容器，请将 JSON 文件存储在可访问的位置。 接下来，要部署容器，请运行以下命令。 指定 JSON 文件的名称（此示例中为 `marathon.json`）。

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

输出与下面类似：

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

现在，如果查询 Marathon 应用程序，则此新应用程序会出现在输出中。

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>访问容器

可以确定 Nginx 运行于群集中其中一个专用代理的容器中。 若要查找运行容器的的主机和端口，请查询正在运行的任务的 Marathon： 

```bash
curl localhost/marathon/v2/tasks
```

在输出中查找 `host` 的值（类似于 `10.32.0.x` 的 IP 地址）和 `ports` 的值。


现在对群集的管理 FQDN 建立 SSH 终端连接，而不是隧道连接。 连接后，进行以下请求，从而替换 `host` 和 `ports` 的正确值：

```bash
curl http://host:ports
```

Nginx 服务器的输出与下面类似：

![容器的 Nginx](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>缩放容器
可以使用 Marathon API 来扩大或缩小应用程序部署。 在上一示例中，为应用程序部署了一个实例。 让我们进行扩展，扩大为三个实例。 为此，请使用以下 JSON 文本创建 JSON 文件，并将其存储在可访问的位置。

```json
{ "instances": 3 }
```

从隧道连接运行以下命令来扩大应用程序。

> [!NOTE]
> URI 为 http: \/ /localhost/marathon/v2/应用/跟要缩放的应用程序的 ID。 如果使用的 Nginx 示例，则提供在这里，则 URI 将为 http:\//localhost/marathon/v2/apps/nginx。

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

最后，查询应用程序的 Marathon 终结点。 此时可看到有 3 个 Nginx 容器。

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>等效的 PowerShell 命令
可以在 Windows 系统上使用 PowerShell 命令执行这些操作。

若要收集有关该 DC/OS 群集的详细信息，如代理名称和代理状态，请运行以下命令：

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

使用描述了预期部署的 JSON 文件通过 Marathon 部署 Docker 格式容器。 以下示例部署 Nginx 容器，将 DC/OS 代理的端口 80 绑定到容器的端口 80。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

要部署 Docker 格式的容器，请将 JSON 文件存储在可访问的位置。 接下来，要部署容器，请运行以下命令。 指定 JSON 文件的路径（此示例中为 `marathon.json`）。

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

还可以使用 Marathon API 来扩大或缩小应用程序部署。 在上一示例中，为应用程序部署了一个实例。 让我们进行扩展，扩大为三个实例。 为此，请使用以下 JSON 文本创建 JSON 文件，并将其存储在可访问的位置。

```json
{ "instances": 3 }
```

运行以下命令来扩大应用程序：

> [!NOTE]
> URI 为 http: \/ /localhost/marathon/v2/应用/跟要缩放的应用程序的 ID。 如果你在此处使用提供的 Nginx 示例，则 URI 将为 http:\//localhost/marathon/v2/apps/nginx。

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>后续步骤
* [阅读有关 Mesos HTTP 终结点的详细信息](https://mesos.apache.org/documentation/latest/endpoints/)
* [阅读有关 Marathon REST API 的详细信息](https://mesosphere.github.io/marathon/docs/rest-api.html)

