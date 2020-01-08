---
title: 创建自定义探测 - Azure 应用程序网关 - PowerShell 经典 | Microsoft Docs
description: 了解如何使用经典部署模型中的 PowerShell 创建应用程序网关的自定义探测
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 338a7be1-835c-48e9-a072-95662dc30f5e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 01c1768f60da98206f0dfd041745428256f545fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "58861873"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>使用 PowerShell 创建 Azure 应用程序网关（经典）的自定义探测

> [!div class="op_single_selector"]
> * [Azure 门户](application-gateway-create-probe-portal.md)
> * [Azure 资源管理器 PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 经典 PowerShell](application-gateway-create-probe-classic-ps.md)

在本文中，将使用 PowerShell 向现有应用程序网关添加自定义探测。 如果应用程序包含特定运行状况检查页面。或者未在默认 Web 应用程序上提供成功的响应，那么它们非常适合使用自定义探测。

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 了解如何[使用 Resource Manager 模型执行这些步骤](application-gateway-create-probe-ps.md)。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>创建应用程序网关

创建应用程序网关：

1. 创建应用程序网关资源。
2. 创建配置 XML 文件或配置对象。
3. 将配置提交到新建的应用程序网关资源。

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>创建使用自定义探测的应用程序网关资源

要创建网关，请使用 `New-AzureApplicationGateway` cmdlet，并将值替换成自己的值。 此时不会开始计收网关的费用。 计费会在后面已成功启动网关时开始。

以下示例使用名为“testvnet1”的虚拟网络和名为“subnet-1”的子网创建应用程序网关。

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

若要验证是否已创建网关，可以使用 `Get-AzureApplicationGateway` cmdlet。

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> *InstanceCount* 的默认值为 2，最大值为 10。 *GatewaySize* 的默认值为 Medium。 可以选择 Small、Medium 或 Large。
> 
> 

*VirtualIPs* 和 *DnsName* 显示为空白，因为网关尚未启动。 这些值在网关进入运行状态后立即创建。

### <a name="configure-an-application-gateway-by-using-xml"></a>使用 XML 配置应用程序网关

在以下示例中，使用 XML 文件配置所有应用程序网关设置，并将这些设置提交到应用程序网关资源。  

将以下文本复制到记事本中。

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

编辑配置项的括号之间的值。 使用扩展名 .xml 保存文件。

以下示例演示如何使用配置文件设置应用程序网关负载均衡公共端口 80 上的 HTTP 流量，然后使用自定义探测将网络流量发送到两个 IP 地址之间的后端端口 80。

> [!IMPORTANT]
> 协议项 Http 或 Https 区分大小写。

已添加用于配置自定义探测的新配置项 \<Probe\>。

配置参数为：

|参数|描述|
|---|---|
|**名称** |自定义探测的引用名称。 |
| 协议  | 使用的协议（可能的值为 HTTP 或 HTTPS）。|
| Host  和 Path  | 应用程序网关为了确定实例运行状况而调用的完整 URL 路径。 例如，如果网站为 http:\//contoso.com/，则可以为“http:\//contoso.com/path/custompath.htm”配置自定义探测，使探测检查能够获得成功的 HTTP 响应。|
| 间隔  | 配置探测检查间隔，以秒为单位。|
| **超时** | 定义 HTTP 响应检查的探测超时。|
| UnhealthyThreshold  | 将后端实例标记为“不正常”  所需的失败 HTTP 响应数目。|

\<BackendHttpSettings\> 配置中会引用探测名称，以分配使用自定义探测设置的后端池。

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>将自定义探测添加到现有应用程序网关

更改当前的应用程序网关配置需要完成三个步骤：获取当前的 XML 配置文件，对其进行修改以创建自定义探测，使用新的 XML 设置来配置应用程序网关。

1. 使用 `Get-AzureApplicationGatewayConfig` 获取 XML 文件。 此 cmdlet 会导出要修改的配置 XML 以添加探测设置。

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. 在文本编辑器中打开 XML 文件。 将 `<probe>` 节添加到 `<frontendport>` 的后面。

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   在 XML 的 backendHttpSettings 节中，添加以下示例中所示的探测名称：

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   保存该 XML 文件。

1. 通过 `Set-AzureApplicationGatewayConfig` 使用新的 XML 文件更新应用程序网关配置。 此 cmdlet 使用新配置更新应用程序网关。

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>后续步骤

如果要配置安全套接字层 (SSL) 卸载，请参阅[配置应用程序网关以进行 SSL 卸载](application-gateway-ssl.md)。

如果要将应用程序网关配置为与内部负载均衡器配合使用，请参阅 [Create an application gateway with an internal load balancer (ILB)](application-gateway-ilb.md)（创建具有内部负载均衡器 (ILB) 的应用程序网关）。

