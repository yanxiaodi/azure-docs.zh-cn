---
title: 创建、更改或删除 Azure 虚拟网络对等互连 | Microsoft Docs
description: 了解如何创建、更改或删除虚拟网络对等互连。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: c77608aa7a4d1410277dfbe259fb1f55ea8324ae
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449383"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>创建、更改或删除虚拟网络对等互连

了解如何创建、更改或删除虚拟网络对等互连。 虚拟网络对等互连可以通过 Azure 主干网络连接同一区域或不同区域的虚拟网络（也称为全局 VNet 对等互连）。 对等互连后，这些虚拟网络仍将作为单独的资源进行管理。 如果不熟悉虚拟网络对等互连，可以通过阅读[虚拟网络对等互连概述](virtual-network-peering-overview.md)或完成[教程](tutorial-connect-virtual-networks-portal.md)来了解其详细信息。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用门户，请打开 https://portal.azure.com ，并使用具有[所需权限](#permissions)的帐户登录，以处理对等互连。
- 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需要使用具有[所需权限](#permissions)的帐户运行 `Connect-AzAccount` 来处理对等互连，以便与 Azure 建立连接。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需要使用具有[所需权限](#permissions)的帐户运行 `az login` 来处理对等互连，以便与 Azure 建立连接。

登录或连接到 Azure 所用的帐户必须分配有[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或者分配有可执行[权限](#permissions)中列出的适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="create-a-peering"></a>创建对等互连

创建对等互连之前，请熟悉要求和约束以及[所需权限](#permissions)。

1. 在 Azure 门户顶部的搜索框中，输入“虚拟网络”  。 当“虚拟网络”出现在搜索结果中时，请将其选中  。 如果“虚拟网络（经典）”出现在列表中，请不要选择它，因为无法从通过经典部署模型部署的虚拟网络创建对等互连。 
2. 从列表中选择要为其创建对等的虚拟网络。
3. 在“设置”  下，选择“对等”  。
4. 选择 **+ 添加**。 
5. <a name="add-peering"></a>为以下设置输入或选择值：
    - **名称：** 对等互连的名称在虚拟网络中必须唯一。
    - **虚拟网络部署模型：** 选择要对等互连的虚拟网络是通过哪种部署模型来进行部署的。
    - **我知道我的资源 ID：** 如果对要进行对等互连的虚拟网络拥有读取访问权限，请保留取消选中此复选框。 如果对要进行对等互连的虚拟网络或订阅没有读取访问权限，则选中此框。 在选中此框时显示的“资源 ID”复选框中输入要进行对等互连的虚拟网络的完整资源 ID。  输入的虚拟网络资源 ID 必须与此虚拟网络位于同一 Azure [区域](https://azure.microsoft.com/regions)，或[受支持的不同](#requirements-and-constraints) Azure 区域。 完整资源 ID 类似于 `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`。 可以通过查看虚拟网络的属性，获取虚拟网络的资源 ID。 若要了解如何查看虚拟网络的属性，请参阅[管理虚拟网络](manage-virtual-network.md#view-virtual-networks-and-settings)。 如果订阅关联的 Azure Active Directory 租户和你要进行对等互连的虚拟网络的订阅不同，则首先将每个租户中的一名用户添加为对方租户中的[来宾用户](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory)。
    - **订阅：** 选择要进行对等互连的虚拟网络的[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)。 将列出一个或多个订阅，具体取决于帐户对多少个订阅具有读取访问权限。 如果选中“资源 ID”复选框，则此设置不可用。 
    - **虚拟网络：** 选择要进行对等互连的虚拟网络。 可以选择通过任一 Azure 部署模型创建的虚拟网络。 如果要选择不同区域中的虚拟网络，必须选择[受支持区域](#cross-region)中的虚拟网络。 必须具有对虚拟网络的读取访问权限，才能使其出现在列表中。 如果列出了某个虚拟网络，但显示为灰色，则可能是因为虚拟网络的地址空间与此虚拟网络的地址空间重叠。 如果虚拟网络地址空间重叠，则它们无法进行对等互连。 如果选中“资源 ID”复选框，则此设置不可用。 
    - **允许虚拟网络访问：** 如果要启用两个虚拟网络之间的通信，请选择“启用”（默认）。  启用虚拟网络之间的通信可允许资源连接到任意虚拟网络，并以相同的带宽和延迟互相之间进行通信，就如同它们是连接到同一个虚拟网络一样。 这两个虚拟网络中的资源之间的所有通信都在 Azure 专用网络上进行。 网络安全组的服务标记 **VirtualNetwork** 中包含虚拟网络和已对等互连的虚拟网络。 若要深入了解网络安全组服务标记，请参阅[网络安全组概述](security-overview.md#service-tags)。 如果不希望流量流到已对等互连的虚拟网络，请选择“禁用”。  如果已将一个虚拟网络与另一个虚拟网络对等互连，但有时想要禁用这两个虚拟网络之间的流量流动，则可以选择“禁用”。  可发现启用/禁用比删除并重新创建对等互连更加方便。 当禁用此设置时，流量不会在已对等互连的虚拟网络间流动。
    - **允许转发的流量：** 选中此框将允许某个虚拟网络中通过网络虚拟设备转发的（不是从该虚拟网络发起的）流量通过对等互连流动到此虚拟网络。  例如，假设有名为 Spoke1、Spoke2 和 Hub 的三个虚拟网络。 每个辐射虚拟网络与中心虚拟网络之间存在一个对等互连，但各个辐射虚拟网络之间不存在对等互连。 一个网络虚拟设备部署在中心虚拟网络中，用户定义的路由应用于通过该网络虚拟设备在各个子网之间路由流量的每个辐射虚拟网络。 如果没有为每个辐射虚拟网络与中心虚拟网络之间的对等互连选中此复选框，则流量不会在各个辐射虚拟网络之间流动，因为中心i不在各个虚拟网络之间转发流量。 虽然启动此功能可允许通过对等互连转发流量，但它并不会创建任何用户定义的路由或网络虚拟设备。 用户自定义的路由和网络虚拟设备是单独创建的。 了解[用户定义的路由](virtual-networks-udr-overview.md#user-defined)。 如果流量通过 Azure VPN 网关在虚拟网络之间转发，则无需检查此设置。
    - **允许网关传输：** 如果有附加到此虚拟网络的虚拟网络网关并且想要允许来自已对等互连的虚拟网络的流量流经网关，请选中此框。 例如，此虚拟网络有可能通过虚拟网络网关附加到本地网络。 网关可以是一个 ExpressRoute 或 VPN 网关。 选中此框将允许来自所对等互连的虚拟网络的流量通过附加到此虚拟网络的网关流动到本地网络。 如果选中此框，则已对等连接的虚拟网络不能有已配置的网关。 设置从另一虚拟网络到此虚拟网络的对等互连时，对等互连的虚拟网络必须选中复选框“使用远程网关”。  如果保留此框的未选中状态（默认），则来自已对等互连的虚拟网络的流量仍可流动到此虚拟网络，但无法流经附加到此虚拟网络的虚拟网络网关。 如果在虚拟网络（资源管理器）和虚拟网络（经典）之间进行对等互连，则网关必须位于虚拟网络（资源管理器）中。

       除了将流量转发到本地网络之外，VPN 网关还可以在与该网关所在的虚拟网络对等互连的虚拟网络之间转发网络流量，各个虚拟网络不需要都彼此对等互连。 如果希望在中心（请参阅为**允许转发的流量**描述的中心和辐射示例）虚拟网络中使用 VPN 网关在未彼此对等互连的辐射虚拟网络之间路由流量，则使用 VPN 网关转发流量非常有用。 若要了解有关允许使用网关进行传输的详细信息，请参阅[配置 VPN 网关以在虚拟网络对等互连中传输](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 此方案要求实现用户定义的路由来将虚拟网络网关指定为下一跃点类型。 了解[用户定义的路由](virtual-networks-udr-overview.md#user-defined)。 只能将 VPN 网关指定为用户定义的路由中的下一跃点类型，不能将 ExpressRoute 网关指定为用户定义的路由中的下一跃点类型。

    - **使用远程网关：** 选中此框可允许来自此虚拟网络的流量流经附加到正与之对等互连的虚拟网络的虚拟网络网关。 例如，正与之对等互连的虚拟网络附加了一个 VPN 网关，可实现与本地网络的通信。  选中此框可允许来自此虚拟网络的流量流经附加到已对等互连的虚拟网络的 VPN 网关。 如果选中此框，已对等互连的虚拟网络必须附加有虚拟网络网关，并且必须已选中“允许网关传输”复选框。  如果保留此框的未选中状态（默认），则来自已对等互连的虚拟网络的流量仍将流动到此虚拟网络，但无法流经附加到此虚拟网络的虚拟网络网关。
    此虚拟网络只有一个对等互连可以启用此设置。

        如果已在虚拟网络中配置了网关，则无法使用远程网关。 若要了解有关使用网关进行传输的详细信息，请参阅[配置 VPN 网关以在虚拟网络对等互连中传输](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

6. 选择“确定”，将对等互连添加到所选的虚拟网络。 

有关在不同订阅和部署模型中的虚拟网络之间实现对等互连的分步说明，请参阅[后续步骤](#next-steps)。

### <a name="commands"></a>命令

- **Azure CLI**：[az network vnet peering create](/cli/azure/network/vnet/peering)
- PowerShell  ：[Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>查看或更改对等互连设置

更改对等互连之前，请熟悉要求和约束以及[所需权限](#permissions)。

1. 在门户顶部的搜索框中，输入“虚拟网络”  。 当“虚拟网络”出现在搜索结果中时，请将其选中  。 如果“虚拟网络（经典）”出现在列表中，请不要选择它，因为无法从通过经典部署模型部署的虚拟网络创建对等互连。 
2. 从列表中选择要为其更改对等设置的虚拟网络。
3. 在“设置”  下，选择“对等”  。
4. 选择要查看或更改其设置的对等互连。
5. 更改相应的设置。 若要了解每个设置的选项，请阅读“创建对等互连”部分的[步骤 5](#add-peering)。
6. 选择“保存”。 

**命令**

- **Azure CLI**：[az network vnet peering list](/cli/azure/network/vnet/peering) 可列出虚拟网络的对等，[az network vnet peering show](/cli/azure/network/vnet/peering) 可显示特定对等的设置，[az network vnet peering update](/cli/azure/network/vnet/peering) 可更改对等设置。|
- PowerShell  ：[Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) 可检索视图对等互连设置，[Set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) 可更改设置。

## <a name="delete-a-peering"></a>删除对等互连

在删除对等互连之前，请确保帐户具有[所需权限](#permissions)。

当对等互连删除后，来自虚拟网络的流量将不再流动到已对等互连的虚拟网络中。 将通过资源管理器部署的虚拟网络对等互连后，每个虚拟网络都具与另一个虚拟网络的对等互连。 虽然从一个虚拟网络删除对等互连会禁用虚拟网络之间的通信，但这不会删除另一个虚拟网络的对等互连。 存在于另一个虚拟网络中的对等互连的对等互连状态为“已断开连接”。  必须在第一个虚拟网络中重新创建对等互连，并且这两个虚拟网络的对等互连状态均更改为“已连接”后，才能重新创建对等互连。 

如果希望虚拟网络偶尔进行通信，而非始终通信，与其删除对等互连，更好的方法是改为将“允许虚拟网络访问”设置设为“禁用”。   若要了解如何操作，请阅读本文“创建对等互连”部分的步骤 6。 可发现禁用和启用网络访问比删除并重新创建对等互连更加容易。

1. 在门户顶部的搜索框中，输入“虚拟网络”  。 当“虚拟网络”出现在搜索结果中时，请将其选中  。 如果“虚拟网络（经典）”出现在列表中，请不要选择它，因为无法从通过经典部署模型部署的虚拟网络创建对等互连。 
2. 从列表中选择要为其删除对等的虚拟网络。
3. 在“设置”  下，选择“对等”  。
4. 在要删除的对等右侧，依次选择“...”、“删除”和“是”，从第一个虚拟网络删除对等。   
5. 完成先前的步骤，以从对等互连中的另一个虚拟网络中删除对等互连。

**命令**

- **Azure CLI**：[az network vnet peering delete](/cli/azure/network/vnet/peering)
- PowerShell  ：[Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>要求和约束

- <a name="cross-region"></a>可在相同区域或不同区域中的虚拟网络之间建立对等互连。 不同区域中的对等互连虚拟网络也称为“全局 VNet 对等互连”  。 
- 在创建全局对等互连时，对等互连的虚拟网络可以存在于任何 Azure 公有云区域或中国云区域或政府版云区域。 不能跨云对等互连。 例如，Azure 公有云中的 VNet 不能与 Azure 中国云中的 VNet 对等互连。
- 一个虚拟网络中的资源无法与全局对等互连虚拟网络中基本内部负载均衡器的前端 IP 地址通信。 对基本负载均衡器的支持仅存在于同一区域内。 VNet 对等互连和全局 VNet 对等互连都支持标准负载均衡器。 [此处](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)记录了使用基本负载均衡器但不能通过全局 VNet 对等互连正常工作的服务。
- 可以使用远程网关或允许在全局对等互连的虚拟网络和本地对等互连的虚拟网络中的网关传输。
- 虚拟网络可以位于相同或不同的订阅中。 如果对等虚拟网络位于不同的订阅中，两个订阅可关联到同一个或不同的 Azure Active Directory 租户。 如果还没有 AD 租户，可以[创建一个](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant)。 门户中不支持通过与不同 Azure Active Directory 租户相关联的订阅跨虚拟网络进行对等互连。 可使用 CLI、PowerShell 或模板。
- 进行对等互连的虚拟网络的 IP 地址空间不得重叠。
- 虚拟网络与另一个虚拟网络对等后，不能向其添加或从中删除地址范围。 若要添加或删除地址范围，请删除对等，添加或删除地址范围，然后重新创建对等。 若要为虚拟网络添加或删除地址范围，请参阅[管理虚拟网络](manage-virtual-network.md)。
- 可以对等互连两个通过资源管理器部署的虚拟网络，或对等互连一个通过资源管理器部署的虚拟网络与一个通过经典部署模型部署的虚拟网络。 不能对等互连两个通过经典部署模型创建的虚拟网络。 如果不熟悉 Azure 部署模型，请阅读[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。 可以使用 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V)来连接两个通过经典部署模型创建的虚拟网络。
- 对等互连两个通过资源管理器创建的虚拟网络时，必须为对等互连中的每个虚拟网络都配置对等互连。 将看到以下类型的对等互连状态之一： 
  - *已启动：* 当从第一个虚拟网络将对等互连创建至第二个虚拟网络时，对等互连状态为“已启动”  。 
  - *已连接：* 当从第二个虚拟网络将对等互连创建至第一个虚拟网络时，对等互连状态为“已连接”  。 如果查看第一个虚拟网络的对等互连状态，将看到其状态从“已启动”  更改为“已连接”  。 直到两个虚拟网络对等互连的对等互连状态均为“已连接”  时，对等互连才成功建立。
- 当对等互连一个通过资源管理器创建的虚拟网络与一个通过经典部署模型创建的虚拟网络时，只需为通过资源管理器部署的虚拟网络配置对等互连。 不能为虚拟网络（经典）配置对等互连，或在两个通过经典部署模型部署的虚拟网络之间配置对等互连。 在从虚拟网络（资源管理器）将对等互连创建至虚拟网络（经典）时，对等互连状态为“正在更新”  ，随后将更改为“已连接”  。
- 对等互连在两个虚拟网络之间创建。 对等互连是不可传递的。 如果在以下虚拟网络之间创建对等互连：
  - VirtualNetwork1 和 VirtualNetwork2
  - VirtualNetwork2 和 VirtualNetwork3

  不会通过 VirtualNetwork2 在 VirtualNetwork1 和 VirtualNetwork3 之间形成对等互连。 如果要在 VirtualNetwork1 和 VirtualNetwork3 之间创建虚拟网络对等互连，必须在 VirtualNetwork1 和 VirtualNetwork3 之间创建对等互连。
- 无法使用默认 Azure 名称解析来解析已对等互连的虚拟网络中的名称。 若要解析其他虚拟网络中的名称，必须使用[适用于专用域的 Azure DNS](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，或使用自定义 DNS 服务器。 若要了解如何设置自己的 DNS 服务器，请参阅[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。
- 同一区域中对等互连虚拟网络中的资源可以互相之间以相同的带宽和延迟进行通信，就如同资源是位于同一个虚拟网络中一样。 但是，每种虚拟机大小都有其自己的最大网络带宽。 若要深入了解不同虚拟机大小的最大网络带宽，请参阅 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机大小。
- 一个虚拟网络可以对等互连到另一个虚拟网络，也可以通过 Azure 虚拟网络网关连接到另一个虚拟网络。 当虚拟网络同时通过对等互连和网关连接时，虚拟网络的流量会根据对等互连配置流动，而不是网关。
- 在成功配置虚拟网络对等互连之后，必须重新下载点到站点 VPN 客户端，以确保将新路由下载到客户端。
- 对于利用虚拟网络对等互连的入口和出口流量，有少许收费。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/virtual-network)。

## <a name="permissions"></a>权限

必须向以下角色分配用于进行虚拟网络对等互连的帐户：

- [网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)：对于通过资源管理器部署的虚拟网络。
- [经典网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)：对于通过经典部署模型部署的虚拟网络。

如果未将帐户分配给上述角色之一，则必须将其分配给分配有下表中的必要操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

| 操作                                                          | 名称 |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | 创建从虚拟网络 A 到虚拟网络 B 的对等互连时必需。虚拟网络 A 必须是虚拟网络（资源管理器）          |
| Microsoft.Network/virtualNetworks/peer/action                   | 创建从虚拟网络 B（资源管理器）到虚拟网络 A 的对等互连时必需                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | 创建从虚拟网络 B（经典）到虚拟网络 A 的对等互连时必需                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | 读取虚拟网络对等互连   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | 删除虚拟网络对等互连 |

## <a name="next-steps"></a>后续步骤

- 可在通过相同或不同订阅中的相同或不同部署模型创建的虚拟网络之间创建虚拟网络对等互连。 完成适用于以下方案之一的教程：

  |Azure 部署模型             | 订阅  |
  |---------                          |---------|
  |都是资源管理器模型              |[相同](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[不同](create-peering-different-subscriptions.md)|
  |一个是资源管理器模型，一个是经典模型  |[相同](create-peering-different-deployment-models.md)|
  |                                   |[不同](create-peering-different-deployment-models-subscriptions.md)|

- 了解如何创建[中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](template-samples.md)创建虚拟网络对等互连
- 为虚拟网络创建并应用 [Azure Policy](policy-samples.md)
