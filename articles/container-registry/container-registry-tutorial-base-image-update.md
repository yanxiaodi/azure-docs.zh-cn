---
title: 教程 - 在基础映像更新时自动化容器映像生成 - Azure 容器注册表任务
description: 本教程介绍在更新基础映像时，如何配置 Azure 容器注册表任务以自动触发云中的容器映像生成。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 6b9a74ee6530d8fc195490b0f1414e6348e855f6
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743592"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>教程：在 Azure 容器注册表中更新基础映像时自动化容器映像生成 

ACR 任务支持在容器基础映像更新时自动化的生成执行，例如在某个基础映像中修补 OS 或应用程序框架时。 本教程介绍当容器基础映像已推送到注册表时，如何在 ACR 任务中创建在云中触发生成的任务。

本教程（教程系列的最后一部分）的内容包括：

> [!div class="checklist"]
> * 生成基础映像
> * 创建应用程序映像生成任务
> * 更新基础映像以触发应用程序映像任务
> * 显示已触发的任务
> * 验证已更新的应用程序映像

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果想本地使用 Azure CLI，则必须已安装 Azure CLI 版本 **2.0.46** 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI][azure-cli]。

## <a name="prerequisites"></a>先决条件

### <a name="complete-the-previous-tutorials"></a>完成前一篇教程

本教程假定你已完成本系列中前两个教程中的步骤，其中包括：

* 创建 Azure 容器注册表
* 创建示例存储库分支
* 克隆示例存储库
* 创建 GitHub 个人访问令牌

如果尚未完成以上步骤，请在继续之前先完成前两个教程步骤：

[使用 Azure 容器注册表任务在云中生成容器映像](container-registry-tutorial-quick-task.md)

[使用 Azure 容器注册表任务自动化容器映像生成](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>配置环境

使用适用于环境的值填充这些 shell 环境变量。 此步骤并非必须执行的步骤，但它能让在此教程中执行多个 Azure CLI 命令更容易。 如果没有填充这些环境变量，示例命令中出现时则必须手动替换每个值。

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>基础映像

定义大部分容器映像的 Dockerfile 指定它所基于的父级映像，通常称为它的基础映像  。 基础映像通常包含操作系统，例如 [Alpine Linux][base-alpine] 或 [Windows Nano Server][base-windows]，其余的容器层应用于这些操作系统上。 这些映像可能还包括应用程序框架，例如 [Node.js][base-node] 或 [.NET Core][base-dotnet]。

### <a name="base-image-updates"></a>基础映像更新

基础映像通常通过映像维护程序更新，以将 OS 或框架的新功能或改进添加进该映像中。 安全补丁是更新基础映像的另一常见原因。

基础映像更新时，将提示需要重新生成注册表中基于该映像的任何容器映像，以添加新功能和修补。 更新容器的基础映像时，ACR 任务能够自动生成映像。

### <a name="tasks-triggered-by-a-base-image-update"></a>基础映像更新触发的任务

* 对于从 Dockerfile 生成的映像，ACR 任务将在以下位置检测对基础映像的依赖关系：

  * 运行任务所在的同一 Azure 容器注册表
  * 同一区域中的另一个 Azure 容器注册表 
  * Docker Hub 中的公共存储库 
  * Microsoft 容器注册表中的公共存储库

   如果 `FROM` 语句中指定的基础映像驻留在上述某个位置，则 ACR 任务会添加一个挂钩，以确保它的基础映像更新时会重新生成该映像。

* 目前，ACR 任务仅跟踪应用程序（*运行时*）映像的基础映像更新。 它不跟踪多阶段 Dockerfile 中使用的中间 (buildtime  ) 映像的基础映像更新。  

* 使用 [az acr task create][az-acr-task-create] 命令创建某个 ACR 任务时，默认会启用该任务，以便在更新基础映像时触发。  即，`base-image-trigger-enabled` 属性设置为 True。 若要在任务中禁用此行为，请将该属性更新为 False。 例如，运行以下 [az acr task update][az-acr-task-update] 命令：

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* 若要启用某个 ACR 任务来确定并跟踪容器映像的依赖项（包含其基础映像），首先必须触发该任务**至少一次**。 例如，使用 [az acr task run][az-acr-task-run] 命令手动触发该任务。

* 若要在更新基础映像时触发任务，基础映像必须有一个稳定的标记，例如 `node:9-alpine`。  在将 OS 和框架修补到最新稳定版本时会更新的基础映像往往带有此标记。 如果使用新的版本标记更新基础映像，则不会触发任务。 有关映像标记的详细信息，请参阅[最佳做法指南](container-registry-image-tag-version.md)。 

### <a name="base-image-update-scenario"></a>基础映像更新方案

本教程将指导完成基础映像更新方案。 [代码示例][code-sample]包括两个 Dockerfile：一个应用程序映像和一个它指定为其基础的映像。 在后续部分，我们将创建一个 ACR 任务，它会在新版本基础映像推送到同一容器注册表时自动触发应用程序映像的生成。

[Dockerfile-app][dockerfile-app]：小型 Node.js Web 应用程序，它呈现一个静态 Web 页面，其中显示该应用程序所基于的 Node.js 版本。 该版本字符串是模拟的：显示基础映像中定义的环境变量和 `NODE_VERSION` 的内容。

[Dockerfile-base][dockerfile-base]：`Dockerfile-app` 指定为其基础的映像。 它本身基于[节点][base-node]映像，并且包括 `NODE_VERSION` 环境变量。

在以下部分，我们将创建一个任务，在基础映像 Dockerfile 中更新 `NODE_VERSION` 值，并使用 ACR 任务来生成基础映像。 ACR 任务将新基础映像推送到注册表时，它会自动触发应用程序映像的生成。 可选择本地运行应用程序容器映像，在生成的映像中查看不同版本字符串。

在本教程中，ACR 任务会生成并推送在 Dockerfile 中指定的应用程序容器映像。 ACR 任务也可运行[多步骤任务](container-registry-tasks-multi-step.md)，使用 YAML 文件来定义相关步骤，以便生成并推送多个容器，并可选择对其进行测试。

## <a name="build-the-base-image"></a>生成基础映像

首先使用 ACR 任务的快速任务来生成基础映像  。 如本系列教程的[第一篇教程](container-registry-tutorial-quick-task.md)中所述，如果生成成功，则此过程不仅会生成映像，还会将其推送到容器注册表。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>创建任务

接下来，使用 [az acr task create][az-acr-task-create] 创建一个任务：

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> 如果以前在预览期使用 `az acr build-task` 创建了任务，则需要使用 [az acr task][az-acr-task] 命令重新创建这些任务。

此任务类似于[上一篇教程](container-registry-tutorial-build-task.md)中创建的快速任务。 将提交内容推送到 `--context` 指定的存储库时，生成任务会指示 ACR 任务触发映像生成。 在前一篇教程用于生成映像的 Dockerfile 指定了公共基础映像 (`FROM node:9-alpine`)，而此任务中的 Dockerfile [Dockerfile-app][dockerfile-app] 指定的是同一注册表中的基础映像：

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

使用此配置可以轻松模拟稍后在本教程中对基础映像进行的框架修补。

## <a name="build-the-application-container"></a>生成应用程序容器

使用 [az acr task run][az-acr-task-run] 手动触发任务并生成应用程序映像。 此步骤确保该任务跟踪应用程序映像对基础映像的依赖性。

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

任务完成后，如果还要完成以下可选步骤，请记下“运行 ID”（例如“da6”）  。

### <a name="optional-run-application-container-locally"></a>可选：本地运行应用程序容器

如果在本地运行（而不是在 Cloud Shell）并且已安装 Docker，运行容器，查看呈现在 Web 浏览器中的应用程序，并重新生成其基础映像。 如果正在使用 Cloud Shell，可跳过此部分（Cloud Shell 不支持 `az acr login` 或 `docker run`）。

首先，使用 [az acr login][az-acr-login] 在容器注册表中进行身份验证：

```azurecli
az acr login --name $ACR_NAME
```

然后，使用 `docker run` 在本地运行容器。 将 **\<run-id\>** 替换为上一步骤的输出中的“运行 ID”（例如“da6”）。 此示例将容器命名为 `myapp`，并包含 `--rm` 参数，以便在停止容器后将其删除。

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

在浏览器中导航到 `http://localhost:8080`，应看见呈现在 Web 页面中的 Node.js 版本号，如下所示。 在稍后的步骤中，会通过向版本字符串中添加“a”来提升版本。

![浏览器中呈现示例应用程序的屏幕截图][base-update-01]

若要停止并删除容器，请运行以下命令：

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>列出生成

接下来，使用 [az acr task list-runs][az-acr-task-list-runs] 命令列出 ACR 任务对注册表完成的任务运行：

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

如果已完成之前的教程（并且没有删除注册表），应看到如下所示的输出。 记下任务运行的数目以及最新的运行 ID，以便在后续部分中更新基础映像后对比输出。

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>更新基础映像

在这里模拟基础映像中的框架补丁。 编辑“Dockerfile-base”，并在 `NODE_VERSION` 中定义的版本号后面添加一个“a”  ：

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

运行快速任务来生成修改后的基础映像。 记下输出中的“运行 ID”  。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

生成完成并且 ACR 任务将新基础映像推送到注册表后，它会触发应用程序映像的生成。 之前创建的任务触发应用程序映像生成可能需要一些时间，因为它必须检测新生成和推送的基础映像。

## <a name="list-updated-build"></a>列出已更新的生成

更新基础映像后，请再次列出任务运行，以便与之前的列表进行比较。 如果开始时输出没有区别，可定期运行该命令以查看出现在列表中的新任务运行。

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

输出如下所示。 最后执行的生成的触发器应为“映像更新”，指示任务是通过基础映像的快速任务启动的。

```console
$ az acr task list-runs --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

如果想要执行以下可选步骤运行新生成的容器，以查看更新的版本号，请记下映像更新触发的生成的“运行 ID”值（在之前的输出中为“da8”）  。

### <a name="optional-run-newly-built-image"></a>可选：运行新生成的映像

如果正在本地运行（而不是在 Cloud Shell 中运行）并且已安装 Docker，则可在新应用程序映像的生成完成后运行它。 将 `<run-id>` 替换为上一步骤中获取的“运行 ID”。 如果正在使用 Cloud Shell，可跳过此部分（Cloud Shell 不支持 `docker run`）。

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

在浏览器中导航到 http://localhost:8081 ，应看见网页上显示有更新后的 Node.js 版本编号（带有“a”）：

![浏览器中呈现示例应用程序的屏幕截图][base-update-02]

请务必注意，使用新版本号更新“基础”映像，但是最后生成的“应用程序”映像显示新版本   。 ACR 任务选取了对基础映像的更改，并自动重新生成了应用程序映像。

若要停止并删除容器，请运行以下命令：

```bash
docker stop updatedapp
```

## <a name="clean-up-resources"></a>清理资源

若要删除本系列教程中创建的所有资源，包括容器注册表、容器实例、密钥保管库和服务主体，请运行以下命令：

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解在更新映像的基础映像后，如何使用任务来自动触发容器映像生成。 现在，继续了解适用于容器注册表的身份验证。

> [!div class="nextstepaction"]
> [Azure 容器注册表中身份验证](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
