---
title: 筛选网络流量 - Azure PowerShell | Microsoft Docs
description: 本文介绍如何在 PowerShell 中使用网络安全组筛选发往子网的网络流量。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 08031bc2ac29ea77374e21c4ce6f7bcf6151bcad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730030"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>在 PowerShell 中使用网络安全组筛选网络流量

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以使用网络安全组来筛选虚拟网络子网的入站和出站网络流量。 网络安全组包含安全规则，这些规则可按 IP 地址、端口和协议筛选网络流量。 安全规则应用到子网中部署的资源。 在本文中，学习如何：

* 创建网络安全组和安全规则
* 创建虚拟网络并将网络安全组关联到子网
* 将虚拟机 (VM) 部署到子网中
* 测试流量筛选器

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 PowerShell，则本文需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="create-a-network-security-group"></a>创建网络安全组

网络安全组包含安全规则。 安全规则指定源和目标。 源和目标可以是应用程序安全组。

### <a name="create-application-security-groups"></a>创建应用程序安全组

首先使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 针对本文中创建的所有资源创建一个资源组。 以下示例在 *eastus* 位置创建一个资源组：

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

使用 [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) 创建应用程序安全组。 使用应用程序安全组可以分组具有类似端口筛选要求的服务器。 以下示例创建两个应用程序安全组。

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>创建安全规则

使用 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 创建安全规则。 以下示例创建一个规则，该规则允许通过端口 80 和 443 将来自 Internet 的入站流量发往 *myWebServers* 应用程序安全组：

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

在本文中，将在 Internet 上为 *myAsgMgmtServers* VM 公开 RDP（端口 3389）。 在生产环境中，我们建议使用 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或[专用](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)网络连接来连接到要管理的 Azure 资源，而不要向 Internet 公开端口 3389。

### <a name="create-a-network-security-group"></a>创建网络安全组

使用 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) 创建网络安全组。 以下示例创建名为 *myNsg* 的网络安全组：

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 创建虚拟网络。 以下示例创建名为 *myVirtualNetwork* 的虚拟网络：

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 创建子网配置，然后使用 [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) 将子网配置写入虚拟网络。 以下示例将名为 *mySubnet* 的子网添加到虚拟网络，并将 *myNsg* 网络安全组关联到该虚拟网络：

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>创建虚拟机

在创建 VM 之前，使用 [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) 检索包含子网的虚拟网络对象：

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 为每个 VM 创建一个公共 IP 地址：

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

使用 [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 创建两个网络接口，并将公共 IP 地址分配给网络接口。 以下示例创建一个网络接口，将 *myVmWeb* 公共 IP 地址关联到该网络接口，并使其成为 *myAsgWebServers* 应用程序安全组的成员：

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

以下示例创建一个网络接口，将 *myVmMgmt* 公共 IP 地址关联到该网络接口，并使其成为 *myAsgMgmtServers* 应用程序安全组的成员：

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

在虚拟网络中创建两个 VM，以便在后续步骤中可以验证流量筛选。

使用 [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) 创建 VM 配置，然后使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 创建 VM。 以下示例创建充当 Web 服务器的 VM。 `-AsJob` 选项会在后台创建 VM，因此可继续执行下一步：

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

创建充当管理服务器的 VM：

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

创建虚拟机需花费几分钟的时间。 请 Azure 创建完 VM 之前，请不要继续下一步。

## <a name="test-traffic-filters"></a>测试流量筛选器

使用 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 返回 VM 的公共 IP 地址。 以下示例返回 *myVmMgmt* VM 的公共 IP 地址：

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

从本地计算机使用以下命令创建与 *myVmMgmt* VM 的远程桌面会话。 将 `<publicIpAddress>` 替换为上一命令返回的 IP 地址。

```
mstsc /v:<publicIpAddress>
```

打开下载的 RDP 文件。 出现提示时，选择“连接”。

输入在创建 VM 时指定的用户名和密码（可能需要选择“更多选择”，然后选择“使用其他帐户”，以便指定在创建 VM 时输入的凭据），然后选择“确定”。 你可能会在登录过程中收到证书警告。 选择“是”以继续进行连接。

连接将会成功，因为允许通过端口 3389 将入站流量从 Internet 发往已附加到 *myVmMgmt* VM 的网络接口所在的 *myAsgMgmtServers* 应用程序安全组。

在 PowerShell 中使用以下命令，从 *myVmMgmt* VM 来与 *myVmWeb* VM 建立远程桌面连接：

``` 
mstsc /v:myvmWeb
```

连接将会成功，因为每个网络安全组中的默认安全规则允许通过虚拟网络中所有 IP 地址之间的所有端口发送流量。 无法从 Internet 来与 *myVmWeb* VM 建立远程桌面连接，因为 *myAsgWebServers* 的安全规则不允许通过端口 3389 发送来自 Internet 的入站流量。

在 PowerShell 中使用以下命令在 *myVmWeb* VM 上安装 Microsoft IIS：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

完成 IIS 安装后，从 *myVmWeb* VM 断开连接，从而保留 *myVmMgmt* 远程桌面连接。 若要查看 IIS 欢迎屏幕，请打开 Internet 浏览器并浏览到 http:\//myVmWeb。

从 *myVmMgmt* VM 断开连接。

在计算机上，在 PowerShell 中输入以下命令，以检索 *myVmWeb* 服务器的公共 IP 地址：

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

若要确认可以从 Azure 外部访问 *myVmWeb* Web 服务器，请在计算机上打开 Internet 浏览器并浏览到 `http://<public-ip-address-from-previous-step>`。 连接将会成功，因为允许通过端口 80 将入站流量从 Internet 发往已附加到 *myVmWeb* VM 的网络接口所在的 *myAsgWebServers* 应用程序安全组。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组及其包含的所有资源，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 将其删除：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

在本文中，我们已创建一个网络安全组并将其关联到虚拟网络子网。 若要详细了解网络安全组，请参阅[网络安全组概述](security-overview.md)和[管理网络安全组](manage-network-security-group.md)。

默认情况下，Azure 在子网之间路由流量。 你也可以选择通过某个 VM（例如，充当防火墙的 VM）在子网之间路由流量。 若要了解操作方法，请参阅[创建路由表](tutorial-create-route-table-powershell.md)。
