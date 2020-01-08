---
title: 在 Linux 上使用 C# 创建第一个 Azure Service Fabric 应用 | Microsoft Docs
description: 了解如何使用 C# 和 .NET Core 2.0 创建和部署 Service Fabric 应用程序。
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/11/2018
ms.author: subramar
ms.openlocfilehash: 7bb9b1c545d30f66a09817ba977f12666a9f41c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394283"
---
# <a name="create-your-first-azure-service-fabric-application"></a>创建第一个 Azure Service Fabric 应用程序
> [!div class="op_single_selector"]
> * [Java - Linux（预览版）](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux（预览版）](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric 提供用于在 Linux 上使用 .NET Core 和 Java 构建服务的 SDK。 本教程介绍如何在 .NET Core 2.0 中使用 C# 创建适用于 Linux 的应用程序和生成服务。

## <a name="prerequisites"></a>必备组件
开始之前，请确保已[设置 Linux 开发环境](service-fabric-get-started-linux.md)。 如果使用的是 Mac OS X，则可以[使用 Vagrant 在虚拟机中设置 Linux 单机环境](service-fabric-get-started-mac.md)。

还需要安装 [Service Fabric CLI](service-fabric-cli.md)

### <a name="install-and-set-up-the-generators-for-c"></a>为 C# 安装和设置生成器
Service Fabric 提供基架工具，可以借助此类工具，使用 Yeoman 模板生成器从终端创建 Service Fabric 应用程序。 遵循以下步骤安装适用于 C# 的 Service Fabric Yeoman 模板生成器：

1. 在计算机上安装 nodejs 和 NPM

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. 通过 NPM 在计算机上安装 [Yeoman](https://yeoman.io/) 模板生成器

   ```bash
   npm install -g yo
   ```
3. 通过 NPM 安装 Service Fabric Yeoman C# 应用程序生成器

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>创建应用程序
Service Fabric 应用程序可以包含一个或多个服务，每个服务都在提供应用程序功能时具有特定角色。 用于 C# 的 Service Fabric [Yeoman](https://yeoman.io/) 生成器是在上一步安装的，利用它可以轻松地创建第一个服务，以及在以后添加其他服务。 让我们使用 Yeoman 来创建具有单项服务的应用程序。

1. 在终端中键入以下命令以开始生成基架：`yo azuresfcsharp`
2. 为应用程序命名。
3. 选择第一个服务的类型并将其命名。 对于本教程，我们会选择“可靠角色服务”。

   ![适用于 C# 的 Service Fabric Yeoman 生成器][sf-yeoman]

> [!NOTE]
> 有关选项的详细信息，请参阅 [Service Fabric 编程模型概述](service-fabric-choose-framework.md)。
>
>

## <a name="build-the-application"></a>构建应用程序
Service Fabric Yeoman 模板包含一个可用于（在导航到应用程序文件夹后）从终端生成应用的生成脚本。

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>部署应用程序

生成应用程序后，可以将其部署到本地群集。

1. 连接到本地 Service Fabric 群集。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. 运行模板中提供的安装脚本可将应用程序包复制到群集的映像存储区、注册应用程序类型和创建应用程序实例。

    ```bash
    ./install.sh
    ```

部署生成的应用程序时，其方式与部署任何其他 Service Fabric 应用程序相同。 如需详细的说明，请参阅相关文档，了解如何[使用 Service Fabric CLI 管理 Service Fabric 应用程序](service-fabric-application-lifecycle-sfctl.md)。

这些命令的参数可以在应用程序包内的已生成清单中找到。

应用程序部署完以后，请打开浏览器并导航到 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)，其地址为 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)。 然后，展开“应用程序”  节点，注意现在有一个条目是用于你的应用程序类型，另一个条目用于该类型的第一个实例。

> [!IMPORTANT]
> 必须将证书配置为向 Service Fabric 运行时验证应用程序，才能将应用程序部署到 Azure 中的安全 Linux 群集。 这样做可允许 Reliable Services 服务与基础 Service Fabric 运行时 API 通信。 若要了解详细信息，请参阅[将 Reliable Services 应用程序配置为在 Linux 群集上运行](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)。  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>启动测试客户端并执行故障转移
Actor 项目自身未执行任何操作。 它们需要另一个服务或客户端向其发送消息。 Actor 模板包括可用于与 actor 服务进行交互的简单测试脚本。

1. 使用监视实用程序运行该脚本来查看 actor 服务的输出。

   对于 MAC OS X，你需要通过运行以下附加命令将 myactorsvcTestClient 文件夹复制到容器内的同一位置。
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. 在 Service Fabric Explorer 中，找到承载 actor 服务的主副本的节点。 以下屏幕截图中显示的是节点 3。

    ![在 Service Fabric Explorer 中查找主副本][sfx-primary]
3. 单击上一步找到的节点，并在“操作”菜单中选择“停用(重启)”  。 此操作在本地群集中重新启动一个节点，从而强制故障转移到在另一个节点上运行的一个辅助副本。 在执行此操作时，请注意来自测试客户端的输出，并注意虽然发生故障转移，但是计数器仍将继续递增。

## <a name="adding-more-services-to-an-existing-application"></a>将更多服务添加到现有应用程序

要将其他服务添加到使用 `yo` 创建的应用程序，请执行以下步骤：
1. 将目录更改为现有应用程序的根目录。  例如，如果 `MyApplication` 是 Yeoman 创建的应用程序，则使用 `cd ~/YeomanSamples/MyApplication`。
2. 运行 `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>后续步骤

* [使用 Service Fabric CLI 与 Service Fabric 群集交互](service-fabric-cli.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)
* [Service Fabric CLI 入门](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
