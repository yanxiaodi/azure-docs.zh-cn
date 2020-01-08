---
title: 快速入门 - 将 Hello World 部署到 Azure Service Fabric 网格 | Microsoft Docs
description: 本快速入门展示了如何将 Service Fabric 网格应用程序部署到 Azure Service Fabric 网格。
services: service-fabric-mesh
keywords: 未咨询 SEO 专家的情况下，请不要添加或编辑关键字。
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 5ca622602c71976917a07005bf349dd98086327c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336977"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>快速入门：将 Hello World 部署到 Service Fabric 网格

使用 [Service Fabric 网格](service-fabric-mesh-overview.md)，可以轻松在 Azure 中创建和管理微服务应用程序，不需要预配虚拟机。 在本快速入门中，你将在 Azure 中创建一个 Hello World 应用程序并将其公开到 Internet。 此操作通过单个命令完成。 只需要几分钟时间，便可以在浏览器中看到此视图：

![浏览器中的 Hello World 应用][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

如果还没有 Azure 帐户，请在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="set-up-service-fabric-mesh-cli"></a>设置 Service Fabric 网格 CLI 
可以使用 Azure Cloud Shell 或 Azure CLI 的本地安装完成本快速入门。 根据这些[说明](service-fabric-mesh-howto-setup-cli.md)安装 Azure Service Fabric 网格 CLI 扩展模块。

## <a name="sign-in-to-azure"></a>登录 Azure
登录到 Azure 并设置订阅。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>创建资源组
创建要将应用程序部署到其中的资源组。 可以使用现有资源组并跳过此步骤。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>部署应用程序
使用 `az mesh deployment create` 命令在资源组中创建应用程序。  运行以下内容：

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

上面的命令使用 [linux.json 模板](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json)部署 Linux 应用程序。 若要部署 Windows 应用程序，请使用 [windows.json 模板](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json)。 Windows 容器映像大于 Linux 容器映像，可能需要更多时间进行部署。

此命令将生成如下所示的 JSON 代码片段。 在 JSON 输出的 ```outputs``` 部分下，复制 ```publicIPAddress``` 属性。

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

此信息来自 ARM 模板中的 ```outputs``` 节。 如下所示，此节引用网关资源来获取公共 IP 地址。 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>打开应用程序
在应用程序成功部署后，从 CLI 输出中复制服务终结点的公用 IP 地址。 在 Web 浏览器中打开该 IP 地址。 此时将显示包含 Azure Service Fabric 网格徽标的网页。

## <a name="check-the-application-details"></a>检查应用程序详细信息
可以使用 `az mesh app show` 命令来验证应用程序的状态。 此命令提供你可以跟进的有用信息。

对于本快速入门，应用程序名称为 `helloWorldApp`，若要收集该应用程序的详细信息，请执行以下命令：

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>查看应用程序日志

使用 `az mesh code-package-log get` 命令查看所部署的应用程序的日志：

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>清理资源

在准备好删除应用程序后，运行 [az group delete][az-group-delete] 命令删除资源组及其包含的应用程序和网络资源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

若要了解有关创建和部署 Service Fabric 网格应用程序的详细信息，请继续学习教程。
> [!div class="nextstepaction"]
> [创建并部署多服务 Web 应用程序](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
