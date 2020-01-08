---
title: 通过 Azure Dev Spaces 在 Kubernetes 上使用 Visual Studio Code 和 Node.js 进行调试和循环访问
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: quickstart
description: 在 Azure 中使用容器、微服务和 Node.js 快速进行 Kubernetes 开发
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 8148f7858ac9de458daa1c15220507b0de1bad19
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772627"
---
# <a name="quickstart-debug-and-iterate-with-visual-studio-code-and-nodejs-on-kubernetes-using-azure-dev-spaces"></a>快速入门：通过 Azure Dev Spaces 在 Kubernetes 上使用 Visual Studio Code 和 Node.js 进行调试和循环访问

本指南介绍如何：

- 使用 Azure 中的托管 Kubernetes 群集设置 Azure Dev Spaces。
- 使用 Visual Studio Code 在容器中以迭代方式开发代码。
- 通过 Visual Studio Code 调试开发空间中的代码。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free)。
- [已安装 Visual Studio Code](https://code.visualstudio.com/download)。
- 已安装适用于 Visual Studio Code 的 [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) 扩展。
- [已安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-an-azure-kubernetes-service-cluster"></a>创建 Azure Kubernetes 服务群集

需要在[支持的区域][supported-regions]中创建 AKS 群集。 以下命令创建名为 *MyResourceGroup* 的资源组，以及名为 *MyAKS* 的 AKS 群集。

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>在 AKS 群集上启用 Azure Dev Spaces

使用 `use-dev-spaces` 命令在 AKS 群集上启用 Dev Spaces，然后按提示操作。 以下命令在 *MyResourceGroup* 组中的 *MyAKS* 群集上启用 Dev Spaces，并创建一个默认开发空间。 

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>获取示例应用程序代码

本文使用 [Azure Dev Spaces 示例应用程序](https://github.com/Azure/dev-spaces)来演示 Azure Dev Spaces 的用法。

从 GitHub 克隆该应用程序。

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>在 Visual Studio Code 中准备示例应用程序

打开 Visual Studio Code，依次单击“文件”、“打开...”，导航到 *dev-spaces/samples/nodejs/getting-started/webfrontend* 目录，然后单击“打开”。   

现在，*webfrontend* 项目便在 Visual Studio Code 中打开。 若要在开发空间中运行应用程序，请在命令面板中使用 Azure Dev Spaces 扩展生成 Docker 和 Helm chart 资产。

若要在 Visual Studio Code 中打开命令面板，请依次单击“视图”、“命令面板”。   开始键入 `Azure Dev Spaces` 并单击 `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。

![准备 Azure Dev Spaces 的配置文件](./media/common/command-palette.png)

当 Visual Studio Code 还提示你配置公共终结点时，请选择 `Yes` 以启用公共终结点。

![选择公共终结点](media/common/select-public-endpoint.png)

此命令通过生成 Dockerfile 和 Helm 图表来使你的项目适合在 Azure Dev Spaces 中运行。 它还会在项目的根目录下生成包含调试配置的 *.vscode* 目录。

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>通过 Visual Studio Code 在 Kubernetes 中生成并运行代码

单击左侧的“调试”图标，然后单击顶部的“启动服务器(AZDS)”。  

![启动服务器](media/get-started-node/debug-configuration-nodejs.png)

此命令在 Azure Dev Spaces 中生成并运行你的服务。 底部的“终端”窗口会显示运行 Azure Dev Spaces 的服务的生成输出和 URL。  “调试控制台”会显示日志输出。 

> [!Note]
> 如果在“命令面板”中看不到任何 Azure Dev Spaces 命令，请确保已安装[适用于 Azure Dev Spaces 的 Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)。  另请确认是否在 Visual Studio Code 中打开了 *dev-spaces/samples/nodejs/getting-started/webfrontend* 目录。

可以通过打开公共 URL 来查看正在运行的服务。

依次单击“调试”、“停止调试”以停止调试器。  

## <a name="update-code"></a>更新代码

若要部署服务的更新版本，可以更新项目中的任何文件，然后重新运行“启动服务器”  。 例如：

1. 如果应用程序仍在运行，请依次单击“调试”  和“停止调试”  来停止该应用程序。
1. 将 [`server.js`中的第 13 行](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13)更新为：
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. 保存所做更改。
1. 重新运行“启动服务器”  。
1. 导航到正在运行的服务并观察所做的更改。
1. 依次单击“调试”和“停止调试”以停止应用程序   。

## <a name="setting-and-using-breakpoints-for-debugging"></a>设置并使用用于调试的断点

使用“启动服务器(AZDS)”启动服务。 

依次单击“视图”、“资源管理器”，导航回到“资源管理器”视图。    打开 `server.js` 并单击第 13 行的某处，以将光标置于该处。 若要设置断点，请按 *F9*，或者依次单击“调试”、“切换断点”。  

在浏览器中打开服务，你会发现未显示任何消息。 返回 Visual Studio Code，并观察到第 13 行突出显示。 你设置的断点已在第 13 行暂停了服务。 若要恢复服务，请按 *F5*，或者依次单击“调试”、“继续”。   返回浏览器，你会发现，现在显示了消息。

在附加调试器的情况下在 Kubernetes 中运行服务时，你对调试信息（例如调用堆栈、局部变量和异常信息）拥有完全访问权限。

通过将光标置于 `server.js` 中的第 13 行并点击 *F9* 来删除断点。

依次单击“调试”、“停止调试”以停止调试器。  

## <a name="update-code-from-visual-studio-code"></a>在 Visual Studio Code 中更新代码

将调试模式更改为“附加到服务器(AZDS)”并启动服务： 

![](media/get-started-node/attach-nodejs.png)

此命令在 Azure Dev Spaces 中生成并运行你的服务。 此外，它还会在服务的容器中启动 [nodemon](https://nodemon.io) 进程，并将 VS Code 附加到该进程。 如果源代码发生了更改，*nodemon* 进程允许自动重启，这样可以加速内部循环的开发，就如同在本地计算机上进行开发一样。

启动服务后，使用浏览器导航到该服务并与之交互。

当服务正在运行时，返回 VS Code 并更新 `server.js` 中的第 13 行。 例如：
```javascript
    res.send('Hello from webfrontend in Azure while debugging!');
```

保存文件并在浏览器中返回到你的服务。 与服务交互，你会发现已显示更新的消息。

运行 *nodemon* 时，一旦检测到任何代码更改，Node 进程就会自动重启。 此自动重启进程类似于在本地计算机上编辑和重启服务，可以提供内部循环开发体验。

## <a name="clean-up-your-azure-resources"></a>清理 Azure 资源

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

了解 Azure Dev Spaces 如何帮助开发跨多个容器的更复杂应用程序，以及如何通过在不同的空间中使用不同的代码版本或分支来简化协作式开发。

> [!div class="nextstepaction"]
> [使用多个容器和团队开发](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations
