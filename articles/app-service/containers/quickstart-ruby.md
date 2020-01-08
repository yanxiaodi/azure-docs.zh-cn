---
title: 在 Linux 上创建 Ruby Web 应用 - Azure 应用服务 | Microsoft Docs
description: 了解如何使用 Linux 应用服务创建 Ruby on Rails 应用。
keywords: azure 应用服务, linux, oss, ruby, rails
services: app-service
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/11/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: cfcea4c4450ea806b36d1d46c17c6676f7d92893
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071066"
---
# <a name="create-a-ruby-on-rails-app-in-app-service-on-linux"></a>在 Linux 应用服务中创建 Ruby on Rails 应用

[Linux 应用服务](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本快速入门教程介绍了如何使用 [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 将 Ruby on Rails 应用部署到 Linux 上的 Azure 应用服务。

> [!NOTE]
> Ruby 开发堆栈目前仅支持 Ruby on Rails。 如果想要使用不同的平台（如 Sinatra），或者想要使用[不受支持的 Ruby 版本](app-service-linux-intro.md)，则需[在自定义容器中运行它](quickstart-docker-go.md)。

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">安装 Ruby 2.6 或更高版本</a>
* <a href="https://git-scm.com/" target="_blank">安装 Git</a>

## <a name="download-the-sample"></a>下载示例

在终端窗口中，运行以下命令，将示例应用存储库克隆到本地计算机：

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>在本地运行应用程序

在本地运行应用程序，以便你能了解将它部署到 Azure 时它的外观应该是什么样的。 打开终端窗口，转到 `hello-world` 目录，然后使用 `rails server` 命令启动该服务器。

第一步是安装必需的 gem。 示例中包含了 `Gemfile`，因此只需运行以下命令：

```bash
bundle install
```

安装 gem 后，我们将使用捆绑程序启动应用：

```bash
bundle exec rails server
```

使用 Web 浏览器导航到 `http://localhost:3000` 以在本地测试该应用。

![已配置了 Hello World](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

浏览到该应用，查看使用内置映像新建的 Web 应用。 将 _&lt;应用名称>_ 替换为 Web 应用名称。

```bash
http://<app_name>.azurewebsites.net
```

新 Web 应用应该如下所示：

![启动页面](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>部署应用程序

运行以下命令将本地应用程序部署到 Azure Web 应用：

```bash
git remote add azure <Git deployment URL from above>
git push azure master
```

确认远程部署操作报告了成功消息。 命令生成的输出类似于以下文本：

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  master -> master
```

部署完成后，请等待大约 10 秒，然后重启 Web 应用，再导航到 Web 应用并验证结果。

```bash
http://<app-name>.azurewebsites.net
```

![更新的 Web 应用](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> 应用重启时，可能会在浏览器中看到 HTTP 状态代码 `Error 503 Server unavailable` 或在默认页面中看到 `Hey, Ruby developers!`。 可能需要花费几分钟时间才能完全重启应用。
>

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：Ruby on Rails 与 Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [配置 Ruby 应用](configure-language-ruby.md)
