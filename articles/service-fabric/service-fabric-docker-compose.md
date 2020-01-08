---
title: Azure Service Fabric Docker Compose 部署预览版
description: Azure Service Fabric 接受 Docker Compose 格式，因此可以更轻松地安排使用 Service Fabric 的现有容器。 这种支持目前处于预览状态。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: de02c9a8580527ab708418aa266f1b56411fb95b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599578"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Azure Service Fabric 中的 Docker Compose 部署支持（预览版）

Docker 使用 [docker-compose.yml](https://docs.docker.com/compose) 文件定义多容器应用程序。 为了让客户轻松地熟练使用 Docker 来安排 Azure Service Fabric 中的现有容器应用程序，我们在平台中添加了对 Docker Compose 部署的本机预览支持。 Service Fabric 可接受 `docker-compose.yml` 文件的版本 3 和更高版本。 

由于这种支持处于预览状态，因此仅支持一部分 Compose 指令。 例如，不支持应用程序升级。 但是，始终可以删除并部署应用程序，而不是对其进行升级。

若要使用此预览版，请通过 Azure 门户以及相应的 SDK 使用 5.7 版本或更高版本的 Service Fabric 运行时创建群集。 

> [!NOTE]
> 此功能处于预览状态，在生产环境中不受支持。
> 以下示例基于运行时版本 6.0 和 SDK 版本 2.8。

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>在 Service Fabric 上部署一个 Docker Compose 文件

以下命令创建一个 Service Fabric 应用程序（名为 `fabric:/TestContainerApp`），可以像对任何其他 Service Fabric 应用程序一样对该应用程序进行监视和托管。 指定的应用程序名称可用于运行状况查询。
Service Fabric 会将“DeploymentName”识别为 Compose 部署的标识符。

### <a name="use-powershell"></a>使用 PowerShell

通过在 PowerShell 中运行以下命令，根据 docker-compose.yml 文件创建 Service Fabric Compose 部署：

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` 和 `RegistryPassword` 指容器注册表用户名和密码。 创建完部署后，可以使用以下命令检查其状态：

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

若要通过 PowerShell 删除 Compose 部署，请使用以下命令：

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

若要通过 PowerShell 启动 Compose 部署升级，请使用以下命令：

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

若要通过 PowerShell 回滚 Compose 部署升级，请使用以下命令：

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

接受升级之后，可以使用以下命令跟踪升级进度：

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>使用 Azure Service Fabric CLI (sfctl)

或者，可以使用以下 Service Fabric CLI 命令：

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

创建部署后，可以使用以下命令检查其状态：

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

若要删除 Compose 部署，请使用以下命令：

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

若要启动 Compose 部署升级，请使用以下命令：

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

若要回滚 Compose 部署升级，请使用以下命令：

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

接受升级之后，可以使用以下命令跟踪升级进度：

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>支持的 Compose 指令

此预览版支持 Compose 版本 3 格式中的一部分配置选项，包括以下基元：

* 服务 > 部署 > 副本
* 服务 > 部署 > 放置 > 约束
* 服务 > 部署 > 资源 > 限制
    * -cpu-shares
    * -memory
    * -memory-swap
* 服务 > 命令
* 服务 > 环境
* 服务 > 端口
* 服务 > 映像
* 服务 > 隔离（仅适用于 Windows）
* 服务 > 日志记录 > 驱动程序
* 服务 > 日志记录 > 驱动程序 > 选项
* 卷和部署 > 卷

将群集设置为强制实施 [Service Fabric 资源调控](service-fabric-resource-governance.md)中所述的资源限制。 所有其他 Docker Compose 指令均不受此预览版支持。

### <a name="ports-section"></a>端口部分

指定 Service Fabric 侦听程序将使用“端口”部分中的 http 还是 https 协议。 这将确保使用命名服务正确发布终结点协议，以允许反向代理转发请求：
* 若要路由到不安全的 Service Fabric Compose 服务，请指定 /http。 例如“80:80/http”。
* 若要路由到安全的 Service Fabric Compose 服务，请指定 /https。 例如“443:443/https”。

> [!NOTE]
> /http 和 /https 端口部分语法特定于 Service Fabric，用于注册正确的 Service Fabric 侦听程序 URL。  如果以编程方式验证 Docker Compose 文件语法，则可能导致验证错误。

## <a name="servicednsname-computation"></a>ServiceDnsName 计算

如果 Compose 文件中指定的服务名称是完全限定的域名（也就是说，它包含一个句点 [.]），则由 Service Fabric 注册的 DNS 名称为 `<ServiceName>`（包含句点）。 如果不是，则应用程序名称中的每个路径段都会成为服务 DNS 名称中的域标签，其中第一个路径段成为顶级域标签。

例如，如果指定的应用程序名称为 `fabric:/SampleApp/MyComposeApp`，则 `<ServiceName>.MyComposeApp.SampleApp` 将是注册的 DNS 名称。

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Compose 部署（实例定义）与 Service Fabric 应用模型（类型定义）

docker-compose.yml 文件描述一组包括属性和配置在内的可部署容器。
例如，该文件可以包含环境变量和端口。 还可以在 docker-compose.yml 文件中指定放置约束、资源限制和 DNS 名称等部署参数。

[Service Fabric 应用程序模型](service-fabric-application-model.md)使用服务类型和应用程序类型，在此模型中，可以有相同类型的多个应用程序实例。 例如，每个客户一个应用程序实例。 此基于类型的模型支持向运行时注册相同应用程序类型的多个版本。

例如，客户 A 可以有一个使用 1.0 类型的 AppTypeA 实例化的应用程序，而客户 B 可以有另一个使用相同类型和版本实例化的应用程序。 在应用程序清单中定义应用程序类型，在创建应用程序时指定应用程序名称和部署参数。

虽然此模型具有较大灵活性，但我们还是计划支持一种更简单的基于实例的部署模型（其类型隐含在清单文件中）。 在此模型中，每个应用程序都会获取自己独立的清单。 我们将通过增加对 docker-compose.yml 的支持，实现对此模型（采用基于实例的部署格式）的预览支持。

## <a name="next-steps"></a>后续步骤

* 了解 [Service Fabric 应用程序模型](service-fabric-application-model.md)
* [Service Fabric CLI 入门](service-fabric-cli.md)
