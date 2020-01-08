---
title: 在 Linux 上创建 Azure Service Fabric Reliable Actors 应用程序 | Microsoft Docs
description: 了解如何在五分钟内创建并部署一个 Java Service Fabric Reliable Actors 应用程序。
services: service-fabric
documentationcenter: java
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2018
ms.author: atsenthi
ms.openlocfilehash: 4b008c001e1c4749b6ab6f9f21eff479f007c05c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68599679"
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>在 Linux 上创建第一个 Java Service Fabric Reliable Actors 应用程序
> [!div class="op_single_selector"]
> * [Java-Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

借助本快速入门，只需几分钟即可在 Linux 开发环境中创建第一个 Azure Service Fabric Java 应用程序。  完成后，即拥有一个在本地开发群集上运行的简单 Java 单一服务应用程序。  

## <a name="prerequisites"></a>先决条件
开始之前，请安装 Service Fabric SDK、Service Fabric CLI、Yeoman，设置 Java 开发环境，并在 [Linux 开发环境](service-fabric-get-started-linux.md)中设置开发群集。 如果使用 Mac OS X，则可[使用 Docker 在 Mac 上设置开发环境](service-fabric-get-started-mac.md)。

另请安装 [Service Fabric CLI](service-fabric-cli.md)。

### <a name="install-and-set-up-the-generators-for-java"></a>为 Java 安装和设置生成器
Service Fabric 提供基架工具，可以借助此类工具，使用 Yeoman 模板生成器从终端创建 Service Fabric Java 应用程序。  如果尚未安装 Yeoman，请参阅 [Service Fabric 入门（使用 Linux）](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)，获取有关如何设置 Yeoman 的说明。 运行以下命令，安装用于 Java 的 Service Fabric Yeoman 模板生成器。

  ```bash
  npm install -g generator-azuresfjava
  ```

## <a name="basic-concepts"></a>基本概念
若要开始使用 Reliable Actors，只需了解几个基本概念：

* **执行组件服务**。 可以在 Service Fabric 基础结构中部署的 Reliable Services 中封装了 Reliable Actors。 执行组件服务在命名的服务实例中激活。
* **执行组件注册**。 与 Reliable Services 一样，Reliable Actor 服务也需要注册到 Service Fabric 运行时。 此外，需要将执行组件类型注册到执行组件运行时。
* **执行组件接口**。 执行组件接口用于定义执行组件的强类型公共接口。 在 Reliable Actor 模型术语中，执行组件接口用于定义执行组件可以理解并处理的消息类型。 其他执行组件或客户端应用程序使用此执行组件接口将消息“发送”到（异步方式）此执行组件。 Reliable Actors 可实现多个接口。
* **ActorProxy 类**。 客户端应用程序使用 ActorProxy 类调用通过执行组件接口公开的方法。 ActorProxy 类提供两个重要功能：
  
  * 名称解析：能够在群集中找到执行组件（查找托管它的群集节点）。
  * 故障处理：例如，在需要将执行组件重新定位到群集中另一个节点的故障之后，它可以重试方法调用和重新解析执行组件的位置。

有必要提一下以下与执行组件接口有关的规则：

* 不能重载执行组件接口方法。
* 执行组件接口方法不能有 out、ref 或可选参数。
* 不支持泛型接口。

## <a name="create-the-application"></a>创建应用程序
Service Fabric 应用程序包含一个或多个服务，每个服务都在提供应用程序功能时具有特定角色。 有了在上一部分安装的生成器，就可以轻松地创建第一个服务，并在以后添加更多服务。  此外，还可使用适用于 Eclipse 的插件创建、生成和部署 Service Fabric Java 应用程序。 请参阅[使用 Eclipse 创建和部署第一个 Java 应用程序](service-fabric-get-started-eclipse.md)。 对于本快速入门，使用 Yeoman 创建具有单项服务的应用程序，该服务用于存储和获取获取计数器值。

1. 在终端中，键入 ``yo azuresfjava``。
2. 命名应用程序。
3. 选择第一个服务的类型并将其命名。 对于本教程，请选择“可靠角色服务”。 有关其他服务类型的详细信息，请参阅 [Service Fabric 编程模型概述](service-fabric-choose-framework.md)。
   ![适用于 Java 的 Service Fabric Yeoman 生成器][sf-yeoman]

如果将应用程序命名为“HelloWorldActorApplication”，将执行组件命名为“HelloWorldActor”，则会创建以下基架：

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```
## <a name="reliable-actors-basic-building-blocks"></a>Reliable Actors 基本构建基块
前面所述的基本概念可以转化为 Reliable Actor 服务的基本构建块。

### <a name="actor-interface"></a>执行组件接口
包含执行组件的接口定义。 此接口定义执行组件实现和调用执行组件的客户端所共享的执行组件协定，因此合理的做法是在独立于执行组件实现的某个位置定义接口，并且其他多个服务或客户端应用程序可以共享接口。

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`：

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>执行组件服务
包含执行组件实现和执行组件注册代码。 执行组件类实现执行组件接口。 这是执行组件的工作位置。

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`：

```java
@ActorServiceAttribute(name = "HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends FabricActor implements HelloWorldActor {
    private Logger logger = Logger.getLogger(this.getClass().getName());

    public HelloWorldActorImpl(FabricActorService actorService, ActorId actorId){
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>执行组件注册
执行组件服务必须使用 Service Fabric 运行时中的服务类型注册。 为了使执行组件服务能够运行执行组件实例，还必须向执行组件服务注册执行组件类型。 `ActorRuntime` 注册方法将为执行组件执行此操作。

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`：

```java
public class HelloWorldActorHost {

private static final Logger logger = Logger.getLogger(HelloWorldActorHost.class.getName());
    
public static void main(String[] args) throws Exception {
        
        try {

            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new FabricActorService(context, actorType, (a,b)-> new HelloWorldActorImpl(a,b)), Duration.ofSeconds(10));
            Thread.sleep(Long.MAX_VALUE);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occurred", e);
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>构建应用程序
Service Fabric Yeoman 模板包含 [Gradle](https://gradle.org/) 的生成脚本，可用于从终端生成应用程序。
Service Fabric Java 依赖项从 Maven 提取。 若要生成和使用 Service Fabric Java 应用程序，需确保已安装 JDK 和 Gradle。 如果尚未安装，请参阅 [Service Fabric 入门（使用 Linux）](service-fabric-get-started-linux.md#set-up-java-development)，获取有关如何安装 JDK 和 Gradle 的说明。

若要生成并打包应用程序，请运行以下命令：

  ```bash
  cd HelloWorldActorApplication
  gradle
  ```

## <a name="deploy-the-application"></a>部署应用程序
生成应用程序后，可以将其部署到本地群集。

1. 连接到本地 Service Fabric 群集（该群集必须[已设置且正在运行](service-fabric-get-started-linux.md#set-up-a-local-cluster)）。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. 运行模板中提供的安装脚本可将应用程序包复制到群集的映像存储区、注册应用程序类型和创建应用程序实例。

    ```bash
    ./install.sh
    ```

部署生成的应用程序时，其方式与部署任何其他 Service Fabric 应用程序相同。 如需详细的说明，请参阅相关文档，了解如何[使用 Service Fabric CLI 管理 Service Fabric 应用程序](service-fabric-application-lifecycle-sfctl.md)。

这些命令的参数可以在应用程序包内的已生成清单中找到。

应用程序部署完以后，请打开浏览器并导航到 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)，其地址为 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)。
然后，展开“应用程序”节点，注意现在有一个条目是用于你的应用程序类型，另一个条目用于该类型的第一个实例。

> [!IMPORTANT]
> 必须将证书配置为向 Service Fabric 运行时验证应用程序，才能将应用程序部署到 Azure 中的安全 Linux 群集。 这样可便于 Reliable Actors 服务与基础 Service Fabric 运行时 API 通信。 若要了解详细信息，请参阅[将 Reliable Services 应用程序配置为在 Linux 群集上运行](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)。  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>启动测试客户端并执行故障转移
角色自身未执行任何操作，它们需要其他服务或客户端向其发送消息。 Actor 模板包括可用于与 actor 服务进行交互的简单测试脚本。

> [!Note]
> 测试客户端使用 ActorProxy 类与执行组件进行通信，执行组件必须在执行组件服务所在的群集内运行或共享相同的 IP 地址空间。  可以在本地开发群集所在的计算机上运行测试客户端。  但是，若要与远程群集中的执行组件进行通信，必须在处理与执行组件的外部通信的群集上部署网关。

1. 使用监视实用程序运行该脚本来查看 actor 服务的输出。  测试脚本对角色调用 `setCountAsync()` 方法来递增计数器，对角色调用 `getCountAsync()` 方法来获取新的计数器值，并向控制台显示该值。

   若为 MAC OS X，需要运行下面的附加命令，将 HelloWorldTestClient 文件夹复制到容器内的某位置。    
    
    ```bash
     docker cp HelloWorldTestClient [first-four-digits-of-container-ID]:/home
     docker exec -it [first-four-digits-of-container-ID] /bin/bash
     cd /home
     ```

    ```bash
    cd HelloWorldActorTestClient
    watch -n 1 ./testclient.sh
    ```

2. 在 Service Fabric Explorer 中，找到托管角色服务的主要副本的节点。 以下屏幕截图中显示的是节点 3。 主要服务副本处理读写操作。  然后, 将服务状态更改复制到辅助副本, 在下面的屏幕截图的节点0和1上运行。

    ![在 Service Fabric Explorer 中查找主副本][sfx-primary]

3. 在“节点”中，单击上一步找到的节点，然后从“操作”菜单中选择“停用(重启)”。 此操作重启运行主要服务副本的节点，并强制故障转移到在其他节点上运行的其中一个次要副本。  该次要副本将提升为主要副本，并在其他节点上创建另一次要副本，然后主要副本开始执行读/写操作。 节点重启时，请注意来自测试客户端的输出，并注意虽然发生故障转移，但计数器仍继续递增。

## <a name="remove-the-application"></a>删除应用程序
使用模板中提供的卸载脚本可从群集的映像存储区删除应用程序实例、注销应用程序包并删除应用程序包。

```bash
./uninstall.sh
```

在 Service Fabric Explorer 中，可看到应用程序和应用程序类型不再显示在“应用程序”节点中。

## <a name="service-fabric-java-libraries-on-maven"></a>Maven 上的 Service Fabric Java 库
Service Fabric Java 库已托管在 Maven 中。 可以在项目的 ``pom.xml`` 或 ``build.gradle`` 中添加依赖项，以便使用 mavenCentral 提供的 Service Fabric Java 库。 

### <a name="actors"></a>执行组件

针对应用程序的 Service Fabric Reliable Actor 支持。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors:1.0.0'
  }
  ```

### <a name="services"></a>Services

针对应用程序的 Service Fabric Reliable Services 支持。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services:1.0.0'
  }
  ```

### <a name="others"></a>其他
#### <a name="transport"></a>传输

针对 Service Fabric Java 应用程序的传输层支持。 不需向 Reliable Actor 或 Service 应用程序显式添加此依赖项，除非在传输层编程。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport:1.0.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric 支持

针对 Service Fabric（与本机 Service Fabric 运行时通信）的系统级支持。 不需向 Reliable Actor 或 Service 应用程序显式添加此依赖项。 当你包括上述其他依赖项时，会自动从 Maven 提取此依赖项。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf</artifactId>
      <version>1.0.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf:1.0.0'
  }
  ```

## <a name="next-steps"></a>后续步骤

* [使用 Eclipse 在 Linux 上创建第一个 Service Fabric Java 应用程序](service-fabric-get-started-eclipse.md)
* [了解有关 Reliable Actors 的详细信息](service-fabric-reliable-actors-introduction.md)
* [使用 Service Fabric CLI 与 Service Fabric 群集交互](service-fabric-cli.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)
* [Service Fabric CLI 入门](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
