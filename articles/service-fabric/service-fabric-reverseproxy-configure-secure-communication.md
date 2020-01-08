---
title: Azure Service Fabric 反向代理安全通信 | Microsoft Docs
description: 将反向代理配置为启用安全端到端通信。
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/10/2017
ms.author: kavyako
ms.openlocfilehash: d8a11a3289037602535d1b5727d041e376012bd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837828"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>使用反向代理连接到安全服务

本文介绍了如何在反向代理和服务之间建立安全连接，从而启用端到端安全通道。 若要了解有关反向代理的详细信息，请参阅 [Azure Service Fabric 中的反向代理](service-fabric-reverseproxy.md)

只有将反向代理配置为侦听 HTTPS 时，才支持连接到安全服务。 本文假定现为这种情况。
请参阅[在 Azure Service Fabric 中设置反向代理](service-fabric-reverseproxy-setup.md)，在 Service Fabric 中配置反向代理。

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>在反向代理和服务之间建立安全连接 

### <a name="reverse-proxy-authenticating-to-services"></a>向服务进行反向代理身份验证：
反向代理使用其证书向服务标识自己。 对于 Azure 群集，证书使用资源管理器模板 [Microsoft.ServiceFabric/clusters](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/clusters) [资源类型部分](../azure-resource-manager/resource-group-authoring-templates.md)中的 reverseProxyCertificate 属性指定  。 对于独立群集，证书使用 ClusterConfig.json“安全”部分中的 ReverseProxyCertificate 或 ReverseProxyCertificateCommonNames 属性指定  。 若要了解详细信息，请参阅[在独立群集上启用反向代理](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters)。 

服务可以通过实现逻辑验证反向代理提供的证书。 服务可以将接受的客户端证书详细信息指定为配置包中的配置设置。 此信息可在运行时读取，并可用于验证反向代理提供的证书。 若要添加配置设置，请参阅[管理应用程序参数](service-fabric-manage-multiple-environment-app-configuration.md)。 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>反向代理通过服务提供的证书验证服务标识：
反向代理支持以下策略来执行服务提供的证书的服务器证书验证：无、 ServiceCommonNameAndIssuer 和 ServiceCertificateThumbprints。
若要选择反向代理使用的策略，请在 [fabricSettings](service-fabric-cluster-fabric-settings.md) 中的 **ApplicationGateway/Http** 节下指定 **ApplicationCertificateValidationPolicy**。

下一部分介绍了其中每个选项的配置详细信息。

### <a name="service-certificate-validation-options"></a>服务证书验证选项 

- **无**：反向代理跳过验证的代理服务证书，并建立安全连接。 此选项为默认行为。
在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 节中，指定值为 **None** 的 **ApplicationCertificateValidationPolicy**。

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**:反向代理服务器验证基于证书的公用名和中间颁发者的指纹的服务所出示的证书：指定**ApplicationCertificateValidationPolicy**具有值**ServiceCommonNameAndIssuer**中[ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)部分。

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   若要指定服务公用名称和颁发者指纹，请在 **fabricSettings** 下添加 [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) 节，如下所示。 可在 **parameters** 数组中添加多个证书公用名称和颁发者指纹对。 

   如果连接终结点反向代理后提供的证书的公用名称和颁发者指纹与此处指定的任何值匹配，则建立 SSL 通道。 
   如果与证书详细信息不匹配，则反向代理拒绝客户端请求，并显示 502（网关错误）状态代码。 并且 HTTP 状态行还会显示“无效 SSL 证书”短语。 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**:反向代理将验证代理服务证书基于其指纹。 您可以选择继续此路由服务配置使用自签名证书：指定**ApplicationCertificateValidationPolicy**具有值**ServiceCertificateThumbprints**中[ **ApplicationGateway/Http** ](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp)部分。

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   另外，在 **ApplicationGateway/Http** 节中，指定包含 **ServiceCertificateThumbprints** 条目的指纹。 可将多个指纹指定为值字段中的逗号分隔列表，如下所示：

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   如果此配置项中列出了服务器证书的指纹，则反向代理的 SSL 连接成功。 否则将终止连接，拒绝客户端请求，并显示 502（网关错误）。 HTTP 状态行也会包含短语“Invalid SSL Certificate”。

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>服务同时公开安全和不安全终结点时的终结点选择逻辑
Service Fabric 支持为一个服务配置多个终结点。 有关详细信息，请参阅[在服务清单中指定资源](service-fabric-service-manifest-resources.md)。

反向代理根据[服务 URI](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy) 中的 **ListenerName** 查询参数选择某个终结点来转发请求。 如果未指定 **ListenerName** 参数，则反向代理可以选取终结点列表中的任一终结点。 根据为服务配置的终结点，所选终结点可以是 HTTP 或 HTTPS 终结点。 在某些情况下，或者根据某些要求，你希望反向代理在“仅限安全模式”下运行；也就是说，你不希望安全反向代理将请求转发到不安全的终结点。 若要将反向代理设置为仅限安全模式，请在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 节中指定值为 **true** 的 **SecureOnlyMode** 配置条目。   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> 在 **SecureOnlyMode** 下运行时，如果客户端已指定对应于 HTTP（不安全）终结点的 **ListenerName**，则反向代理拒绝请求，并显示 404 (Not Found) HTTP 状态代码。

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>通过反向代理设置客户端证书身份验证
反向代理和所有客户端证书数据丢失时，SSL 将终止。 若要让服务执行客户端证书身份验证，请在 [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp) 节中指定 **ForwardClientCertificate** 设置。

1. 如果将 **ForwardClientCertificate** 设置为 **false**，在反向代理与客户端执行 SSL 握手期间，反向代理不会请求客户端证书。
此选项为默认行为。

2. 如果将 **ForwardClientCertificate** 设置为 **true**，在反向代理与客户端执行 SSL 握手期间，反向代理会请求客户端的证书。
然后会转发名为 X-Client-Certificate 的自定义 HTTP 标头中的客户端证书数据  。 标头值是客户端证书的 base64 编码 PEM 格式字符串。 检查证书数据后，服务可以接受/拒绝请求，并显示相应状态代码。
如果客户端不提供证书，反向代理将转发空标头，并让服务处理该情况。

> [!NOTE]
> 反向代理只是一个转发程序。 它不会对客户端证书执行验证。


## <a name="next-steps"></a>后续步骤
* [在群集上设置和配置反向代理](service-fabric-reverseproxy-setup.md)。
* 请参阅[将反向代理配置为连接到安全服务](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services)了解 Azure 资源管理器模板示例，使用其他服务证书验证选项配置安全反向代理。
* 参阅 [GitHub 上的示例项目](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)中服务之间的 HTTP 通信示例。
* [使用 Reliable Services 远程控制执行远程过程调用](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services 中使用 OWIN 的 Web API](service-fabric-reliable-services-communication-webapi.md)
* [管理群集证书](service-fabric-cluster-security-update-certs-azure.md)
