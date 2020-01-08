---
title: Azure CLI 脚本示例 - 使用应用服务创建 SignalR 服务
description: Azure CLI 脚本示例 - 使用应用服务创建 SignalR 服务
author: sffamily
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: d0f0747aa393475265be4aeb9ca05000fbd5b97b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565752"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>使用应用服务创建 SignalR 服务

此示例脚本会创建新的 Azure SignalR 服务资源，用于将实时内容更新推送到客户端。 此脚本还会添加新的 Web 应用和应用服务计划，以托管使用 SignalR 服务的 ASP.NET Core Web 应用。 Web 应用通过名为“AzureSignalRConnectionString”的应用设置进行了配置，以便连接到新的 SignalR 服务资源  。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

## <a name="sample-script"></a>示例脚本

此脚本使用适用于 Azure CLI 的 signalr 扩展  。 使用此示例脚本前，执行以下命令，安装适用于 Azure CLI 的 signalr 扩展  ：

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

记下为新资源组生成的实际名称。 它将在输出中显示。 如果要删除所有组资源，将使用该资源组名称。

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

表中的每条命令均链接到特定于命令的文档。 此脚本使用以下命令：

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | 创建 Azure SignalR 服务资源。 |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | 列出密钥，使用 SignalR 推送实时内容更新时，应用程序将使用这些密钥。 |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | 创建用于托管 Web 应用的 Azure 应用服务计划。 |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | 使用应用服务托管计划创建 Azure Web 应用。 |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | 为 Web 应用添加新应用设置。 此应用设置用于存储 SignalR 连接字符串。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可在 [Azure SignalR 服务文档](../signalr-reference-cli.md)中找到其他 Azure SignalR 服务 CLI 脚本示例。
