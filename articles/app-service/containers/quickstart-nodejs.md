---
title: 创建 Node.js Web 应用 - Azure 应用服务
description: 如何将 Node.js 应用部署到 Azure 应用服务
author: msangapu
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: b28e8e4dccf75d36b318e838e35de23d176c5c23
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176729"
---
# <a name="create-a-nodejs-app-in-azure"></a>在 Azure 中创建 Node.js 应用

Azure 应用服务提供高度可缩放、自修补的 Web 托管服务。 本快速入门演示如何将 Node.js 应用部署到 Azure 应用服务中。

## <a name="prerequisites"></a>先决条件

如果你没有 Azure 帐户，请[立即注册](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)一个免费帐户来试用任何服务组合，并获得 200 美元的 Azure 信用额度。

需要连同 [Node.js 和 npm](https://nodejs.org/en/download)（Node.js 包管理器）一起安装 [Visual Studio Code](https://code.visualstudio.com/)。

还需要安装 [Azure 应用服务扩展](vscode:extension/ms-azuretools.vscode-azureappservice)，使用该扩展可在 Azure 平台即服务 (PaaS) 上创建、管理和部署 Linux Web 应用。

### <a name="sign-in"></a>登录

安装该扩展后，登录到你的 Azure 帐户。 在活动栏中，单击 Azure 徽标显示“AZURE 应用服务”资源管理器。  单击“登录到 Azure...”并遵照说明操作。 

![登录到 Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>故障排除

如果出现错误“找不到名为 [订阅 ID] 的订阅”，原因可能是你使用了代理，因此无法访问 Azure API。  在终端中使用 `export` 以代理信息配置 `HTTP_PROXY` 和 `HTTPS_PROXY` 环境变量。

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

如果设置环境变量不能解决该问题，请单击下面的“我遇到了问题”按钮联系我们。 

### <a name="prerequisite-check"></a>先决条件检查

在继续之前，请确保已安装并配置了所有必备组件。

在 VS Code 的状态栏中应会显示你的 Azure 电子邮件地址，“AZURE 应用服务”资源管理器中应会显示你的订阅。 

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>创建 Node.js 应用程序

接下来，创建可部署到云的 Node.js 应用程序。 本快速入门使用一个应用程序生成器从终端快速搭建应用程序。

> [!TIP]
> 如果你已完成 [Node.js 教程](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)，可直接跳转到[部署网站](#deploy-the-website)。

### <a name="install-the-express-generator"></a>安装 Express 生成器

[Express](https://www.expressjs.com) 是用于生成和运行 Node.js 应用程序的流行框架。 可以使用 [Express 生成器](https://expressjs.com/en/starter/generator.html)工具搭建（创建）新的 Express 应用程序。 Express 生成器以 npm 模块的形式提供，可使用 npm 命令行工具 `npm` 安装它。

```bash
npm install -g express-generator
```

使用 `-g` 开关可在计算机上全局安装 Express 生成器，以便可从任意位置运行它。

### <a name="scaffold-a-new-application"></a>搭建新应用程序

接下来，运行以下命令搭建名为 `myExpressApp` 的新 Express 应用程序：

```bash
express myExpressApp --view pug --git
```

`--view pug --git` 参数告知生成器使用 [pug](https://pugjs.org/api/getting-started.html) 模板引擎（以前称为 `jade`）并创建 `.gitignore` 文件。

若要安装应用程序的所有依赖项，请转到新文件夹并运行 `npm install`。

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>运行应用程序

接下来，确保运行应用程序。 在终端中，使用 `npm start` 命令启动该应用程序以启动服务器。

```bash
npm start
```

现在，打开浏览器并导航到 [http://localhost:3000](http://localhost:3000)，其中应会显示如下所示的内容：

![运行 Express 应用程序](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>部署网站

在本部分，你将使用 VS Code 和 Azure 应用服务扩展部署 Node.js 网站。 本快速入门使用最基本的部署模型，其中，应用程序将被压缩并部署到 Linux 上的 Azure Web 应用。

### <a name="deploy-using-azure-app-service"></a>使用 Azure 应用服务进行部署

首先，在 VS Code 中打开应用程序文件夹。

```bash
code .
```

在“AZURE 应用服务”资源管理器中，单击蓝色的向上箭头图标，将应用部署到 Azure。 

![部署到 Web 应用](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> 也可以从**命令面板** (CTRL + SHIFT + P) 进行部署，方法是键入“deploy to web app”并运行“Azure App Service:  Deploy to Web App”命令。

1. 选择当前已打开的目录 `myExpressApp`。

2. 选择“创建新 Web 应用”  。

3. 键入 Web 应用的全局唯一名称，然后按 ENTER。 应用名称的有效字符为“a-z”、“0-9”和“-”。

4. 选择 **Node.js 版本**，建议使用“LTS”。

    通知通道将显示正在为应用创建 Azure 资源。

当系统提示是否更新配置以在目标服务器上运行 `npm install` 时，请单击“是”。  随后将部署应用。

![配置的部署](./media/quickstart-nodejs/server-build.png)

部署开始后，系统会提示更新工作区，使以后的部署自动针对相同的应用服务 Web 应用。 选择“是”，以确保将更改部署到正确的应用。 

![配置的部署](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> 确保应用程序正在侦听 PORT 环境变量 `process.env.PORT` 提供的端口。

### <a name="browse-the-website"></a>浏览网站

部署完成后，单击提示中的“浏览网站”以查看全新部署的网站。 

### <a name="troubleshooting"></a>故障排除

如果看到错误“你无权查看此目录或页面”，则表示应用程序可能无法正常启动。  请转到下一部分，查看日志输出以找到并修复错误。 如果无法解决此问题，请单击下面的“我遇到了问题”按钮联系我们。  我们很乐意为你提供帮助。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>更新网站

可以使用相同的过程来部署对此应用所做的更改，并选择现有应用而不是创建新应用。

## <a name="viewing-logs"></a>查看日志

本部分介绍如何查看（或“跟踪”）正在运行的网站发出的日志。 对站点中的 `console.log` 发出的任何调用将显示在 Visual Studio Code 的输出窗口中。

在“AZURE 应用服务”资源管理器中找到该应用，右键单击该应用，然后选择“查看流日志”。  

出现提示时，请选择启用日志记录并重启应用程序。 重启应用后，VS Code 输出窗口将会打开，其中包含与日志流建立的连接。

![查看流日志](./media/quickstart-nodejs/view-logs.png)

![启用日志记录并重启](./media/quickstart-nodejs/enable-restart.png)

几秒钟后，你将看到一条消息，指出已连接到日志流服务。 请多次刷新页面以查看更多活动。

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    ```

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>后续步骤

祝贺你，你现已成功完成本快速入门！

接下来请查看其他 Azure 扩展。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker 工具](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI 工具](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

或安装 [Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 扩展包获取所有这些工具。
