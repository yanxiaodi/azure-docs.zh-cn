---
title: 删除虚拟网关：PowerShell：Azure 资源管理器 | Microsoft Docs
description: 在 Resource Manager 部署模型中使用 PowerShell 删除虚拟网络网关。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: 7b9503b2db14d4de6c4c8cf983c42bccd6f9f8fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157430"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>使用 PowerShell 删除虚拟网络网关
> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell（经典）](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

可以使用多种不同的方法来删除 VPN 网关配置中的虚拟网络网关。

- 如果要删除所有信息并从头开始配置（例如，在测试环境中），可以删除资源组。 删除某个资源组时，会删除该组中的所有资源。 仅当不想保留资源组中的任何资源时，才建议使用此方法。 使用这种方法时，无法做到有选择性地删除一部分资源。

- 如果想要保留资源组中的某些资源，则删除虚拟网络网关的过程会略微复杂一些。 在删除虚拟网络网关之前，必须先删除任何依赖于该网关的资源。 遵循的步骤取决于创建的连接类型，以及每个连接的依赖资源。

## <a name="before-beginning"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1.下载最新的 Azure 资源管理器 PowerShell cmdlet。

下载并安装最新版本的 Azure 资源管理器 PowerShell cmdlet。 有关下载和安装 PowerShell cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

### <a name="2-connect-to-your-azure-account"></a>2.连接到 Azure 帐户。

打开 PowerShell 控制台并连接到帐户。 使用以下示例帮助建立连接：

```powershell
Connect-AzAccount
```

检查该帐户的订阅。

```powershell
Get-AzSubscription
```

如果有多个订阅，请指定要使用的订阅。

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="S2S"></a>删除站点到站点 VPN 网关

若要删除 S2S 配置中的虚拟网络网关，必须先删除与该网关相关的每个资源。 由于存在依赖关系，必须按特定的顺序删除资源。 在以下示例中，必须指定某些值，而其他值是输出结果。 为方便演示，我们在示例中使用了以下特定值：

VNet 名称：VNet1<br>
资源组名称：RG1<br>
虚拟网络网关名称：GW1<br>

以下步骤适用于 Resource Manager 部署模型。

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1.单击要删除的虚拟网络网关。

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2.检查该虚拟网络网关是否已建立任何连接。

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3.删除所有连接。

系统可能会提示确认是否要删除每个连接。

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4.删除虚拟网络网关。

系统可能会提示确认是否要删除该网关。 除了 S2S 配置，如果还有此 VNet 的 P2S 配置，则删除虚拟网络网关会自动断开所有 P2S 客户端且不发出警告。


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

此时，虚拟网络网关已被删除。 可以使用接下来的步骤删除不再使用的任何资源。

### <a name="5-delete-the-local-network-gateways"></a>5 删除本地网络网关。

获取相应本地网络网关的列表。

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

删除本地网络网关。 系统可能会提示确认是否要删除每个本地网络网关。

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6.删除公共 IP 地址资源。

获取虚拟网络网关的 IP 配置。

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

获取此虚拟网络网关使用的公共 IP 地址资源列表。 如果虚拟网络网关采用主动-主动配置，会显示两个公共 IP 地址。

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

删除公共 IP 资源。

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7.删除网关子网并设置配置。

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="v2v"></a>删除 VNet 到 VNet VPN 网关

若要删除 V2V 配置中的虚拟网络网关，必须先删除与该网关相关的每个资源。 由于存在依赖关系，必须按特定的顺序删除资源。 在以下示例中，必须指定某些值，而其他值是输出结果。 为方便演示，我们在示例中使用了以下特定值：

VNet 名称：VNet1<br>
资源组名称：RG1<br>
虚拟网络网关名称：GW1<br>

以下步骤适用于 Resource Manager 部署模型。

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1.单击要删除的虚拟网络网关。

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2.检查该虚拟网络网关是否已建立任何连接。

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
与虚拟网络网关建立的其他连接可能属于不同的资源组。 检查其他每个资源组中的其他连接。 在此示例中，我们检查来自 RG2 的连接。 请针对可能与虚拟网络网关建立了连接的每个资源组运行此步骤。

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3.获取两个方向的连接列表。

由于这是一种 VNet 到 VNet 配置，因此需要获取两个方向的连接列表。

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
在此示例中，我们会检查来自 RG2 的连接。 请针对可能与虚拟网络网关建立了连接的每个资源组运行此步骤。

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4.删除所有连接。

系统可能会提示确认是否要删除每个连接。

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5.删除虚拟网络网关。

系统可能会提示确认是否要删除该虚拟网络网关。 除了 V2V 配置，如果还有此 VNet 的 P2S 配置，则删除虚拟网络网关会自动断开所有 P2S 客户端且不发出警告。

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

此时，虚拟网络网关已删除。 可以使用接下来的步骤删除不再使用的任何资源。

### <a name="6-delete-the-public-ip-address-resources"></a>6.删除公共 IP 地址资源

获取虚拟网络网关的 IP 配置。

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

获取此虚拟网络网关使用的公共 IP 地址资源列表。 如果虚拟网络网关采用主动-主动配置，会显示两个公共 IP 地址。

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

删除公共 IP 资源。 系统可能会提示确认是否要删除该公开 IP。

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7.删除网关子网并设置配置。

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="deletep2s"></a>删除点到站点 VPN 网关

若要删除 P2S 配置中的虚拟网络网关，必须先删除与该网关相关的每个资源。 由于存在依赖关系，必须按特定的顺序删除资源。 在以下示例中，必须指定某些值，而其他值是输出结果。 为方便演示，我们在示例中使用了以下特定值：

VNet 名称：VNet1<br>
资源组名称：RG1<br>
虚拟网络网关名称：GW1<br>

以下步骤适用于 Resource Manager 部署模型。


>[!NOTE]
> 删除 VPN 网关时，所有连接的客户端将与 VNet 断开连接且不发出警告。
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1.单击要删除的虚拟网络网关。

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2.删除虚拟网络网关。

系统可能会提示确认是否要删除该虚拟网络网关。

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

此时，虚拟网络网关已被删除。 可以使用接下来的步骤删除不再使用的任何资源。

### <a name="3-delete-the-public-ip-address-resources"></a>3.删除公共 IP 地址资源

获取虚拟网络网关的 IP 配置。

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

获取此虚拟网络网关使用的公共 IP 地址列表。 如果虚拟网络网关采用主动-主动配置，会显示两个公共 IP 地址。

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

删除公共 IP。 系统可能会提示确认是否要删除该公开 IP。

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4.删除网关子网并设置配置。

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete"></a>通过删除资源组来删除 VPN 网关

如果不关心是否要保留资源组中的任何资源，而只是要从头开始配置，则可以删除整个资源组。 这种方法可以快速删除所有信息。 以下步骤仅适用于 Resource Manager 部署模型。

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1.获取订阅中所有资源组的列表。

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2.找到想要删除的资源组。

找到想要删除的资源组，并查看该资源组中的资源列表。 在本示例中，资源组的名称为 RG1。 请修改示例以检索所有资源的列表。

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3.检查列表中的资源。

返回列表后，请检查该列表，确认要删除该资源组中的所有资源以及资源组本身。 如果要保留资源组中的某些资源，请使用本文之前部分中的步骤删除网关。

### <a name="4-delete-the-resource-group-and-resources"></a>4.删除资源组和资源。

要删除资源组及其包含的所有资源，请修改本示例，并运行。

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5.检查状态。

Azure 需要花费一段时间来删除所有资源。 可以使用以下 cmdlet 检查资源组的状态。

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

返回的结果显示“Succeeded”。

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
