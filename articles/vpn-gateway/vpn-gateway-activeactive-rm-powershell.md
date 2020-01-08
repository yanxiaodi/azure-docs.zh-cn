---
title: 为 VPN 网关配置主动-主动 S2S VPN 连接：Azure 资源管理器：PowerShell | Microsoft Docs
description: 本文逐步讲解如何使用 Azure 资源管理器和 PowerShell 配置包含 Azure VPN 网关的主动-主动连接。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: 6d973d81e0de407893beb5c5808962562f091d4c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871828"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>配置与 Azure VPN 网关的主动-主动 S2S VPN 连接

本文逐步讲解如何使用 Resource Manager 部署模型和 PowerShell 创建主动-主动跨界连接与 VNet 到 VNet 连接。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-highly-available-cross-premises-connections"></a>关于高可用性跨界连接
若要实现跨界连接和 VNet 到 VNet 连接的高可用性，应该部署多个 VPN 网关，在网络与 Azure 之间建立多个并行连接。 有关连接选项和拓扑的概述，请参阅[高可用性跨界连接与 VNet 到 VNet 连接](vpn-gateway-highlyavailable.md)。

本文提供有关设置两个虚拟网络之间的主动-主动跨界 VPN 连接以及主动-主动连接的说明。

* [第 1 部分 - 创建并配置采用主动-主动模式的 Azure VPN 网关](#aagateway)
* [第 2 部分 - 建立主动-主动跨界连接](#aacrossprem)
* [第 3 部分 - 建立主动-主动 VNet 到 VNet 连接](#aav2v)

如果已有 VPN 网关，则可以：
* [将现有 VPN 网关从主动-待机更新至主动-主动，或反之](#aaupdate)

可以将这些选项结合起来，构建符合要求的更复杂、高度可用的网络拓扑。

> [!IMPORTANT]
> 主动-主动模式仅使用以下 SKU： 
>   * VpnGw1、VpnGw2、VpnGw3
>   * HighPerformance（适用于旧的遗留 SKU）

## <a name ="aagateway"></a>第 1 部分 - 创建并配置主动-主动 VPN 网关
以下步骤将 Azure VPN 网关配置为主动-主动模式。 主动-主动与主机-待机网关之间的重要差异：

* 需要使用两个公共 IP 地址创建两个网关 IP 配置
* 需要设置 EnableActiveActiveFeature 标志
* 网关 SKU 必须为 VpnGw1、VpnGw2、VpnGw3 或 HighPerformance（旧 SKU）。

其他属性与非主动-主动网关相同。 

### <a name="before-you-begin"></a>开始之前
* 确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 需要安装 Azure 资源管理器 PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅 [Azure PowerShell 概述](/powershell/azure/overview)。

### <a name="step-1---create-and-configure-vnet1"></a>步骤 1 - 创建并配置 VNet1
#### <a name="1-declare-your-variables"></a>1.声明变量
对于本练习，我们首先要声明变量。 以下示例使用此练习中的值来声明变量。 请务必在配置生产环境时，使用自己的值来替换该值。 如果执行这些步骤是为了熟悉此类型的配置，则可以使用这些变量。 修改变量，然后将其复制并粘贴到 PowerShell 控制台中。

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2.连接到订阅并创建新资源组
确保切换到 PowerShell 模式，以便使用Resource Manager cmdlet。 有关详细信息，请参阅[将 Windows PowerShell 与资源管理器配合使用](../powershell-azure-resource-manager.md)。

打开 PowerShell 控制台并连接到帐户。 使用下面的示例来帮助连接：

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3.创建 TestVNet1
以下示例创建一个名为 TestVNet1 的虚拟网络和三个子网：一个名为 GatewaySubnet、一个名为 FrontEnd，还有一个名为 Backend。 替换值时，请务必始终将网关子网特意命名为 GatewaySubnet。 如果命名为其他名称，网关创建会失败。

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>步骤 2 - 使用主动-主动模式创建 TestVNet1 的 VPN 网关
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1.创建公共 IP 地址和网关 IP 配置
请求两个公共 IP 地址，分配给要为 VNet 创建的网关。 还将定义所需的子网和 IP 配置。

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2.使用主动-主动配置创建 VPN 网关
为 TestVNet1 创建虚拟网络网关。 请注意有两个 GatewayIpConfig 条目，并且已设置 EnableActiveActiveFeature 标志。 创建网关可能需要一些时间（45 分钟或更久）。

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3.获取网关公共 IP 地址和 BGP 对等 IP 地址
创建网关后，需要在 Azure VPN 网关上获取 BGP 对等节点 IP 地址。 需要此地址才能将 Azure VPN 网关配置为本地 VPN 设备的 BGP 对等节点。

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

使用以下 cmdlet 显示针对 VPN 网关分配的两个公共 IP 地址，以及每个网关实例的对应 BGP 对等 IP 地址：

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

网关实例的公共 IP 地址顺序与对应的 BGP 对等连接地址相同。 在本示例中，公共 IP 为 40.112.190.5 的网关 VM 将使用 10.12.255.4 作为其 BGP 对等连接地址，公共 IP 为 138.91.156.129 的网关将使用 10.12.255.5。 设置连接到主动-主动网关的本地 VPN 设备时需要此信息。 下图显示了网关和所有地址：

![主动-主动网关](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

创建网关后，可以使用此网关创建主动-主动跨界连接或 VNet 到 VNet 连接。 以下各节介绍完成该练习所需的步骤。

## <a name ="aacrossprem"></a>第 2 部分 - 建立主动-主动跨界连接
要建立跨界连接，需要创建本地网关来表示本地 VPN 设备，并创建连接将 Azure VPN 网关与本地网关连接在一起。 在本示例中，Azure VPN 网关处于主动-主动模式。 因此，即使只有一个本地 VPN 设备（本地网络网关）和一个连接资源，两个 Azure VPN 网关实例也都与该本地设备建立 S2S VPN 隧道。

在继续下一步之前，请确保已完成本练习的[第 1 部分](#aagateway)。

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>步骤 1 - 创建和配置本地网关
#### <a name="1-declare-your-variables"></a>1.声明变量
此练习将继续生成图中所示的配置。 请务必将值替换为用于配置的值。

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

关于本地网关参数，有几个事项需要注意：

* 本地网关可以与 VPN 网关在相同或不同的位置和资源组中。 本示例显示它们位于不同的资源组，但位于相同的 Azure 位置。
* 如果只有一个本地 VPN 设备（如上所示），则不管是否使用 BGP 协议，主动-主动连接都可正常工作。 本示例对跨界连接使用 BGP。
* 如果 BGP 已启用，需要为本地网关声明的最小前缀是 VPN 设备上的 BGP 对等节点 IP 地址中的主机地址。 在此示例中，它是“10.52.255.253/32”中的 /32 前缀。
* 提醒一下，在本地网络与 Azure VNet 之间必须使用不同的 BGP ASN。 如果它们是相同的，则需要更改 VNet ASN（如果本地 VPN 设备已使用该 ASN 与其他 BGP 邻居对等）。

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2.为 Site5 创建本地网关
继续操作之前，请确保仍与订阅 1 保持连接。 创建资源组（如果尚未创建）。

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>步骤 2 - 连接 VNet 网关和本地网关
#### <a name="1-get-the-two-gateways"></a>1.获取这两个网关

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2.创建 TestVNet1 到 Site5 的连接
在本步骤中，创建从 TestVNet1 到 Site5_1 的连接，其“EnableBGP”设置为 $True。

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3.本地 VPN 设备的 VPN 和 BGP 参数
下面的示例列出了可在本地 VPN 设备上的 BGP 配置节中为此练习输入的参数：

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

连接应在几分钟后建立，BGP 对等会话会在建立 IPsec 连接后启动。 本示例到目前为止只配置了一个本地 VPN 设备，如下图所示：

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>步骤 3 - 将两个本地 VPN 设备连接到主动-主动 VPN 网关
如果同一个本地网络上有两个 VPN 设备，可以通过将 Azure VPN 网关连接到第二个 VPN 设备来实现双重冗余。

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1.为 Site5 创建第二个本地网络网关
第二个本地网络网关的网关 IP地址、地址前缀和 BGP 对等连接地址不能与同一个本地网络的前一个本地网络网关重叠。

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2.连接 VNet 网关与第二个本地网络网关
创建从 TestVNet1 到 Site5_2 的连接，其“EnableBGP”设置为 $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3.第二个本地 VPN 设备的 VPN 和 BGP 参数
下面列出了要输入到第二个 VPN 设备的参数：

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

建立连接（隧道）后，便已获得了连接到本地网络和 Azure 的双重冗余 VPN 设备和隧道：

![dual-redundancy-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>第 3 部分 - 建立主动-主动 VNet 到 VNet 连接
本部分使用 BGP 创建主动-主动 VNet 到 VNet 连接。 

下面的说明延续上面所列的前述步骤。 必须完成[第 1 部分](#aagateway)，使用 BGP 创建和配置 TestVNet1 与 VPN 网关。 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>步骤 1 - 创建 TestVNet2 和 VPN 网关
必须确保新虚拟网络的 IP 地址空间 TestVNet2 不与任何 VNet 范围重叠。

在本示例中，虚拟网络属于同一订阅。 可以在不同订阅之间设置 VNet 到 VNet 连接，有关更多详细信息，请参阅[配置 VNet 到 VNet 连接](vpn-gateway-vnet-vnet-rm-ps.md)。 请确保在创建连接时添加“-EnableBgp $True”，以启用 BGP。

#### <a name="1-declare-your-variables"></a>1.声明变量
请务必将值替换为要用于配置的值。

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2.在新资源组中创建 TestVNet2

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3.创建 TestVNet2 的主动-主动 VPN 网关
请求两个公共 IP 地址，分配给要为 VNet 创建的网关。 还将定义所需的子网和 IP 配置。

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

使用 AS 编号和“EnableActiveActiveFeature”标志创建 VPN 网关。 请注意，必须覆盖 Azure VPN 网关上的默认 ASN。 连接的 VNet 的 ASN 必须不同，才能启用 BGP 和传输路由。

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>步骤 2 - 连接 TestVNet1 和 TestVNet2 网关
在本示例中，这两个网关位于同一订阅中。 可以在同一 PowerShell 会话中完成此步骤。

#### <a name="1-get-both-gateways"></a>1.获取这两个网关
请确保登录并连接到订阅 1。

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2.创建两个连接
在此步骤中，将创建从 TestVNet1 到 TestVNet2 的连接，以及从 TestVNet2 到 TestVNet1 的连接。

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> 请确保为这两个连接启用 BGP。
> 
> 

完成这些步骤后，连接会在几分钟内建立，完成具有双重冗余的 VNet 到 VNet 连接后，BGP 对等会话就会启动：

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>更新现有 VPN 网关

此部分有助于将现有 Azure VPN 网关从主动-待机模式更改为主动-主动模式，或反之。

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>将主动-待机网关更改为主动-主动网关

以下示例将主动-待机网关转换为主动-主动网关。 将主动-待机网关更改为主动-主动网关时，也将创建另一个公共 IP 地址，然后添加第二个网关 IP 配置。

#### <a name="1-declare-your-variables"></a>1.声明变量

将以下用于示例的参数替换为个人配置所需的设置，然后声明这些变量。

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

在声明变量后，可以将此示例复制粘贴到 PowerShell 控制台。

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2.创建公共 IP 地址，并添加第二个网关 IP 配置

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3.启用主动-主动模式并更新网关

在此步骤中，启用主动-主动模式并更新网关。 在此示例中，VPN 网关当前正在使用旧的标准 SKU。 但是，主动-主动模式不支持此标准 SKU。 若要将旧的 SKU 调整为受支持的版本（在此情况下，为 HighPerformance），只需指定要使用的受支持旧 SKU。

* 使用此步骤无法将旧的 SKU 更改为新的 SKU。 只能将旧的 SKU 调整为另一个受支持的旧 SKU。 例如，无法将 SKU 从标准更改为 VpnGw1（即使主动-主动支持 VpnGw1 ），因为标准是旧的 SKU，而 VpnGw1 是当前的 SKU。 有关调整和迁移 SKU 的详细信息，请参阅[网关 SKU](vpn-gateway-about-vpngateways.md#gwsku)。

* 如果想要调整当前 SKU 的大小，例如将 VpnGw1 调整为 VpnGw3，可以使用此步骤，因为这些 SKU 都属于相同的 SKU 系列。 为此，可以使用此值：```-GatewaySku VpnGw3```

在你的环境中使用时，如果不需要调整网关大小，也就不需要指定 -GatewaySku。 请注意在此步骤中，必须在 PowerShell 中设置网关对象以触发实际更新。 即使不调整网关，此更新也可能需要花费 30 到 45 分钟。

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>将主动-主动网关更改为主动-待机网关
#### <a name="1-declare-your-variables"></a>1.声明变量

将以下用于示例的参数替换为个人配置所需的设置，然后声明这些变量。

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

声明变量后，获取要删除的 IP 配置的名称。

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2.删除网关 IP 配置并禁用主动-主动模式

使用此示例删除网关 IP 配置并禁用主动-主动模式。 请注意，必须在 PowerShell 中设置网关对象以触发实际更新。

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

这种更新最多可能需要 30 到 45 分钟。

## <a name="next-steps"></a>后续步骤
连接完成后，即可将虚拟机添加到虚拟网络。 请参阅 [创建虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 以获取相关步骤。
