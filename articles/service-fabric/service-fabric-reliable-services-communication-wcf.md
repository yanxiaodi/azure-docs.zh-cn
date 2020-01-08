---
title: Reliable Services WCF 通信堆栈 | Microsoft 文档
description: Service Fabric 中的内置 WCF 通信堆栈为 Service Services 提供客户端到服务的 WCF 通信。
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: ae8a0ab0382083ebfca0834d2238403668efa71d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725595"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Reliable Services 基于 WCF 的通信堆栈
Reliable services 框架使服务创作者能够选择他们要用于其服务的通信堆栈。 他们可以通过从 [CreateServiceReplicaListeners or CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) 方法返回的 **ICommunicationListener** 来插入所选的通信堆栈。 对于想要使用基于 Windows Communication Foundation (WCF) 的通信的服务创作者，该框架提供了基于 WCF 的通信堆栈实现。

## <a name="wcf-communication-listener"></a>WCF 通信侦听器
特定于 WCF 的 **ICommunicationListener** 实现由 **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** 类提供。

假设我们有 `ICalculator` 类型的服务协定

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

我们可以通过下列方式在服务中创建 WCF 通信侦听器。

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>为 WCF 通信堆栈编写客户端
为编写客户端以便使用 WCF 与服务进行通信，该框架提供了**WcfClientCommunicationFactory**，这是特定于 WCF 的 [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md) 实现。

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

可以从 **WcfCommunicationClientFactory** 创建的 **WcfCommunicationClient** 访问 WCF 通信通道。

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

客户端代码可以使用 **WcfCommunicationClientFactory** 以及用于实现 **ServicePartitionClient** 的 **WcfCommunicationClient** 来确定服务终结点，并与服务通信。

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> 默认 ServicePartitionResolver 假设客户端正在与服务相同的群集中运行。 如果不是这样，请创建 ServicePartitionResolver 对象，并传入群集连接终结点。
> 
> 

## <a name="next-steps"></a>后续步骤
* [使用 Reliable Services 远程控制执行远程过程调用](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services 中使用 OWIN 的 Web API](service-fabric-reliable-services-communication-webapi.md)
* [确保 Reliable Services 的通信安全](service-fabric-reliable-services-secure-communication-wcf.md)

