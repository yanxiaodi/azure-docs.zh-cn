---
title: 将 Azure VPN 网关连接到多个基于策略的本地 VPN 设备：Azure 资源管理器：PowerShell | Microsoft Docs
description: 使用 Azure 资源管理器和 PowerShell 将基于路由的 Azure VPN 网关配置到多个基于策略的 VPN 设备。
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: yushwang
ms.openlocfilehash: 9085d5ee21b1e955b7d9416a379ee730ba26ad3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150159"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>使用 PowerShell 将 Azure VPN 网关连接到多个基于策略的本地 VPN 设备

本文帮助了解如何利用 S2S VPN 连接的 IPsec/IKE 策略将基于路由的 Azure VPN 网关配置为连接到多个基于策略的本地 VPN 设备。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>关于基于策略的 VPN 网关和基于路由的 VPN 网关

基于策略的 VPN 设备与基于路由的 VPN 设备在对连接设置 IPsec 流量选择器的方式上有所不同  ：

* 基于策略的 VPN 设备结合使用两个网络的前缀组合来定义流量通过 IPsec 隧道加密/解密的方式  。 它通常基于执行包筛选的防火墙设备。 包筛选和处理引擎添加了 IPsec 隧道加密和解密。
* 基于路由的 VPN 设备使用任意到任意（通配符）流量选择器，并让路由/转发表将流量直接导向不同 IPsec 隧道  。 它通常基于路由器平台，在此平台中，每个 IPsec 隧道建模为网络接口或 VTI（虚拟隧道接口）。

下图突出显示了以下两种模型：

### <a name="policy-based-vpn-example"></a>基于策略的 VPN 示例
![基于策略](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>基于路由的 VPN 示例
![基于路由](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure 对基于策略的 VPN 的支持情况
目前，Azure 支持两种 VPN 网关模式：基于路由的 VPN 网关和基于策略的 VPN 网关。 两者基于不同的内部平台，因而规格也不同：

|                          | 基于策略的 VPN 网关  | 基于路由的 VPN 网关                |
| ---                      | ---                         | ---                                      |
| Azure 网关 SKU     | 基本                       | 基本、标准、高性能、VpnGw1、VpnGw2、VpnGw3 |
| IKE 版本           | IKEv1                       | IKEv2                                    |
| **最大S2S 连接** | **1**                       | 基本/标准：10<br> 高性能：30 |
|                          |                             |                                          |

使用自定义 IPsec/IKE 策略，现在可以将基于路由的 Azure VPN 网关配置为使用带“PolicyBasedTrafficSelectors”选项的基于前缀的流量选择器，从而连接到基于策略的本地 VPN 设备  。 此功能允许从 Azure 虚拟网络和 VPN 网关连接到多个基于策略的本地 VPN/防火墙设备，从当前基于 Azure Policy 的 VPN 网关中删除单个连接限制。

> [!IMPORTANT]
> 1. 若要启用此连接，基于策略的本地 VPN 设备必须支持 **IKEv2**，才能连接到基于路由的 Azure VPN 网关。 请查看 VPN 设备规格。
> 2. 通过基于策略的 VPN 设备采用此机制进行连接的本地网络只能连接到 Azure 虚拟网络；不能经由相同的 Azure VPN 网关传输到其他本地网络或虚拟网络  。
> 3. 配置选项是自定义 IPsec/IKE 连接策略的一部分。 如果启用基于策略的流量选择器选项，则必须指定完整的策略（IPsec/IKE 加密和完整性算法、密钥强度和 SA 生存期）。

下图显示了在选择基于策略的 VPN 时，经由 Azure VPN 网关的传输路由为何无法工作：

![基于策略的传输](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

如图所示，针对每个本地网络前缀，Azure VPN 网关都有来自虚拟网络的流量选择器，而交叉连接前缀却没有。 例如，本地站点 2、站点 3 和站点 4 可以分别与 VNet1 通信，但不能经由 Azure VPN 网关相互连接。 该图显示若采用此配置，交叉连接流量选择器在 Azure VPN 网关中不可用。

## <a name="configurepolicybased"></a>对连接配置基于策略的流量选择器

本文中的说明采用[为 S2S 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略](vpn-gateway-ipsecikepolicy-rm-powershell.md)中所述的示例，建立 S2S VPN 连接。 下图显示了此特点：

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

启用此连接的工作流：
1. 为跨站点连接创建虚拟网络、VPN 网关和本地网关
2. 创建 IPsec/IKE 策略
3. 创建 S2S 或 VNet 到 VNet 的连接时，应用该策略，并对连接启用基于策略的流量选择器 
4. 如已创建连接，可对现有连接应用或更新策略

## <a name="before-you-begin"></a>开始之前

确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>对连接启用基于策略的流量选择器

确保已通读了本节的[第 3 部分 - 配置 IPsec/IKE 策略](vpn-gateway-ipsecikepolicy-rm-powershell.md)一文。 以下示例采用相同的参数和步骤：

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>步骤 1 - 创建虚拟网络、VPN 网关和本地网关

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1.连接到订阅并声明变量

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

声明变量。 在本练习中，我们使用以下变量：

```azurepowershell-interactive
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2.创建虚拟网络、VPN 网关和本地网关

创建资源组。

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

使用以下示例创建具有三个子网的虚拟网络 TestVNet1 和 VPN 网关。 如果想替换值，务必始终将网关子网特意命名为“GatewaySubnet”。 如果命名为其他名称，网关创建会失败。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>步骤 2 - 创建含 IPsec/IKE 策略的 S2S VPN 连接

#### <a name="1-create-an-ipsecike-policy"></a>1.创建 IPsec/IKE 策略

> [!IMPORTANT]
> 需创建 IPsec/IKE 策略，才能对连接启用“UsePolicyBasedTrafficSelectors”选项。

下面的示例使用以下算法和参数创建 IPsec/IKE 策略：
* IKEv2：AES256、SHA384、DHGroup24
* IPsec：AES256、SHA256、PFS 无、SA 生存期 14400 秒和 102400000KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2.创建采用基于策略的流量选择器和 IPsec/IKE 策略的 S2S VPN 连接
创建 S2S VPN 连接并应用上一步创建的 IPsec/IKE 策略。 请注意其他参数“-UsePolicyBasedTrafficSelectors $True”可对连接启用基于策略的流量选择器。

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

完成这些步骤后，S2S VPN 连接将使用定义的 IPsec/IKE 策略，并对连接启用基于策略的流量选择器。 可重复这些步骤，从同一 Azure VPN 网关添加更多连接到其他基于策略的本地 VPN 设备。

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>对连接更新基于策略的流量选择器
最后一节将介绍如何对现有 S2S VPN 连接更新基于策略的流量选择器选项。

### <a name="1-get-the-connection"></a>1.获取连接
获取连接资源。

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2.检查基于策略的流量选择器选项
以下行显示连接是否使用了基于策略的流量选择器：

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

如果行返回“True”，则表示对连接配置了基于策略的流量选择器；否则返回“False”   。

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3.对连接启用/禁用基于策略的流量选择器
获取连接资源后，可启用或禁用该选项。

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>启用 UsePolicyBasedTrafficSelectors
下面的示例启用了基于策略的流量选择器选项，但未改变 IPsec/IKE 策略：

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>禁用 UsePolicyBasedTrafficSelectors
下面的示例禁用了基于策略的流量选择器选项，但未改变 IPsec/IKE 策略：

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>后续步骤
连接完成后，即可将虚拟机添加到虚拟网络。 请参阅[创建虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)以获取相关步骤。

有关自定义 IPsec/IKE 策略的详细信息，请参阅[为 S2S VPN 或 VNet 到 VNet 的连接配置 IPsec/IKE 策略](vpn-gateway-ipsecikepolicy-rm-powershell.md)。
