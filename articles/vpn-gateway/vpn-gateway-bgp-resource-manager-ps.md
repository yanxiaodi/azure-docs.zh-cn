---
title: 在 Azure VPN 网关上配置 BGP：资源管理器：PowerShell | Microsoft Docs
description: 本文指导完成使用 Azure 资源管理器和 PowerShell 通过 Azure VPN 网关配置 BGP。
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: d7a84bfda06b5db30afff6322c63a056a414357b
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626573"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>如何使用 PowerShell 在 Azure VPN 网关上配置 BGP
本文介绍使用 Resource Manager 部署模型和 PowerShell 在跨界站点到站点 (S2S) VPN 连接和 VNet 到 VNet 连接上启用 BGP 的步骤。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-bgp"></a>关于 BGP
BGP 是通常在 Internet 上使用的，用于在两个或更多网络之间交换路由和可访问性信息的标准路由协议。 BGP 允许 Azure VPN 网关和本地 VPN 设备（称为 BGP 对等节点或邻居）交换“路由”，这些路由将通知这两个网关这些前缀的可用性和可访问性，以便这些前缀可通过涉及的网关或路由器。 BGP 还可以通过将 BGP 网关从一个 BGP 对等节点获知的路由传播到所有其他 BGP 对等节点来允许在多个网络之间传输路由。

有关 BGP 优点的更多讨论，以及若要了解使用 BGP 的技术要求和注意事项，请参阅 [Azure VPN 网关的 BGP 概述](vpn-gateway-bgp-overview.md)。

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>在 Azure VPN 网关上使用 BGP 入门

本文指导完成执行以下任务的步骤：

* [第 1 部分 - 在 Azure VPN 网关上启用 BGP](#enablebgp)
* 第 2 部分 - 使用 BGP 建立跨界连接
* [第 3 部分 - 使用 BGP 建立 VNet 到 VNet 连接](#v2vbgp)

说明的每一部分构成用于在网络连接中启用 BGP 的基本构建基块。 如果完成这所有三个部分，将生成拓扑，如下面的图中所示：

![BGP 拓扑](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

可以将这些部分组合在一起，生成更复杂的多跃点传输网络，以满足需求。

## <a name ="enablebgp"></a>第 1 部分 - 在 Azure VPN 网关上配置 BGP
以下配置步骤设置 Azure VPN 网关的 BGP 参数，如下面的图中所示：

![BGP 网关](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>开始之前
* 确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
* 安装 Azure 资源管理器 PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 

### <a name="step-1---create-and-configure-vnet1"></a>步骤 1 - 创建并配置 VNet1
#### <a name="1-declare-your-variables"></a>1.声明变量
对于本练习，我们首先要声明变量。 以下示例使用此练习中的值来声明变量。 请务必在配置生产环境时，使用自己的值来替换该值。 如果执行这些步骤是为了熟悉此类型的配置，则可以使用这些变量。 修改变量，然后将其复制并粘贴到 PowerShell 控制台中。

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
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
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2.连接到订阅并创建新资源组
若要使用资源管理器 cmdlet，请确保切换到 PowerShell 模式。 有关详细信息，请参阅[将 Windows PowerShell 与 Resource Manager 配合使用](../powershell-azure-resource-manager.md)。

打开 PowerShell 控制台并连接到帐户。 使用下面的示例来帮助连接：

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3.创建 TestVNet1
以下示例创建一个名为 TestVNet1 的虚拟网络和三个子网，这三个子网分别名为 GatewaySubnet、FrontEnd 和 Backend。 替换值时，请务必始终将网关子网特意命名为 GatewaySubnet。 如果命名为其他名称，网关创建会失败。

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>步骤 2 - 使用 BGP 参数为 TestVNet1 创建 VPN 网关
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1.创建 IP 和子网配置
请求一个公共 IP 地址，以分配给要为 VNet 创建的网关。 还将定义所需的子网和 IP 配置。

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2.使用 AS 编号创建 VPN 网关
为 TestVNet1 创建虚拟网络网关。 BGP 需要基于路由的 VPN 网关，还需要添加参数 -Asn 来为 TestVNet1 设置 ASN（AS 编号）。 如果未设置 ASN 参数，将分配 ASN 65515。 创建网关可能需要花费一段时间（30 分钟或更久）。

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3.获取 Azure BGP 对等节点 IP 地址
创建网关后，需要在 Azure VPN 网关上获取 BGP 对等节点 IP 地址。 需要此地址才能将 Azure VPN 网关配置为本地 VPN 设备的 BGP 对等节点。

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

最后一个命令会显示 Azure VPN 网关上的相应 BGP 配置，例如：

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

创建网关后，可以使用此网关通过 BGP 建立跨界连接或 VNet 到 VNet 连接。 以下各节介绍完成该练习所需的步骤。

## <a name ="crossprembbgp"></a>第 2 部分 - 使用 BGP 建立跨界连接

要建立跨界连接，需要创建本地网关来表示本地 VPN 设备，并创建连接将 VPN 网关与本地网关连接在一起。 尽管存在可引导完成这些步骤的文章，但本文包含用于指定 BGP 配置参数所需的其他属性。

![跨界的 BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

在继续下一步之前，请确保已完成本练习的[第 1 部分](#enablebgp)。

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>步骤 1 - 创建和配置本地网关

#### <a name="1-declare-your-variables"></a>1.声明变量

此练习将继续生成图中所示的配置。 请务必将值替换为用于配置的值。

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

关于本地网关参数，有几个事项需要注意：

* 本地网关可以与 VPN 网关在相同或不同的位置和资源组中。 此示例演示它们在不同位置的不同资源组中。
* 需要为本地网关声明的前缀是 VPN 设备上的 BGP 对等节点 IP 地址中的主机地址。 在此示例中，它是“10.52.255.254/32”中的 /32 前缀。
* 提醒一下，在本地网络与 Azure VNet 之间必须使用不同的 BGP ASN。 如果它们是相同的，则需要更改 VNet ASN（如果本地 VPN 设备已使用该 ASN 与其他 BGP 邻居对等）。

继续操作之前，请确保仍与订阅 1 保持连接。

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2.为 Site5 创建本地网关

在创建本地网关之前，请务必创建资源组（如果尚未创建）。 请注意本地网关的两个附加参数：Asn 和 BgpPeerAddress。

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>步骤 2 - 连接 VNet 网关和本地网关

#### <a name="1-get-the-two-gateways"></a>1.获取这两个网关

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2.创建 TestVNet1 到 Site5 的连接

在此步骤中，创建从 TestVNet1 到 Site5 的连接。 必须指定“-EnableBGP $True”，以便为此连接启用 BGP。 如前所述，同一 Azure VPN 网关可以同时具有 BGP 连接和非 BGP 连接。 除非在连接属性中启用了 BGP，否则 Azure 不会为此连接启用 BGP，即使已在这两个网关上配置了 BGP 参数，也是如此。

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

下面的示例列出了可在本地 VPN 设备上的 BGP 配置节中为此练习输入的参数：

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

连接在几分钟后建立，且 BGP 对等会话在建立 IPsec 连接后启动。

## <a name ="v2vbgp"></a>第 3 部分 - 使用 BGP 建立 VNet 到 VNet 连接

此部分使用 BGP 添加 VNet 到 VNet 连接，如下图所示：

![VNet 到 VNet 的 BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

以下说明延续前面的步骤。 必须完成[第 I 部分](#enablebgp)，以使用 BGP 创建和配置 TestVNet1 和 VPN 网关。 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>步骤 1 - 创建 TestVNet2 和 VPN 网关

必须确保新虚拟网络的 IP 地址空间 TestVNet2 不与任何 VNet 范围重叠。

在本示例中，虚拟网络属于同一订阅。 可在不同订阅之间设置 VNet 到 VNet 连接。 有关详细信息，请参阅[配置 VNet 到 VNet 连接](vpn-gateway-vnet-vnet-rm-ps.md)。 请确保在创建连接时添加“-EnableBgp $True”，以启用 BGP。

#### <a name="1-declare-your-variables"></a>1.声明变量

请务必将值替换为要用于配置的值。

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
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
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
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

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3.使用 BGP 参数为 TestVNet2 创建 VPN 网关

请求一个公共 IP 地址，以分配给要为 VNet 创建的网关，并定义所需的子网和 IP 配置。

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

使用 AS 编号创建 VPN 网关。 必须覆盖 Azure VPN 网关上的默认 ASN。 连接的 VNet 的 ASN 必须不同，才能启用 BGP 和传输路由。

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
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

完成这些步骤后，将在几分钟后建立连接。 完成 VNet 到 VNet 连接后，BGP 对等互连会话将运行。

如果已完成此练习的所有三个部分，则已建立以下网络拓扑：

![VNet 到 VNet 的 BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>后续步骤

连接完成后，即可将虚拟机添加到虚拟网络。 请参阅 [创建虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 以获取相关步骤。
