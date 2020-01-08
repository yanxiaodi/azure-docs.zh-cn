---
title: 创建 Azure Service Fabric 容器应用程序 | Microsoft Docs
description: 在 Azure Service Fabric 上创建第一个 Windows 容器应用程序。 生成包含 Python 应用程序的 Docker 映像，将该映像推送到容器注册表，然后生成并部署 Service Fabric 容器应用程序。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: jpconnock
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/25/2019
ms.author: atsenthi
ms.openlocfilehash: 771a4ffde9f3929a55ee8ce48c2b38e16b83ad49
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650676"
---
# <a name="create-your-first-service-fabric-container-application-on-windows"></a>在 Windows 上创建第一个 Service Fabric 容器应用程序

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

在 Service Fabric 群集上运行 Windows 容器中的现有应用程序不需要对应用程序进行任何更改。 本文逐步讲解如何创建包含 Python [Flask](http://flask.pocoo.org/) Web 应用程序的 Docker 映像并将其部署到在本地计算机上运行的 Service Fabric 群集。 此外，将通过 [Azure 容器注册表](/azure/container-registry/)共享容器化的应用程序。 本文假定读者对 Docker 有一个基本的了解。 阅读 [Docker Overview](https://docs.docker.com/engine/understanding-docker/)（Docker 概述）即可了解 Docker。

> [!NOTE]
> 本文适用于 Windows 开发环境。  Service Fabric 群集运行时和 Docker 运行时必须在同一 OS 上运行。  不能在 Linux 群集上运行 Windows 容器。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

* 一台运行以下软件的开发计算机：
  * Visual Studio 2015 或 Visual Studio 2019。
  * [Service Fabric SDK 和工具](service-fabric-get-started.md)。
  *  适用于 Windows 的 Docker。 [Get Docker CE for Windows (stable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)（获取适用于 Windows 的 Docker CE（稳定版））。 安装并启动 Docker 以后，右键单击任务栏图标，并选择“切换到 Windows 容器”。 此步骤是运行基于 Windows 的 Docker 映像所必需的。

* 有三个或更多节点在使用容器的 Windows Server 上运行的 Windows 群集。 

  在本文中，群集结点上运行的使用容器的 Windows Server 版本必须与开发计算机上的版本相匹配。 这是因为要在开发计算机上构建 Docker 映像，而容器 OS 和用来部署的主机 OS 的版本之间有兼容性限制。 有关详细信息，请参阅 [Windows Server 容器 OS 与主机 OS 的兼容性](#windows-server-container-os-and-host-os-compatibility)。 
  
若要确定使用容器的 Windows Server 版本，请在开发计算机上从 Windows 命令提示符运行 `ver` 命令：

* 如果版本包含 x.x.14323.x，[创建群集](service-fabric-cluster-creation-via-portal.md)时请选择 WindowsServer 2016-Datacenter-with-Containers 作为操作系统。
  * 如果版本包含 x.x.16299.x，[创建群集](service-fabric-cluster-creation-via-portal.md)时请选择 WindowsServerSemiAnnual Datacenter-Core-1709-with-Containers 作为操作系统。

* 一个位于 Azure 容器注册表中的注册表 - 在 Azure 订阅中[创建容器注册表](../container-registry/container-registry-get-started-portal.md)。

> [!NOTE]
> 支持将容器部署到在 Windows 10 上运行的 Service Fabric 群集。  有关如何配置 Windows 10 以运行 Windows 容器的信息，请参阅[此文](service-fabric-how-to-debug-windows-containers.md)。
>   

> [!NOTE]
> Service Fabric 6.2 及更高版本支持将容器部署到在 Windows Server 1709 上运行的群集。  
> 

## <a name="define-the-docker-container"></a>定义 Docker 容器

基于 Docker 中心内的 [Python 映像](https://hub.docker.com/_/python/)生成一个映像。

在 Dockerfile 中指定 Docker 容器。 Dockerfile 包含有关在容器中设置环境、加载要运行的应用程序以及映射端口的说明。 Dockerfile 是 `docker build` 命令的输入，该命令用于创建映像。

创建一个空目录并创建文件 *Dockerfile*（不带文件扩展名）。 将以下内容添加到 *Dockerfile* 并保存所做的更改：

```
# Use an official Python runtime as a base image
FROM python:2.7-windowsservercore

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available to the world outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

有关详细信息，请阅读 [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)（Dockerfile 参考）。

## <a name="create-a-basic-web-application"></a>创建基本 Web 应用程序
创建一个在端口 80 上进行侦听并返回 `Hello World!` 的 Flask Web 应用程序。 在同一个目录中，创建文件 *requirements.txt*。 添加以下内容并保存所做的更改：
```
Flask
```

此外，创建 *app.py* 文件并添加以下代码片段：

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

<a id="Build-Containers"></a>
## <a name="build-the-image"></a>生成映像
运行 `docker build` 命令，创建运行上述 Web 应用程序的映像。 打开 PowerShell 窗口并导航到包含 Dockerfile 的目录。 运行下面的命令：

```
docker build -t helloworldapp .
```

该命令按 Dockerfile 中的说明生成新映像，并将映像命名为（-t 表示标记）`helloworldapp`。 若要生成容器映像，首先从 Docker Hub 下载基础映像并在其上添加应用程序。 

生成命令执行完以后，请运行 `docker images` 命令，查看有关新映像的信息：

```
$ docker images

REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              8ce25f5d6a79        2 minutes ago       10.4 GB
```

## <a name="run-the-application-locally"></a>在本地运行应用程序
请先在本地验证映像，然后再将其推送到容器注册表。 

运行应用程序：

```
docker run -d --name my-web-site helloworldapp
```

name 用于为运行的容器（而不是容器 ID）命名。

容器启动以后，查找其 IP 地址，以便通过浏览器连接到正在运行的容器：
```
docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
```

如果该命令未返回任何内容，请运行以下命令并检查 IP 地址的 **NetworkSettings**->**Networks** 元素：
```
docker inspect my-web-site
```

连接到正在运行的容器。 打开 Web 浏览器并指向返回的 IP 地址，例如“http:\//172.31.194.61”。 此时会看到标题“Hello World!” 显示在浏览器中。

若要停止容器，请运行：

```
docker stop my-web-site
```

从开发计算机中删除该容器：

```
docker rm my-web-site
```

<a id="Push-Containers"></a>
## <a name="push-the-image-to-the-container-registry"></a>将映像推送到容器注册表

验证容器是否在开发计算机上运行以后，即可将映像推送到 Azure 容器注册表的注册表中。

运行 ``docker login``，以使用[注册表凭据](../container-registry/container-registry-authentication.md)登录到容器注册表。

以下示例传递了 Azure Active Directory [服务主体](../active-directory/develop/app-objects-and-service-principals.md)的 ID 和密码。 例如，你可能在自动化方案中向注册表分配了服务主体。 或者，可以使用注册表用户名和密码登录。

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

以下命令使用注册表的完全限定路径创建映像的标记或别名。 此示例将映像置于 ```samples``` 命名空间，以免注册表根目录中出现混乱。

```
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

将映像推送到容器注册表：

```
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="create-the-containerized-service-in-visual-studio"></a>在 Visual Studio 中创建容器化服务
Service Fabric SDK 和工具提供服务模板，用于创建容器化应用程序。

1. 启动 Visual Studio。 选择“文件” > “新建” > “项目”。
2. 选择“Service Fabric 应用程序”，将其命名为“MyFirstContainer”，并单击“确定”。
3. 从“服务模板”列表中选择“容器”。
4. 在“映像名称”中输入“myregistry.azurecr.io/samples/helloworldapp”，这是已推送到容器存储库中的映像。
5. 为服务命名，并单击“**确定**”。

## <a name="configure-communication"></a>配置通信
容器化服务需要使用终结点进行通信。 请将 `Endpoint` 元素以及协议、端口和类型添加到 ServiceManifest.xml 文件。 在此示例中，使用固定端口 8081。 如果未指定端口，则从应用程序端口范围中选择一个随机端口。 

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
  </Endpoints>
</Resources>
```
> [!NOTE]
> 可以通过使用适用的属性值声明其他 EndPoint 元素来添加服务的其他终结点。 每个端口可以仅声明一个协议值。

定义终结点后，Service Fabric 即可将该终结点发布到命名服务。 在群集中运行的其他服务可以解析此容器。 还可以使用[反向代理](service-fabric-reverseproxy.md)进行容器到容器通信。 将 HTTP 侦听端口和想要与其通信的服务名称作为环境变量提供给反向代理，以此方式进行通信。

该服务在特定端口（本例中为 8081）上进行侦听。 当应用程序部署到 Azure 中的群集时，该群集和应用程序都在 Azure 负载均衡器之后运行。 应用程序端口必须在 Azure 负载均衡器中打开，以便入站流量能够通过该服务。  可以在 Azure 负载均衡器中使用 [PowerShell 脚本](./scripts/service-fabric-powershell-open-port-in-load-balancer.md)来打开此端口，也可以在 [Azure 门户](https://portal.azure.com)中将其打开。

## <a name="configure-and-set-environment-variables"></a>配置和设置环境变量
可以针对服务清单中的每个代码包指定环境变量。 此功能适用于所有服务，不管它们是作为容器、进程还是来宾可执行文件部署的。 可以替代应用程序清单中的环境变量值，或者在部署期间将其指定为应用程序参数。

以下服务清单 XML 代码片段演示如何指定代码包的环境变量：
```xml
<CodePackage Name="Code" Version="1.0.0">
  ...
  <EnvironmentVariables>
    <EnvironmentVariable Name="HttpGatewayPort" Value=""/>    
  </EnvironmentVariables>
</CodePackage>
```

可在应用程序清单中重写这些环境变量：

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <EnvironmentOverrides CodePackageRef="FrontendService.Code">
    <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
  </EnvironmentOverrides>
  ...
</ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping-and-container-to-container-discovery"></a>配置容器端口到主机端口的映射，以及容器到容器的发现
配置用来与容器通信的主机端口。 端口绑定可将服务在容器中侦听的端口映射到主机上的端口。 在 ApplicationManifest.xml 文件的 `ContainerHostPolicies` 元素中添加 `PortBinding` 元素。 在本文中，`ContainerPort` 为 80（容器根据 Dockerfile 中的指定值公开端口 80），`EndpointRef` 为“Guest1TypeEndpoint”（以前在服务清单中定义的终结点）。 传入到端口 8081 上的服务的请求映射到容器上的端口 80。

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```
> [!NOTE]
> 可以通过使用适用的属性值声明其他 PortBinding 元素来添加服务的其他端口绑定。

## <a name="configure-container-repository-authentication"></a>配置容器存储库身份验证

若要了解如何为容器映像下载配置不同类型的身份验证, 请参阅[容器存储库身份验证](configure-container-repository-credentials.md)。

## <a name="configure-isolation-mode"></a>配置隔离模式
Windows 支持容器的两种隔离模式：进程和 Hyper-V。 使用进程隔离模式时，在同一台主机计算机上运行的所有容器将与主机共享内核。 使用 Hyper-V 隔离模式时，内核将在每个 Hyper-V 容器与容器主机之间隔离。 隔离模式在应用程序清单文件中的 `ContainerHostPolicies` 元素内指定。 可以指定的隔离模式为 `process`、`hyperv` 和 `default`。 Windows Server 主机上默认采用进程隔离模式。 Windows 10 主机仅支持 Hyper-V 隔离模式，因此无论容器的离模式设置如何，它都在 Hyper-V 隔离模式下运行。 以下代码片段演示如何在应用程序清单文件中指定隔离模式。

```xml
<ContainerHostPolicies CodePackageRef="Code" Isolation="hyperv">
```
   > [!NOTE]
   > hyperv 隔离模式在 Ev3 和 Dv3 Azure SKU 上提供，后者具有嵌套式虚拟化支持。 
   >
   >

## <a name="configure-resource-governance"></a>配置资源调控
[资源调控](service-fabric-resource-governance.md)限制容器能够在主机上使用的资源。 在应用程序清单中指定的 `ResourceGovernancePolicy` 元素用于声明服务代码包的资源限制。 可以为以下资源设置资源限制：内存、MemorySwap、CpuShares（CPU 相对权重）、MemoryReservationInMB、BlkioWeight（BlockIO 相对权重）。 在此示例中，服务包 Guest1Pkg 在放置它的群集节点上获得一个核心。 内存限制是绝对的，所以此代码包限制为 1024 MB 内存（和相同的软保证预留）。 代码包（容器或进程）无法分配超出此限制的内存，尝试执行此操作会引发内存不足异常。 若要强制执行资源限制，服务包中的所有代码包均应指定内存限制。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```
## <a name="configure-docker-healthcheck"></a>配置 docker HEALTHCHECK 

从 v6.1 开始，Service Fabric 自动将 [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) 事件集成到其系统运行状况报告。 这意味着，如果容器启用了 **HEALTHCHECK**，则只要容器的运行状况状态如 Docker 所报告的那样更改，Service Fabric 就会报告运行状况。 当 *health_status* 为“正常”时，会在 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 中显示运行状况报告“正常”；当 *health_status* 为“不正常”时，会显示“警告”。 

从 v6.4 的最新更新版开始，可以选择指定应将 Docker HEALTHCHECK 评估报告为错误。 如果此选项已启用，当 *health_status* 为“正常”时，将显示“正常”运行状况报告；当 *health_status* 为“不正常”时，将显示“错误”运行状况报告。

生成容器映像时使用的 Dockerfile 中必须存在 **HEALTHCHECK** 指令，该指令指向监视容器运行状况时执行的实际检查。

![HealthCheckHealthy][3]

![HealthCheckUnhealthyApp][4]

![HealthCheckUnhealthyDsp][5]

可以为每个容器配置 **HEALTHCHECK** 行为，方法是在 ApplicationManifest 中将 **HealthConfig** 选项指定为 **ContainerHostPolicies** 的一部分。

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
默认情况下，*IncludeDockerHealthStatusInSystemHealthReport* 设置为 **true**，*RestartContainerOnUnhealthyDockerHealthStatus* 设置为 **false**，而 *TreatContainerUnhealthyStatusAsError* 设置为 **false**。 

如果 *RestartContainerOnUnhealthyDockerHealthStatus* 设置为 **true**，则会重启（可能在其他节点上进行）反复报告“不正常”的容器。

如果 *TreatContainerUnhealthyStatusAsError* 设置为 **true**，当容器的 *health_status* 为“运行不正常”时，将显示“错误”运行状况报告。

若要禁用整个 Service Fabric 群集的 **HEALTHCHECK** 集成，则需将 [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) 设置为 **false**。

## <a name="deploy-the-container-application"></a>部署容器应用程序
保存所有更改，生成应用程序。 若要发布应用程序，请右键单击解决方案资源管理器中的“MyFirstContainer”，选择“发布”。

在“连接终结点”中输入群集的管理终结点。 例如，“containercluster.westus2.cloudapp.azure.com:19000”。 在 [Azure 门户](https://portal.azure.com)中，可以在群集的“概览”选项卡中查找客户端连接终结点。

单击“发布”。

[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 是一项基于 Web 的工具，用于检验和管理 Service Fabric 群集中的应用程序和节点。 打开浏览器，导航到 http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ ，并执行应用程序部署。 将映像下载到群集节点（这可能需要一段时间，具体时间取决于映像大小）之前，应用程序可部署但处于错误状态：![错误][1]

如果应用程序处于 ```Ready``` 状态，则表示它已准备就绪：![就绪][2]

打开浏览器并导航到 http://containercluster.westus2.cloudapp.azure.com:8081 。 此时会看到标题“Hello World!” 显示在浏览器中。

## <a name="clean-up"></a>清理

只要群集处于运行状态，就会产生费用。若要避免不必要的费用，可考虑[删除群集](service-fabric-cluster-delete.md)。

将映像推送到容器注册表以后，可从开发计算机中删除本地映像：

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="windows-server-container-os-and-host-os-compatibility"></a>Windows Server 容器 OS 与主机 OS 的兼容性

Windows Server 容器并非在所有主机 OS 版本间都兼容。 例如：
 
- 使用 Windows Server 1709 版本生成的 Windows Server 容器在运行 Windows Server 2016 版本的主机上无效。 
- 使用 Windows Server 2016 生成的 Windows Server 容器仅在运行 Windows Server 1709 版本的主机上以 Hyper-V 隔离模式工作。 
- 因为 Windows Server 容器使用 Windows Server 2016 生成，所以在运行 Windows Server 2016 的主机上以进程隔离模式运行时，可能需要确保容器 OS 和主机 OS 的版本相同。
 
若要了解详细信息，请参阅 [Windows 容器版本兼容性](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility)。

将容器生成和部署到 Service Fabric 群集时，请考虑主机 OS 和容器 OS 的兼容性。 例如：

- 请确保通过与群集节点上的 OS 兼容的 OS 部署容器。
- 请确保为容器应用指定的隔离模式与为部署容器的节点上的容器 OS 提供支持的要求一致。
- 请考虑 OS 升级到群集结点的方式，否则容器可能影响它们的兼容性。 

建议采取以下做法，确保容器正确部署在 Service Fabric 群集上：

- 对 Docker 映像进行显式映像标记，以指定生成容器的 Windows Server OS 的版本。 
- 在应用程序清单文件中使用 [OS 标记](#specify-os-build-specific-container-images)，确保应用程序与不同 Windows Server 版本和升级兼容。

> [!NOTE]
> 使用 Service Fabric 6.2 及更高版本，可以在 Windows 10 主机上本地部署基于 Windows Server 2016 的容器。 在 Windows 10 上，容器以 Hyper-V 隔离模式运行，不管应用程序清单中设置的隔离模式如何。 若要了解详细信息，请参阅[配置隔离模式](#configure-isolation-mode)。   
>
 
## <a name="specify-os-build-specific-container-images"></a>指定特定于 OS 内部版本的容器映像 

Windows Server 容器在不同 OS 版本中可能不兼容。 例如，在进程隔离模式中，使用 Windows Server 2016 生成的 Windows Server 容器在 Windows Server 版本 1709 上无效。 因此，如果将群集节点更新到最新版本，则使用较早版本的 OS 生成的容器服务可能会发生故障。 为了避免 6.1 及更新版本的运行时出现这种情况，Service Fabric 允许为每个容器指定多个 OS 映像并为这些映像标记应用程序清单中的 OS 的内部版本。 可以通过在 Windows 命令提示符下运行 `winver` 获取 OS 的内部版本。 更新应用程序清单并为每个 OS 版本指定映像重写项，然后更新节点上的 OS。 以下代码片段演示了如何在应用程序清单 **ApplicationManifest.xml** 中指定多个容器映像：


```xml
      <ContainerHostPolicies> 
         <ImageOverrides> 
           <Image Name="myregistry.azurecr.io/samples/helloworldappDefault" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1701" Os="14393" /> 
               <Image Name="myregistry.azurecr.io/samples/helloworldapp1709" Os="16299" /> 
         </ImageOverrides> 
      </ContainerHostPolicies> 
```
WIndows Server 2016 的内部版本为 14393，Windows Server 版本 1709 的内部版本为 16299。 对于单个容器服务，服务清单仍然只指定一个映像，如下所示：

```xml
<ContainerHost>
    <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName> 
</ContainerHost>
```

   > [!NOTE]
   > OS 内部版本标记功能仅适用于 Windows 上的 Service Fabric
   >

如果 VM 上的基础 OS 为内部版本 16299（版本 1709），Service Fabric 会根据该 Windows Server 版本选取容器映像。 如果应用程序清单中除了标记的容器映像外，还提供了未标记的容器映像，则 Service Fabric 会将未标记的映像视为可以跨版本使用的映像。 请显式对容器映像进行标记以避免在升级过程中出现问题。

未标记的容器映像将作为在 ServiceManifest 中提供的映像的替代。 因此，映像“myregistry.azurecr.io/samples/helloworldappDefault”将替代 ServiceManifest 中的 ImageName“myregistry.azurecr.io/samples/helloworldapp”。

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Service Fabric 应用程序和服务清单的完整示例
下面本文中使用的服务和应用程序完整清单。

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->    
    <EnvironmentVariables>
      <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
      <EnvironmentVariable Name="BackendServiceName" Value=""/>
    </EnvironmentVariables>

  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to
           listen. Please note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="8081" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="FrontendService.Code">
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentOverrides>
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="MIIB6QYJKoZIhvcNAQcDoIIB2jCCAdYCAQAxggFRMIIBTQIBADA1MCExHzAdBgNVBAMMFnJ5YW53aWRhdGFlbmNpcGhlcm1lbnQCEFfyjOX/17S6RIoSjA6UZ1QwDQYJKoZIhvcNAQEHMAAEg
gEAS7oqxvoz8i6+8zULhDzFpBpOTLU+c2mhBdqXpkLwVfcmWUNA82rEWG57Vl1jZXe7J9BkW9ly4xhU8BbARkZHLEuKqg0saTrTHsMBQ6KMQDotSdU8m8Y2BR5Y100wRjvVx3y5+iNYuy/JmM
gSrNyyMQ/45HfMuVb5B4rwnuP8PAkXNT9VLbPeqAfxsMkYg+vGCDEtd8m+bX/7Xgp/kfwxymOuUCrq/YmSwe9QTG3pBri7Hq1K3zEpX4FH/7W2Zb4o3fBAQ+FuxH4nFjFNoYG29inL0bKEcTX
yNZNKrvhdM3n1Uk/8W2Hr62FQ33HgeFR1yxQjLsUu800PrYcR5tLfyTB8BgkqhkiG9w0BBwEwHQYJYIZIAWUDBAEqBBBybgM5NUV8BeetUbMR8mJhgFBrVSUsnp9B8RyebmtgU36dZiSObDsI
NtTvlzhk11LIlae/5kjPv95r3lw6DHmV4kXLwiCNlcWPYIWBGIuspwyG+28EWSrHmN7Dt2WqEWqeNQ==" PasswordEncrypted="true"/>
        <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this
         application type is created. You can also create one or more instances of service type using the
         ServiceFabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="configure-time-interval-before-container-is-force-terminated"></a>配置在强制终止容器之前需经历的时间间隔

可以配置一个时间间隔，目的是在启动服务删除操作（或移动到另一个节点的操作）之后，要求运行时在删除容器之前等待特定的时间。 配置时间间隔时，会向容器发送 `docker stop <time in seconds>` 命令。  有关更多详细信息，请参阅 [docker stop](https://docs.docker.com/engine/reference/commandline/stop/)。 等待时间间隔在 `Hosting` 节指定。 可以在群集创建时或配置升级后期添加 `Hosting` 节。 以下群集清单代码片段显示了如何设置等待时间间隔：

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
    }
]
```
默认时间间隔设置为 10 秒。 由于此配置是动态的，因此对群集进行仅限配置的升级即可更新超时。 


## <a name="configure-the-runtime-to-remove-unused-container-images"></a>将运行时配置为删除未使用的容器映像

可以将 Service Fabric 群集配置为从节点删除未使用的容器映像。 如果节点上存在过多容器映像，则可通过此配置回收磁盘空间。 若要启用此功能，请更新群集清单中的 [Hosting](service-fabric-cluster-fabric-settings.md#hosting) 节，如以下代码片段所示： 


```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "microsoft/windowsservercore|microsoft/nanoserver|microsoft/dotnet-frameworku|..."
          }
          ...
          }
        ]
    } 
]
```

对于不应删除的映像，可以在 `ContainerImagesToSkip` 参数下进行指定。  


## <a name="configure-container-image-download-time"></a>配置容器映像下载时间

Service Fabric 运行时为下载和解压缩容器映像分配了 20 分钟的时间，这适用于大多数容器映像。 如果是大型映像，或者网络连接速度较慢，则可能必须延长中止映像下载和解压缩之前的等待时间。 此超时是使用群集清单的 **Hosting** 节中的 **ContainerImageDownloadTimeout** 属性设置的，如以下代码片段所示：

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
    }
]
```


## <a name="set-container-retention-policy"></a>设置容器保留策略

Service Fabric（6.1 或更高版本）支持保留终止的或无法启动的容器，这样有助于诊断容器启动故障。 此策略可以在 **ApplicationManifest.xml** 文件中设置，如以下代码片段所示：

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

ContainersRetentionCount 设置指定在容器故障时需保留的容器数。 如果指定一个负值，则会保留所有故障容器。 如果不指定 **ContainersRetentionCount** 属性，则不会保留任何容器。 ContainersRetentionCount 属性还支持应用程序参数，因此用户可以为测试性群集和生产群集指定不同的值。 使用此功能时可使用放置约束，将容器服务的目标设置为特定的节点，防止将容器服务移至其他节点。 使用此功能保留的容器必须手动删除。

## <a name="start-the-docker-daemon-with-custom-arguments"></a>使用自定义参数启动 Docker 守护程序

有了 6.2 版和更高版本的 Service Fabric 运行时，可以使用自定义参数启动 Docker 守护程序。 指定自定义参数时，Service Fabric 不会将 `--pidfile` 参数以外的任何其他参数传递给 docker 引擎。 因此，`--pidfile` 不应作为参数传递。 此外，参数应继续让 docker 守护程序侦听 Windows 上的默认名称管道（或 Linux 上的 unix 域套接字），以便 Service Fabric 与该守护程序通信。 自定义参数是在 **Hosting** 节中的 **ContainerServiceArguments** 下的群集清单中传递的，如以下代码片段中所示。 
 

```json
"fabricSettings": [
    ...,
    { 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
    } 
]
```

## <a name="next-steps"></a>后续步骤
* 详细了解如何运行 [Service Fabric 上的容器](service-fabric-containers-overview.md)。
* 阅读[在容器中部署 .NET 应用程序](service-fabric-host-app-in-a-container.md)教程。
* 了解 Service Fabric [应用程序生命周期](service-fabric-application-lifecycle.md)。
* 查看 GitHub 上的 [Service Fabric 容器代码示例](https://github.com/Azure-Samples/service-fabric-containers)。

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[4]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[5]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
