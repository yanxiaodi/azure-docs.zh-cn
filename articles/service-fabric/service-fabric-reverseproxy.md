---
title: Azure Service Fabric 反向代理 | Microsoft 文档
description: 使用 Service Fabric 的反向代理从群集内部和外部与微服务通信
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 6ce6f1f6559b43a64fb7edd0773a20f8ee0cf8a3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837927"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Azure Service Fabric 中的反向代理
借助 Azure Service Fabric 中内置的反向代理，Service Fabric 群集中运行的微服务可以发现包含 http 终结点的其他服务，并与之通信。

## <a name="microservices-communication-model"></a>微服务通信模型
Service Fabric 中的微服务在群集中的部分节点上运行，可以出于各种原因在这些节点之间迁移。 因此，微服务的终结点可能会动态变化。 若要发现群集中的其他服务并与之通信，微服务必须完成以下步骤：

1. 通过命名服务解析服务位置。
2. 连接到服务。
3. 在实现服务解析以及在发生连接故障时应用的重试策略的循环中，包装上述步骤

有关详细信息，请参阅[与服务连接和通信](service-fabric-connect-and-communicate-with-services.md)。

### <a name="communicating-by-using-the-reverse-proxy"></a>使用反向代理通信
反向代理是在每个节点上运行的服务，用于代表客户端服务处理终结点解析、自动重试及其他连接故障。 可以将反向代理配置为，一边处理客户端服务的请求，一边应用各种策略。 借助反向代理，客户端服务可以使用任意客户端 HTTP 通信库，无需服务中有特殊的解析和重试逻辑。 

反向代理在本地节点上公开一个或多个终结点，以供客户端服务用来向其他服务发送请求。

![内部通信][1]

> [!NOTE]
> **支持的平台**
>
> Service Fabric 中的反向代理目前支持以下平台
> * *Windows 群集*：Windows 8 及更高版本，或 Windows Server 2012 及更高版本
> * *Linux 群集*：反向代理当前不适用于 Linux 群集
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>从群集外部访问微服务
微服务的默认外部通信模型为“选择加入”模型，在该模型中，无法直接从外部客户端访问每个服务。 [Azure 负载均衡器](../load-balancer/load-balancer-overview.md)充当微服务和外部客户端之间的网络边界，可以进行网络地址转换并将外部请求转发到内部的 IP:端口终结点。 要允许外部客户端直接访问微服务的终结点，必须先将负载均衡器配置为将流量转发到群集中服务使用的每个端口。 另外，大多数微服务（尤其是有状态微服务）并不驻留在群集的所有节点上。 这些微服务在故障转移时可在节点之间移动。 在这种情况下，负载均衡器无法有效确定要将流量转发到的副本的目标节点位置。

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>从群集外部通过反向代理访问微服务
可以在负载均衡器中直接配置反向代理的端口，而无需配置单个服务的端口。 这种配置可让群集外部的客户端使用反向代理访问群集内部的服务，无需经过额外的配置。

![外部通信][0]

> [!WARNING]
> 在负载均衡器中配置反向代理的端口后，可从群集外部访问群集中公开 HTTP 终结点的所有微服务。 这意味着微服务设计为内部的可能会被确定的恶意用户发现。 这潜在地提供可被利用的严重漏洞；例如：
>
> * 恶意用户可以通过反复调用没有足够强化的攻击面的内部服务来发起拒绝服务攻击。
> * 恶意用户可能会将格式错误的数据包传送到内部服务，从而导致意外行为。
> * 设计为内部的服务可能会返回不应公开给群集外部的服务的私有或敏感信息，从而将此敏感信息泄露给恶意用户。 
>
> 在公开反向代理端口之前，请确保完全了解并减轻对群集及其上运行的应用程序的潜在安全影响。 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>使用反向代理访问服务时所用的 URI 格式
反向代理使用特定的统一资源标识符 (URI) 格式来识别传入请求应该转发到的服务分区：

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s)：** 可以将反向代理配置为接受 HTTP 或 HTTPS 流量。 对于 HTTPS 转发，在设置反向代理侦听 HTTPS 后，请参阅[使用反向代理连接到安全服务](service-fabric-reverseproxy-configure-secure-communication.md)。
* **群集的完全限定域名 (FQDN) | 内部 IP：** 对于外部客户端，可以配置反向代理，以便可以通过群集域（例如 mycluster.eastus.cloudapp.azure.com）访问反向代理。 默认情况下，反向代理在每个节点上运行。 对于内部流量，可在本地主机或任意内部节点 IP（例如 10.0.0.1）上访问反向代理。
* **Port：** 这是为反向代理指定的端口，例如 19081。
* **ServiceInstanceName：** 在不使用“fabric:/”方案的情况下尝试访问的已部署服务实例的完全限定名称。 例如，若要访问 *fabric:/myapp/myservice/* 服务，可以使用 *myapp/myservice*。

    服务实例名称要区分大小写。 若 URL 中的服务实例名称大小写不同，则会导致请求失败，并显示 404（未找到）。
* **Suffix path：** 要连接到的服务的实际 URL 路径，例如 *myapi/values/add/3*。
* **PartitionKey：** 对于分区服务，这是针对要访问的分区计算出的分区键。 请注意，这*不*是分区 ID GUID。 对于使用单独分区方案的服务，此参数不是必需的。
* **PartitionKind：** 这是服务分区方案。 该方案可以是“Int64Range”或“Named”。 对于使用单独分区方案的服务，此参数不是必需的。
* **ListenerName** 服务中的终结点采用以下形式：{"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}}。 当服务公开了多个终结点时，此参数标识应将客户端请求转发到的终结点。 如果服务只有一个侦听器，则可以省略此项。
* **TargetReplicaSelector** 这指定应当如何选择目标副本或实例。
  * 当目标服务为有状态服务时，TargetReplicaSelector 可以是下列其中一项：“PrimaryReplica”、“RandomSecondaryReplica”或“RandomReplica”。 如果未指定此参数，默认值为“PrimaryReplica”。
  * 当目标服务为无状态服务时，反向代理将选择服务分区的一个随机实例来将实例转发到其中。
* **Timeout：** 此参数指定反向代理针对服务创建的 HTTP 请求（代表客户端请求）的超时。 默认值为 60 秒。 这是一个可选参数。

### <a name="example-usage"></a>用法示例
以 *fabric:/MyApp/MyService* 服务为例，该服务可针对以下 URL 打开一个 HTTP 侦听器：

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

下面是该服务的资源：

* `/index.html`
* `/api/users/<userId>`

如果服务使用单独分区方案，则 *PartitionKey* 和 *PartitionKind* 查询字符串参数不是必需的，可以使用网关访问服务，如下所示：

* 外部访问方式：`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* 内部访问方式：`http://localhost:19081/MyApp/MyService`

如果服务使用“统一 Int64”分区方案，则必须使用 *PartitionKey* 和 *PartitionKind* 查询字符串来访问服务的分区：

* 外部访问方式：`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* 内部访问方式：`http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

要访问服务公开的资源，可直接在 URL 中将资源路径置于服务名称之后：

* 外部访问方式：`http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* 内部访问方式：`http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

然后，网关会将这些请求转发到服务的 URL：

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>针对端口共享服务进行特殊处理
无法访问某个服务时，Service Fabric 反向代理会尝试重新解析服务地址以及重试请求。 无法访问某个服务时，通常表示服务实例或副本已在其常规生命周期中转移到其他节点。 发生这种情况时，反向代理可能会收到网络连接错误，指出在原来解析过的地址上的某个终结点不再处于开放状态。

不过，副本或服务实例可能会共享主机进程，在通过基于 http.sys 的 Web 服务器进行托管的情况下还可能会共享端口，这些 Web 服务器包括：

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET Core WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

在这样的情形下，可能会出现 Web 服务器出现在主机进程中并且能够响应请求，而被解析的服务实例或副本却再也不能在主机上使用的情况。 这种情况下，网关会从 Web 服务器收到 HTTP 404 响应。 因此，HTTP 404 响应可能有两种不同的含义：

- 案例 #1：服务地址正确，但用户请求的资源不存在。
- 案例 #2：服务地址不正确，且用户请求的资源可能在其他节点上。

第一种情况是正常的 HTTP 404，属于用户错误。 在第二种情况中，用户请求的资源确实存在。 反向代理找不到该资源，因为服务本身已移动。 反向代理需要重新解析地址，并重试请求。

因此，反向代理需要通过某种方式来区分这两种情况。 为了进行这种区分，需要从服务器获得提示。

* 默认情况下，反向代理假设第二种情况属于事实，因此会尝试重新解析和重新发出请求。
* 若要向反向代理指示第一种情况属于事实，服务应返回以下 HTTP 响应标头：

  `X-ServiceFabric : ResourceNotFound`

此 HTTP 响应标头指示的是正常的 HTTP 404 情形，即所请求的资源不存在，因此反向代理不会尝试重新解析服务地址。

## <a name="special-handling-for-services-running-in-containers"></a>针对容器中运行的服务的特殊处理

对于容器中运行的服务，可以使用环境变量 `Fabric_NodeIPOrFQDN` 构造[反向代理 URL](#uri-format-for-addressing-services-by-using-the-reverse-proxy)，如下面的代码中所示：

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
对于本地群集，`Fabric_NodeIPOrFQDN` 默认设置为“localhost”。 使用 `-UseMachineName` 参数启动本地群集，确保容器可访问节点上运行的反向代理。 有关详细信息，请参阅[配置开发人员环境以调试容器](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers)。

在 Docker Compose 容器中运行的 Service Fabric 服务需要特殊的 docker-compose.yml 端口部分 http: 或 https: 配置  。 有关详细信息，请参阅 [Azure Service Fabric 中的 Docker Compose 部署支持](service-fabric-docker-compose.md)。

## <a name="next-steps"></a>后续步骤
* [在群集上设置和配置反向代理](service-fabric-reverseproxy-setup.md)。
* [设置使用反向代理转发到安全的 HTTP 服务](service-fabric-reverseproxy-configure-secure-communication.md)
* [诊断反向代理事件](service-fabric-reverse-proxy-diagnostics.md)
* 参阅 [GitHub 上的示例项目](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)中服务之间的 HTTP 通信示例。
* [使用 Reliable Services 远程控制执行远程过程调用](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services 中使用 OWIN 的 Web API](service-fabric-reliable-services-communication-webapi.md)
* [使用 Reliable Services 的 WCF 通信](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
