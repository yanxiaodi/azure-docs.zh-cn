---
title: 如何将 Azure SignalR 服务事件发送到事件网格
description: 本指南介绍如何为 SignalR 服务启用事件网格事件, 然后将客户端连接连接/断开连接的事件发送到示例应用程序。
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 100c7120889f88c1bab3418822835e8d4ece9826
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839295"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>如何将事件从 Azure SignalR Service 发送到事件网格

Azure 事件网格是一种完全托管的事件路由服务, 它使用 pub 子模型提供统一的事件消耗。 在本指南中, 将使用 Azure CLI 创建 Azure SignalR 服务、订阅连接事件, 并部署一个示例 web 应用程序来接收事件。 最后, 你可以连接和断开连接, 并在示例应用程序中查看事件负载。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][azure-account]。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

本文中的 Azure CLI 命令已根据 **Bash** shell 设置了格式。 如果使用其他 shell（例如 PowerShell 或命令提示符），则可能需要相应地调整行连续字符或变量赋值行。 本文使用变量来最大程度地减少所需的命令编辑量。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下[az group create][az-group-create]命令会在*eastus*区域中创建名为*myResourceGroup*的资源组。 若要对资源组使用不同的名称，请将 `RESOURCE_GROUP_NAME` 设置为不同的值。

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>创建 SignalR 服务

接下来, 使用以下命令将 Azure Signalr 服务部署到资源组。
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

创建 SignalR 服务后, Azure CLI 将返回类似于下面的输出:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>创建事件终结点

在本部分，我们使用 GitHub 存储库中的资源管理器模板，将一个预生成的示例 Web 应用程序部署到 Azure 应用服务。 稍后，我们将订阅该注册表的事件网格事件，并将此应用指定为事件要发送到的终结点。

若要部署示例应用，请将 `SITE_NAME` 设置为 Web 应用的唯一名称，并执行以下命令。 站点名称在 Azure 中必须唯一，因为它是 Web 应用的完全限定域名 (FQDN) 的一部分。 在稍后的部分，我们将在 Web 浏览器中导航到应用的 FQDN，以查看注册表的事件。

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

部署成功后 (可能需要几分钟的时间), 打开浏览器并导航到 web 应用, 确保其正在运行:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>订阅注册表事件

在事件网格中订阅一个主题，以告知你要跟踪哪些事件，以及要将事件发送到何处。 以下[az eventgrid event-订阅 create][az-eventgrid-event-subscription-create]命令订阅你创建的 Azure SignalR 服务, 并将你的 web 应用的 URL 指定为它应将事件发送到的终结点。 此处可以重复使用在前面几个部分填充的环境变量，因此无需进行编辑。

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

完成订阅后，应会看到如下所示的输出：

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>触发注册表事件

切换到服务模式`Serverless Mode` , 并设置与 SignalR 服务的客户端连接。 您可以采用[无服务器示例](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless)作为参考。

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>查看注册表事件

现已将客户端连接到 SignalR 服务。 导航到事件网格查看器 web 应用, 应会看到一个`ClientConnectionConnected`事件。 如果终止该客户端, 则还会看到一个`ClientConnectionDisconnected`事件。

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
