---
title: 搭配使用 Azure 应用程序网关和内部负载均衡器 - PowerShell | Microsoft Docs
description: 本页提供有关使用 Azure 资源管理器创建、配置、启动和删除具有内部负载均衡器 (ILB) 的 Azure 应用程序网关的说明
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2018
ms.author: victorh
ms.openlocfilehash: 70b350e228785e47a41cb83ce0d80b93c8a601c1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66135224"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>创建具有内部负载均衡器 (ILB) 的应用程序网关

可以配置使用面向 Internet 的 VIP 或不向 Internet 公开的内部终结点（也称为内部负载均衡器 (ILB) 终结点）的 Azure 应用程序网关。 配置使用 ILB 的网关适用于不向 Internet 公开的内部业务线应用程序。 对于位于不向 Internet 公开的安全边界内的多层应用程序中的服务和层也很有用，但仍需要执行循环负载分散、会话粘性或安全套接字层 (SSL) 终止。

本文介绍如何配置具有 ILB 的应用程序网关。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 按照[安装说明](/powershell/azure/install-az-ps)安装最新版本的 Azure PowerShell 模块。
2. 将为应用程序网关创建虚拟网络和子网。 请确保没有虚拟机或云部署正在使用子网。 应用程序网关必须单独位于虚拟网络子网中。
3. 必须存在配置为使用应用程序网关的服务器，或者必须在虚拟网络中为其创建终结点，或者必须为其分配公共 IP/VIP。

## <a name="what-is-required-to-create-an-application-gateway"></a>创建应用程序网关需要什么？

* **后端服务器池：** 后端服务器的 IP 地址列表。 列出的 IP 地址应属于虚拟网络子网但位于应用程序网关的不同子网中，或者是公共 IP/VIP。
* **后端服务器池设置：** 每个池均具有端口、协议和基于 Cookie 的相关性等设置。 这些设置绑定到池，并会应用到池中的所有服务器。
* **前端端口：** 此端口是应用程序网关上打开的公共端口。 流量将抵达此端口，并重定向到后端服务器之一。
* **侦听器：** 侦听器具有前端端口、协议（Http 或 Https，区分大小写）和 SSL 证书名称（如果要配置 SSL 卸载）。
* **规则：** 规则会绑定侦听器和后端服务器池，并定义当流量抵达特定侦听器时应定向到哪个后端服务器池。 目前仅支持 *基本* 规则。 *基本* 规则是一种轮循负载分发模式。

## <a name="create-an-application-gateway"></a>创建应用程序网关

使用 Azure 经典部署和 Azure 资源管理器部署的差别在于创建应用程序网关的顺序和需要配置的项。
使用 Resource Manager 时，组成应用程序网关的所有项都将分开配置，并结合在一起来创建应用程序网关资源。

以下是创建应用程序网关所需执行的步骤：

1. 创建资源管理器的资源组
2. 为应用程序网关创建虚拟网络和子网
3. 创建应用程序网关配置对象
4. 创建应用程序网关资源

## <a name="create-a-resource-group-for-resource-manager"></a>创建 Resource Manager 的资源组

确保切换 PowerShell 模式，以便使用 Azure 资源管理器 cmdlet。 [将 Windows PowerShell 与资源管理器配合使用](../powershell-azure-resource-manager.md)中提供了详细信息。

### <a name="step-1"></a>步骤 1

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>步骤 2

检查该帐户的订阅。

```powershell
Get-AzSubscription
```

系统会提示使用凭据进行身份验证。

### <a name="step-3"></a>步骤 3

选择要使用的 Azure 订阅。

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>步骤 4

创建新的资源组（如果要使用现有的资源组，请跳过此步骤）。

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

Azure 资源管理器要求所有资源组指定一个位置。 此位置将用作该资源组中的资源的默认位置。 请确保用于创建应用程序网关的所有命令都使用相同的资源组。

在上述示例中，我们创建了名为“appgw-rg”的资源组，位置为“美国西部”。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>为应用程序网关创建虚拟网络和子网

以下示例演示如何使用资源管理器创建虚拟网络：

### <a name="step-1"></a>步骤 1

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

此步骤会将地址范围 10.0.0.0/24 分配给用于创建虚拟网络的子网变量。

### <a name="step-2"></a>步骤 2

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

此步骤将使用前缀 10.0.0.0/16 和子网 10.0.0.0/24，在美国西部区域的“appgw-rg”资源组中创建名为“appgwvnet”的虚拟网络。

### <a name="step-3"></a>步骤 3

```powershell
$subnet = $vnet.subnets[0]
```

此步骤会将子网对象分配到变量 $subnet 以完成后续步骤。

## <a name="create-an-application-gateway-configuration-object"></a>创建应用程序网关配置对象

### <a name="step-1"></a>步骤 1

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

此步骤会创建名为“gatewayIP01”的应用程序网关 IP 配置。 当应用程序网关启动时，它会从配置的子网获取 IP 地址，再将网络流量路由到后端 IP 池中的 IP 地址。 请记住，每个实例需要一个 IP 地址。

### <a name="step-2"></a>步骤 2

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

此步骤配置名为“pool01”、IP 地址为“10.1.1.8, 10.1.1.9, 10.1.1.10”的后端 IP 地址池。 这些 IP 地址将接收来自前端 IP 终结点的网络流量。 替换上述 IP 地址，添加自己的应用程序 IP 地址终结点。

### <a name="step-3"></a>步骤 3

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

此步骤会为后端池中进行了负载均衡的网络流量配置应用程序网关设置“poolsetting01”。

### <a name="step-4"></a>步骤 4

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

此步骤会为 ILB 配置名为“frontendport01”的前端 IP 端口。

### <a name="step-5"></a>步骤 5

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

此步骤会创建名为“fipconfig01”的前端 IP 配置，并将其与当前虚拟网络子网中的某个专用 IP 相关联。

### <a name="step-6"></a>步骤 6

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

此步骤会创建名为“listener01”的侦听器，并将前端端口与前端 IP 配置相关联。

### <a name="step-7"></a>步骤 7

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

此步骤会创建名为“rule01”的负载均衡器路由规则，用于配置负载均衡器的行为。

### <a name="step-8"></a>步骤 8

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

此步骤会配置应用程序网关的实例大小。

> [!NOTE]
> “容量”的默认值为 2。 至于 SKU 名称，可以在 Standard_Small、Standard_Medium 和 Standard_Large 之间进行选择。

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>使用 New-AzureApplicationGateway 创建应用程序网关

创建包含上述步骤中所有配置项目的应用程序网关。 示例中的应用程序网关名为“appgwtest”。

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

此步骤会创建包含上述步骤中所有配置项目的应用程序网关。 示例中的应用程序网关名为“appgwtest”。

## <a name="delete-an-application-gateway"></a>删除应用程序网关

若要删除应用程序网关，请按顺序执行以下步骤：

1. 使用 `Stop-AzApplicationGateway` cmdlet 停止该网关。
2. 使用 `Remove-AzApplicationGateway` cmdlet 删除该网关。
3. 使用 `Get-AzureApplicationGateway` cmdlet 验证是否已删除该网关。

### <a name="step-1"></a>步骤 1

获取应用程序网关对象，并将其关联到变量“$getgw”。

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>步骤 2

使用 `Stop-AzApplicationGateway` 停止应用程序网关。 此示例在第一行显示 `Stop-AzApplicationGateway` cmdlet，接着显示输出。

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

在应用程序网关进入停止状态后，使用 `Remove-AzApplicationGateway` cmdlet 删除该服务。

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> 可以使用 **-force** 开关来禁止显示该删除的确认消息。

若要验证是否已删除服务，可以使用 `Get-AzApplicationGateway` cmdlet。 此步骤不是必需的。

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>后续步骤

如果要配置 SSL 卸载，请参阅 [Configure an application gateway for SSL offload](application-gateway-ssl.md)（配置应用程序网关以进行 SSL 卸载）。

如果要将应用程序网关配置为与 ILB 配合使用，请参阅 [创建具有内部负载均衡器 (ILB) 的应用程序网关](application-gateway-ilb.md)。

如需负载均衡选项的其他常规信息，请参阅：

* [Azure 负载均衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure 流量管理器](https://azure.microsoft.com/documentation/services/traffic-manager/)

