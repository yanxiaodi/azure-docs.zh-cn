---
title: Docker 容器的食谱
titleSuffix: Azure Cognitive Services
description: 使用这些容器食谱来创建可重复使用的认知服务容器。 可以用部分或全部配置设置来构建容器, 以便在容器启动时不需要它们。 使用此新的容器层 (包括设置), 并在本地对其进行测试后, 可以将容器存储在容器注册表中。 当容器启动时, 它将只需要当前未存储在容器中的设置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: a8162f96051a73b9f6e6a6fe3ece020e0a94f08f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068825"
---
# <a name="create-containers-for-reuse"></a>创建用于重用的容器

使用这些容器食谱来创建可重复使用的认知服务容器。 可以用部分或全部配置设置来构建容器, 以便在容器启动时_不_需要它们。

使用此新的容器层 (包括设置), 并在本地对其进行测试后, 可以将容器存储在容器注册表中。 当容器启动时, 它将只需要当前未存储在容器中的设置。 专用注册表容器提供配置空间以便您在中传递这些设置。

## <a name="docker-run-syntax"></a>Docker 运行语法

本`docker run`文档中的任何示例均假定 Windows 控制台`^`带有行继续符。 请考虑以下各项使用:

* 除非非常熟悉 docker 容器，否则不要更改参数顺序。
* 如果使用的是 Windows 以外的操作系统或 Windows 控制台以外的控制台, 请使用正确的控制台/终端、用于装载的文件夹语法, 以及控制台和系统的行继续符。  由于认知服务容器是 Linux 操作系统, 因此目标装载使用 Linux 样式的文件夹语法。
* `docker run`例如, 使用目录关闭`c:`驱动器, 以避免在 Windows 上出现任何权限冲突。 如果需要使用特定目录作为输入目录，则需要授予 docker 服务权限。

## <a name="store-no-configuration-settings-in-image"></a>在映像中不存储任何配置设置

每个`docker run`服务的示例命令不会将任何配置设置存储在容器中。 从控制台或注册表服务启动容器时, 需要传入这些配置设置。 专用注册表容器提供配置空间以便您在中传递这些设置。

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>重用食谱: 将所有配置设置存储到容器

为了存储所有配置设置, 请使用这些设置`Dockerfile`创建一个。

此方法的问题:

* 新容器包含来自原始容器的单独名称和标记。
* 若要更改这些设置, 必须更改 Dockerfile 的值, 重新生成映像, 并将其重新发布到注册表。
* 如果某人获取了对容器注册表或本地主机的访问权限, 则可以运行该容器并使用认知服务终结点。
* 如果认知服务不需要输入安装, 请不要将这些`COPY`行添加到 Dockerfile。

创建 Dockerfile, 从要使用的现有认知服务容器提取, 然后在 Dockerfile 中使用 docker 命令来设置或请求容器所需的信息。

此示例:

* `{BILLING_ENDPOINT}` 使用`ENV`从主机的环境密钥设置计费终结点。
* 使用 "ENV" `{ENDPOINT_KEY}`从主机的环境密钥设置计费 API 密钥。

### <a name="reuse-recipe-store-billing-settings-with-container"></a>重用食谱: 使用容器存储计费设置

此示例演示如何从 Dockerfile 生成文本分析的情绪容器。

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

根据需要在[本地](#how-to-use-container-on-your-local-host)或从[专用注册表容器](#how-to-add-container-to-private-registry)中生成并运行容器。

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>重用食谱: 使用容器存储计费和装载设置

此示例演示如何使用语言理解, 以及如何保存 Dockerfile 中的计费和模型。

* 使用`COPY`复制主机的文件系统中的语言理解 (LUIS) 模型文件。
* LUIS 容器支持多个模型。 如果所有模型都存储在相同的文件夹中, 则需要一`COPY`条语句。
* 从模型输入目录的相对父级运行 docker 文件。 对于下面的示例, 请`docker build` `/input`从的`docker run`相对父级运行和命令。 命令中`/input`的第一个是主计算机的目录。 `COPY` 第二`/input`个是容器的目录。

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

根据需要在[本地](#how-to-use-container-on-your-local-host)或从[专用注册表容器](#how-to-add-container-to-private-registry)中生成并运行容器。

## <a name="how-to-use-container-on-your-local-host"></a>如何在本地主机上使用容器

若要生成 Docker 文件, 请`<your-image-name>`将替换为映像的新名称, 然后使用:

```console
docker build -t <your-image-name> .
```

运行映像, 并在容器停止时将其删除 (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>如何将容器添加到专用注册表

请按照以下步骤使用 Dockerfile, 并将新映像放置在专用容器注册表中。  

1. `Dockerfile`使用文本 "重用食谱" 创建。 `Dockerfile`没有扩展。

1. 将尖括号中的所有值替换为自己的值。

1. 使用以下命令将文件生成到命令行或终端中的映像。 将尖括号`<>`中的值替换为自己的容器名称和标记。  

    标记选项`-t`是一种添加有关已为容器更改的信息的方法。 例如, 容器名称`modified-LUIS`指示原始容器已分层。 的`with-billing-and-model`标记名称指示如何修改语言理解 (LUIS) 容器。

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. 从控制台登录到 Azure CLI。 此命令打开浏览器并要求身份验证。 经过身份验证后, 可以关闭浏览器并在控制台中继续工作。

    ```azurecli
    az login
    ```

1. 使用控制台中的 Azure CLI 登录到专用注册表。

    将尖括号`<my-registry>`中的值替换为自己的注册表名称。  

    ```azurecli
    az acr login --name <my-registry>
    ```

    如果分配了服务主体, 还可以使用 docker login 登录。

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. 为容器标记专用注册表位置。 将尖括号`<my-registry>`中的值替换为自己的注册表名称。 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    如果不使用标记名称, `latest`则暗示使用。

1. 将新映像推送到专用容器注册表。 当你查看专用容器注册表时, 以下 CLI 命令中使用的容器名称将是该存储库的名称。

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建和使用 Azure 容器实例](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->