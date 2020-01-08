---
title: 创建 PHP Web 应用 - Azure 应用服务 | Microsoft Docs
description: 数分钟内在 Azure 应用服务 Web 应用中部署首个 PHP Hello World。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 90f83e3e6fa1910641b3e384460cf4aa14016c1a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067330"
---
# <a name="create-a-php-web-app-in-azure"></a>在 Azure 中创建 PHP Web 应用

> [!NOTE]
> 本文将应用部署到 Windows 上的应用服务。 若要部署到基于 _Linux_ 的应用服务，请参阅[在基于 Linux 的应用服务中创建 PHP Web 应用](./containers/quickstart-php.md)。
>

[Azure 应用服务](overview.md)提供高度可缩放、自修补的 Web 托管服务。  本快速入门教程演示如何将 PHP 应用部署到 Azure 应用服务。 在 Cloud Shell 中使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 创建 Web 应用，并使用 Git 将 PHP 代码示例部署到该 Web 应用。

![在 Azure 中运行应用的示例](media/app-service-web-get-started-php/hello-world-in-browser.png)

可以在 Mac、Windows 或 Linux 计算机上执行此处的步骤。 安装先决条件后，大约需要五分钟完成这些步骤。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本快速入门教程需要：

* <a href="https://git-scm.com/" target="_blank">安装 Git</a>
* <a href="https://php.net/manual/install.php" target="_blank">安装 PHP</a>

## <a name="download-the-sample-locally"></a>将示例下载到本地

在终端窗口中运行以下命令。 这样会将示例应用程序克隆到本地计算机，并导航到包含示例代码的目录。 

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>在本地运行应用

在本地运行应用程序，这样就能了解将它部署到 Azure 时它的外观应该是什么样的。 打开一个终端窗口并使用 `php` 命令启动内置的 PHP Web 服务器。

```bash
php -S localhost:8080
```

打开 Web 浏览器并导航到 `http://localhost:8080` 处的示例应用。

此时会看到来自示例应用的 Hello World!  消息显示在页面中。

![本地运行应用的示例](media/app-service-web-get-started-php/localhost-hello-world-in-browser.png)

在终端窗口中，按 **Ctrl+C** 退出 Web 服务器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>创建 Web 应用

在 Cloud Shell 中，使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 命令在 `myAppServicePlan` 应用服务计划中创建一个 Web 应用。 

在以下示例中，将 `<app_name>` 替换为全局唯一的应用名称（有效字符是 `a-z`、`0-9` 和 `-`）。 运行时设置为 `PHP|7.0`。 若要查看所有受支持的运行时，请运行 [`az webapp list-runtimes`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)。 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "PHP|7.0" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "PHP|7.0" --deployment-local-git
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```
现在你已经创建了一个新的空 Web 应用并启用了 Git 部署。

> [!NOTE]
> Git 远程的 URL 将显示在 `deploymentLocalGitUrl` 属性中，其格式为 `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`。 保存此 URL，因为后面需要它。
>

浏览到新建的 Web 应用。 将 _&lt;app name>_ 替换为在上一步中创建的唯一应用名称。

```bash
http://<app name>.azurewebsites.net
```

新 Web 应用应该如下所示：

![空 Web 应用页面](media/app-service-web-get-started-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>浏览到应用

使用 Web 浏览器浏览到已部署的应用程序。

```
http://<app_name>.azurewebsites.net
```

PHP 示例代码在 Azure 应用服务的 Web 应用中运行。

![在 Azure 中运行应用的示例](media/app-service-web-get-started-php/hello-world-in-browser.png)

**祝贺你！** 现已将第一个 PHP 应用部署到应用服务。

## <a name="update-locally-and-redeploy-the-code"></a>在本地更新并重新部署代码

使用本地文本编辑器打开 PHP 应用中的 `index.php` 文件，并对 `echo` 旁边的字符串中的文本稍微进行更改：

```php
echo "Hello Azure!";
```

在本地终端窗口中，提交在 Git 中所做的更改，然后将代码更改推送到 Azure。

```bash
git commit -am "updated output"
git push azure master
```

完成部署后，返回到“浏览到应用”步骤期间打开的浏览器窗口，然后刷新页面。 

![已更新的在 Azure 中运行应用的示例](media/app-service-web-get-started-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>管理新的 Azure 应用

转到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>管理创建的 Web 应用。

在左侧菜单中单击“应用程序服务”  ，然后单击 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/app-service-web-get-started-php/php-docs-hello-world-app-service-list.png)

此时将显示 Web 应用的概述页。 在此处可以执行基本的管理任务，例如浏览、停止、启动、重启和删除。

![Azure 门户中的应用服务页](media/app-service-web-get-started-php/php-docs-hello-world-app-service-detail.png)

左侧菜单提供用于配置应用的不同选项。 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [带 MySQL 的 PHP](app-service-web-tutorial-php-mysql.md)
