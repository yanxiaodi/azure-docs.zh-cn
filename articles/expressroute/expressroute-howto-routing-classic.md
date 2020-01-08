---
title: 配置线路的对等互连 - ExpressRoute：Azure：经典 | Microsoft Docs
description: 本文指导完成创建和预配 ExpressRoute 线路的专用、公共和 Microsoft 对等互连的步骤。 本文还介绍了如何检查状态，以及如何更新或删除线路的对等互连。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: a57681cc9f44593ceea6b2c1795274c1b16d3a94
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "64726213"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-classic"></a>创建和修改 ExpressRoute 线路的对等互连（经典）
> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [视频 - 专用对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [视频 - 公共对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [视频 - Microsoft 对等互连](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-routing-classic.md)
> 

本文指导执行相关步骤，以便使用 PowerShell 和经典部署模型创建和管理 ExpressRoute 线路的对等互连/路由配置。 下面的步骤还将说明如何查看状态，以及如何更新、删除和取消预配 ExpressRoute 线路的对等互连。 可以为 ExpressRoute 线路配置一到三个对等互连（Azure 专用、Azure 公共和 Microsoft）。 可以按照所选的任意顺序配置对等互连。 但是，必须确保一次只完成一个对等互连的配置。 

这些说明仅适用于由提供第 2 层连接服务的服务提供商创建的线路。 如果服务提供商提供第 3 层托管服务（通常是 IPVPN，如 MPLS），则连接服务提供商会配置和管理路由。

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**关于 Azure 部署模型**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configuration-prerequisites"></a>配置先决条件

* 在开始配置之前，请务必查看[先决条件](expressroute-prerequisites.md)页、[路由要求](expressroute-routing.md)页和[工作流](expressroute-workflows.md)页。
* 必须有一个活动的 ExpressRoute 线路。 在继续下一步之前，请按说明 [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md)，并通过连接提供商启用该线路。 ExpressRoute 线路必须处于已预配和已启用状态，才能运行下述 cmdlet。

### <a name="download-the-latest-powershell-cmdlets"></a>下载最新的 PowerShell cmdlet

安装最新版本的 Azure 服务管理 (SM) PowerShell 模块和 ExpressRoute 模块。 使用以下示例时，请注意，当更新版本的 cmdlet 发布时，版本号（在此示例中为 5.1.1）将更改。

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

有关详细信息，请参阅 [Azure PowerShell cmdlet 入门](/powershell/azure/overview)，其中提供了有关如何配置计算机以使用 Azure PowerShell 模块的分步指导。

### <a name="sign-in"></a>登录

若要登录到 Azure 帐户，请使用以下示例：

1. 使用提升的权限打开 PowerShell 控制台，并连接到帐户。

   ```powershell
   Connect-AzAccount
   ```
2. 检查该帐户的订阅。

   ```powershell
   Get-AzSubscription
   ```
3. 如果有多个订阅，请选择要使用的订阅。

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. 接下来，使用以下 cmdlet 将 Azure 订阅添加到经典部署模型的 PowerShell。

   ```powershell
   Add-AzureAccount
   ```

## <a name="azure-private-peering"></a>Azure 专用对等互连

本部分说明如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 专用对等互连配置。 

### <a name="to-create-azure-private-peering"></a>创建 Azure 专用对等互连

1. **创建 ExpressRoute 线路。**

   请按说明创建 [ExpressRoute 线路](expressroute-howto-circuit-classic.md) ，并由连接服务提供商进行预配。 如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Azure 专用对等互连。 在此情况下，不需要遵循后续部分中所列的说明。 但是，如果连接服务提供商不管理路由，请在创建线路之后遵循以下说明。
2. **检查 ExpressRoute 线路以确保它已预配。**
   
   检查 ExpressRoute 线路是否已预配并已启用。

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   返回：

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   确保线路显示为已预配并已启用。 否则，请咨询连接服务提供商，使线路处于所需状态。

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **配置线路的 Azure 专用对等互连。**

   在继续执行后续步骤之前，请确保已准备好以下各项：
   
   * 主链路的 /30 子网。 它不能是保留给虚拟网络使用的任何地址空间的一部分。
   * 辅助链路的 /30 子网。 它不能是保留给虚拟网络使用的任何地址空间的一部分。
   * 用于建立此对等互连的有效 VLAN ID。 确认线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。 可以将专用 AS 编号用于此对等互连。 确认未使用 65515。
   * MD5 哈希（如果选择使用）。 可选  。
     
   可使用以下示例为线路配置 Azure 专用对等互连：

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100
   ```    

   若要使用 MD5 哈希，请使用以下示例为线路配置 Azure 专用对等互连：

   ```powershell
   New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"
   ```
     
   > [!IMPORTANT]
   > 确认将 AS 编号指定为对等互连 ASN 而不是客户 ASN。
   > 

### <a name="to-view-azure-private-peering-details"></a>查看 Azure 专用对等互连详细信息

可使用以下 cmdlet 来查看配置详细信息：

```powershell
Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

返回：

```
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 100
```

### <a name="to-update-azure-private-peering-configuration"></a>更新 Azure 专用对等互连配置

可以使用以下 cmdlet 来更新配置的任何部分。 在以下示例中，线路的 VLAN ID 将从 100 更新为 500。

```powershell
Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"
```

### <a name="to-delete-azure-private-peering"></a>删除 Azure 专用对等互连

可以运行以下 cmdlet 来删除对等互连配置。 运行此 cmdlet 之前，必须确保已从 ExpressRoute 线路取消链接所有虚拟网络。

```powershell
Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
```

## <a name="azure-public-peering"></a>Azure 公共对等互连

本部分说明如何为 ExpressRoute 线路创建、获取、更新和删除 Azure 公共对等互连配置。

> [!NOTE]
> Azure 公共对等互连不推荐使用适用于新线路。
>

### <a name="to-create-azure-public-peering"></a>创建 Azure 公共对等互连

1. **创建 ExpressRoute 线路**

   请按说明创建 [ExpressRoute 线路](expressroute-howto-circuit-classic.md) ，并由连接服务提供商进行预配。 如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Azure 公共对等互连。 在这种情况下，不需要遵循后续部分中所列的说明。 但是，如果连接服务提供商不管理路由，请在创建线路之后遵循以下说明。
2. **检查 ExpressRoute 线路以确认它已预配**

   首先必须检查 ExpressRoute 线路是否已预配并已启用。

   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   返回：

   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   确认线路显示为已预配并已启用。 否则，请咨询连接服务提供商，使线路处于所需状态。

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
4. **配置线路的 Azure 公共对等互连**
   
   在继续下一步之前，请确保已准备好以下信息：
   
   * 主链路的 /30 子网。 这必须是有效的公共 IPv4 前缀。
   * 辅助链路的 /30 子网。 这必须是有效的公共 IPv4 前缀。
   * 用于建立此对等互连的有效 VLAN ID。 确认线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * MD5 哈希（如果选择使用）。 可选  。

   > [!IMPORTANT]
   > 请确保将 AS 编号指定为对等互连 ASN 而不是客户 ASN。
   >  
     
   可使用以下示例为线路配置 Azure 公共对等互连：

   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200
   ```
     
   若要使用 MD5 哈希，请使用以下示例配置线路：
     
   ```powershell
   New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"
   ```
     
### <a name="to-view-azure-public-peering-details"></a>查看 Azure 公共对等互连详细信息

若要查看配置详细信息，请使用以下 cmdlet：

```powershell
Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

返回：

```powershell
AdvertisedPublicPrefixes       : 
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 131.107.0.0/30
RoutingRegistryName            : 
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 131.107.0.4/30
State                          : Enabled
VlanId                         : 200
```

### <a name="to-update-azure-public-peering-configuration"></a>更新 Azure 公共对等互连配置

可以使用以下 cmdlet 来更新配置的任何部分。 在此示例中，线路的 VLAN ID 将从 200 更新为 600。

```powershell
Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"
```

确认线路显示为已预配并已启用。 
### <a name="to-delete-azure-public-peering"></a>删除 Azure 公共对等互连

可以运行以下 cmdlet 来删除对等互连配置：

```powershell
Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
```

## <a name="microsoft-peering"></a>Microsoft 对等互连

本部分说明如何为 ExpressRoute 线路创建、获取、更新和删除 Microsoft 对等互连配置。 

### <a name="to-create-microsoft-peering"></a>创建 Microsoft 对等互连

1. **创建 ExpressRoute 线路**
  
   请按说明创建 [ExpressRoute 线路](expressroute-howto-circuit-classic.md) ，并由连接服务提供商进行预配。 如果连接服务提供商提供第 3 层托管服务，可以请求连接服务提供商启用 Azure 专用对等互连。 在此情况下，不需要遵循后续部分中所列的说明。 但是，如果连接服务提供商不管理路由，请在创建线路之后遵循以下说明。
2. **检查 ExpressRoute 线路以确认它已预配**

   确认线路显示为已预配并已启用。 
   
   ```powershell
   Get-AzureDedicatedCircuit -ServiceKey "*********************************"
   ```

   返回：
   
   ```powershell
   Bandwidth                        : 200
   CircuitName                      : MyTestCircuit
   Location                         : Silicon Valley
   ServiceKey                       : *********************************
   ServiceProviderName              : equinix
   ServiceProviderProvisioningState : Provisioned
   Sku                              : Standard
   Status                           : Enabled
   ```
   
   确认线路显示为已预配并已启用。 否则，请咨询连接服务提供商，使线路处于所需状态。

   ```powershell
   ServiceProviderProvisioningState : Provisioned
   Status                           : Enabled
   ```
3. **配置线路的 Microsoft 对等互连**
   
    在继续下一步之前，请确保已准备好以下信息。
   
   * 主链路的 /30 子网。 这必须是你拥有的且已在 RIR/IRR 中注册的有效公共 IPv4 前缀。
   * 辅助链路的 /30 子网。 这必须是你拥有的且已在 RIR/IRR 中注册的有效公共 IPv4 前缀。
   * 用于建立此对等互连的有效 VLAN ID。 确认线路中没有其他对等互连使用同一个 VLAN ID。
   * 对等互连的 AS 编号。 可以使用 2 字节和 4 字节 AS 编号。
   * 播发的前缀：必须提供要通过 BGP 会话播发的所有前缀列表。 只接受公共 IP 地址前缀。 如果打算发送一组前缀，可以发送逗号分隔列表。 这些前缀必须已在 RIR/IRR 中注册。
   * 客户 ASN：如果要播发的前缀未注册到对等互连 AS 编号，可以指定它们要注册到的 AS 编号。 可选  。
   * 路由注册表名称：可以指定 AS 编号和前缀要注册到的 RIR/IRR。
   * MD5 哈希（如果选择使用）。 **可选。**
     
   运行以下 cmdlet 为线路配置 Microsoft 对等互连：
 
   ```powershell
   New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
   ```

### <a name="to-view-microsoft-peering-details"></a>查看 Microsoft 对等互连详细信息

可使用以下 cmdlet 来查看配置详细信息：

```powershell
Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```
返回：

```powershell
AdvertisedPublicPrefixes       : 123.0.0.0/30
AdvertisedPublicPrefixesState  : Configured
AzureAsn                       : 12076
CustomerAutonomousSystemNumber : 2245
PeerAsn                        : 1234
PrimaryAzurePort               : 
PrimaryPeerSubnet              : 10.0.0.0/30
RoutingRegistryName            : ARIN
SecondaryAzurePort             : 
SecondaryPeerSubnet            : 10.0.0.4/30
State                          : Enabled
VlanId                         : 300
```

### <a name="to-update-microsoft-peering-configuration"></a>更新 Microsoft 对等互连配置

可以使用以下 cmdlet 来更新配置的任何部分：

```powershell
Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"
```

### <a name="to-delete-microsoft-peering"></a>删除 Microsoft 对等互连

可以运行以下 cmdlet 来删除对等互连配置：

```powershell
Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
```

## <a name="next-steps"></a>后续步骤

接下来，请[将 VNet 链接到 ExpressRoute 线路](expressroute-howto-linkvnet-classic.md)。

* 有关工作流的详细信息，请参阅 [ExpressRoute 工作流](expressroute-workflows.md)。
* 有关线路对等互连的详细信息，请参阅 [ExpressRoute 线路和路由域](expressroute-circuit-peerings.md)。
