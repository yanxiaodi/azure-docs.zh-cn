---
title: 配置 ExpressRoute 和站点到站点 VPN 连接 - 并存：PowerShell：Azure | Microsoft Docs
description: 使用 PowerShell 为资源管理器模型配置可共存的 ExpressRoute 连接和站点到站点 VPN 连接。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: fdd267937db589156aa5eddc7608323b143266de
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508844"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>使用 PowerShell 配置 ExpressRoute 和站点到站点共存连接
> [!div class="op_single_selector"]
> * [PowerShell - 资源管理器](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - 经典](expressroute-howto-coexist-classic.md)
> 
> 

本文有助于配置可共存的 ExpressRoute 和站点到站点 VPN 连接。 能够配置站点到站点 VPN 和 ExpressRoute 具有多项优势。 可以将站点到站点 VPN 配置为 ExpressRoute 的安全故障转移路径，或者使用站点到站点 VPN 连接到不是通过 ExpressRoute 进行连接的站点。 我们会在本文中介绍这两种方案的配置步骤。 本文适用于 Resource Manager 部署模型。

配置站点到站点 VPN 和 ExpressRoute 共存连接具有多项优势：

* 可以将站点到站点 VPN 配置为 ExpressRoute 的安全故障转移路径。 
* 另外，还可以使用站点到站点 VPN 连接到未通过 ExpressRoute 连接的站点。 

本文中介绍了这两种方案的配置步骤。 本文适用于 Resource Manager 部署模型并使用 PowerShell。 也可以使用 Azure 门户配置这些方案，但文档尚不可用。 可以先配置任一网关。 通常，添加新网关或网关连接时不会导致停机。

>[!NOTE]
>如果想要通过 ExpressRoute 线路创建站点到站点 VPN，请参阅[此文](site-to-site-vpn-over-microsoft-peering.md)。
>

## <a name="limits-and-limitations"></a>限制和局限性
* **不支持传输路由。** 无法在通过站点到站点 VPN 连接的本地网络与通过 ExpressRoute 连接的本地网络之间进行路由（通过 Azure）。
* **不支持基本 SKU 网关。** 必须为 [ExpressRoute 网关](expressroute-about-virtual-network-gateways.md)和 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)使用非基本 SKU 网关。
* **仅支持基于路由的 VPN 网关。** 必须使用基于路由的 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)。
* **应该为 VPN 网关配置静态路由。** 如果本地网络同时连接到 ExpressRoute 和站点到站点 VPN，则必须在本地网络中配置静态路由，以便将站点到站点 VPN 连接路由到公共 Internet。
* **如果未指定，则 VPN 网关将默认为 ASN 65515。** Azure VPN 网关支持 BGP 路由协议。 通过添加 -Asn 开关，可为虚拟网络指定 ASN（AS 编号）。 如果未指定此参数，则默认 AS 编号为 65515。 可以将任何 ASN 用于配置，但如果选择 65515 以外的其他 ASN，则必须重置网关才能使设置生效。

## <a name="configuration-designs"></a>配置设计
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>将站点到站点 VPN 配置为 ExpressRoute 的故障转移路径
可以将站点到站点 VPN 连接配置为 ExpressRoute 的备份。 此连接仅适用于链接到 Azure 专用对等互连路径的虚拟网络。 对于可通过 Azure Microsoft 对等互连访问的服务，没有基于 VPN 的故障转移解决方案。 ExpressRoute 线路始终是主链接。 仅当 ExpressRoute 线路失败时，数据才会流经站点到站点 VPN 路径。 若要避免不对称路由，本地网络配置还应当引用基于站点到站点 VPN 的 ExpressRoute 线路。 对于接收 ExpressRoute 的路由，可以通过设置更高的本地优先级来首选 ExpressRoute 路径。 

> [!NOTE]
> 虽然在两个路由相同的情况下 ExpressRoute 线路优先于站点到站点 VPN，Azure 仍会使用最长的前缀匹配来选择指向数据包目标的路由。
> 
> 

![共存](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>配置站点到站点 VPN，以便连接到不通过 ExpressRoute 进行连接的站点
可以对网络进行配置，使得部分站点通过站点到站点 VPN 直接连接到 Azure，部分站点通过 ExpressRoute 进行连接。 

![共存](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> 不能将虚拟网络配置为转换路由器。
> 
> 

## <a name="selecting-the-steps-to-use"></a>选择要使用的步骤
有两组不同的过程可供选择。 选择的配置过程取决于是要连接到现有虚拟网络，还是要创建新的虚拟网络。

* 我没有 VNet，需要创建一个。
  
    如果没有虚拟网络，此过程将指导你使用 Resource Manager 部署模型创建新的虚拟网络，然后创建新的 ExpressRoute 和站点到站点 VPN 连接。 若要配置虚拟网络，请遵循[创建新的虚拟网络和并存连接](#new)中的步骤。
* 我已有一个 Resource Manager 部署模型 VNet。
  
    可能已在具有现有站点到站点 VPN 连接或 ExpressRoute 连接的位置拥有虚拟网络。 在此场景下，如果网关子网掩码为 /28 或更小（/28、/29、等等），则必须删除现有网关。 [为现有的 VNet 配置并存连接](#add) 部分指导删除网关，然后创建新的 ExpressRoute 连接和站点到站点 VPN 连接。
  
    如果删除并重新创建网关，则跨界连接将会中断一段时间。 但是，在配置网关时，如果进行了相应配置，VM 和服务仍可以通过负载均衡器与外界通信。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="new"></a>创建新的虚拟网络和并存连接
本过程指导创建 VNet 以及将共存的站点到站点连接和 ExpressRoute 连接。 针对此配置使用的 cmdlet 可能与你熟悉的 cmdlet 稍有不同。 请务必使用说明内容中指定的 cmdlet。

1. 登录并选择订阅。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. 设置变量。

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. 创建包括网关子网的虚拟网络。 有关创建虚拟网络的详细信息，请参阅[创建虚拟网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 有关创建子网的详细信息，请参阅[创建子网](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)。
   
   > [!IMPORTANT]
   > 网关子网必须是 /27 或更短的前缀（例如 /26 或 /25）。
   > 
   > 
   
    创建新的 VNet。

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    添加子网。

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    保存 VNet 配置。

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>接下来，创建站点到站点 VPN 网关。 有关 VPN 网关配置的详细信息，请参阅[使用站点到站点连接配置 VNet](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)。 只有 *VpnGw1*、*VpnGw2*、*VpnGw3*、*标准*和*高性能* VPN 网关支持 GatewaySku。 基本 SKU 不支持 ExpressRoute-VPN 网关共存配置。 VpnType 必须为 *RouteBased*。

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    Azure VPN 网关支持 BGP 路由协议。 通过在以下命令中添加 -Asn 开关，可为该虚拟网络指定 ASN（AS 编号）。 若未指定该参数，将默认为 AS 编号 65515。

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    可以在 $azureVpn.BgpSettings.BgpPeeringAddress 和 $azureVpn.BgpSettings.Asn 中找到 Azure 用于 VPN 网关的 BGP 对等 IP 和 AS 编号。 有关详细信息，请参阅为 [Azure VPN 网关配置 BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md)。
5. 创建一个本地站点 VPN 网关实体。 此命令不会配置本地 VPN 网关， 而是允许提供本地网关设置（如公共 IP 和本地地址空间），以便 Azure VPN 网关可以连接到它。
   
    如果本地 VPN 设备仅支持静态路由，可按以下方式配置静态路由：

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    如果本地 VPN 设备支持 BGP，并且想要启用动态路由，那么需要知道本地 VPN 设备使用的 BGP 对等 IP 和 AS 编号。

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. 配置本地 VPN 设备以连接到新的 Azure VPN 网关。 有关 VPN 设备配置的详细信息，请参阅 [VPN 设备配置](../vpn-gateway/vpn-gateway-about-vpn-devices.md)。

7. 将 Azure 上的站点到站点 VPN 网关连接到本地网关。

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. 如果连接到现有 ExpressRoute 线路，请跳过步骤 8 和 9，并跳到步骤 10。 配置 ExpressRoute 线路。 有关配置 ExpressRoute 线路的详细信息，请参阅[创建 ExpressRoute 线路](expressroute-howto-circuit-arm.md)。


9. 配置基于 ExpressRoute 线路的 Azure 专用对等互连。 有关配置基于 ExpressRoute 线路的 Azure 专用对等互连的详细信息，请参阅[配置对等互连](expressroute-howto-routing-arm.md)

10. <a name="gw"></a>创建 ExpressRoute 网关。 有关 ExpressRoute 网关配置的详细信息，请参阅 [ExpressRoute 网关配置](expressroute-howto-add-gateway-resource-manager.md)。 GatewaySKU 必须是 *Standard*、*HighPerformance* 或 *UltraPerformance*。

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. 将 ExpressRoute 网关连接到 ExpressRoute 线路。 完成此步骤后，则已通过 ExpressRoute 建立本地网络与 Azure 之间的连接。 有关链接操作的详细信息，请参阅[将 VNet 链接到 ExpressRoute](expressroute-howto-linkvnet-arm.md)。

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="add"></a>为现有的 VNet 配置并存连接
如果你的虚拟网络只有一个虚拟网络网关（例如，站点到站点 VPN 网关），并且你想要添加另一个不同类型的网关（例如，ExpressRoute 网关），请检查网关子网大小。 如果网关子网为 /27 或更大，则可以跳过以下步骤并按照上一部分中的步骤添加站点到站点 VPN 网关或 ExpressRoute 网关。 如果网关子网为 /28 或 /29，则必须先删除虚拟网络网关，然后增加网关子网大小。 本部分的步骤说明如何这样做。

针对此配置使用的 cmdlet 可能与你熟悉的 cmdlet 稍有不同。 请务必使用说明内容中指定的 cmdlet。

1. 删除现有的 ExpressRoute 或站点到站点 VPN 网关。

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. 删除网关子网。

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. 添加为 /27 或更大的网关子网。
   
   > [!NOTE]
   > 如果因为虚拟网络中没有剩余足够的 IP 地址而无法增加网关子网大小，则需增加 IP 地址空间。
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    保存 VNet 配置。

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. 此时，已有不带网关的虚拟网络。 若要创建新的网关，并将连接设置，请使用下面的示例：

   设置变量。

    ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   ```

   创建网关。

   ```azurepowershell-interactive
   $gw = New-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup> -Location <yourlocation) -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
   ```

   创建连接。

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
   New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
   ```

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>将点到站点配置添加到 VPN 网关

可以按照下面的步骤将点到站点配置添加到共存设置中的 VPN 网关。 若要上传 VPN 根证书，必须以本地方式将 PowerShell 安装到计算机，或者使用 Azure 门户。

1. 添加 VPN 客户端地址池。

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. 为 VPN 网关将 VPN 根证书上传到 Azure。 在此示例中，假定根证书存储在运行以下 PowerShell cmdlet 的本地计算机中，并且你在本地运行 PowerShell。 也可使用 Azure 门户来上传证书。

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```

有关点到站点 VPN 的详细信息，请参阅 [配置点到站点连接](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)。

## <a name="next-steps"></a>后续步骤
有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
