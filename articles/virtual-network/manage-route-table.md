---
title: 创建、更改或删除 Azure 路由表
titlesuffix: Azure Virtual Network
description: 了解如何创建、更改或删除路由表。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: a39d9f9c5a138ece5d40cc5afe1d1dcdd8e7a41a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65849803"
---
# <a name="create-change-or-delete-a-route-table"></a>创建、更改或删除路由表

Azure 自动在 Azure 子网、虚拟网络与本地网络之间路由流量。 若要更改 Azure 的任何默认路由，可以创建一个路由表。 如果不熟悉虚拟网络路由，可在[路由概述](virtual-networks-udr-overview.md)中或通过完成[教程](tutorial-create-route-table-portal.md)了解有关详细信息。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任何部分中的步骤之前，请完成以下任务：

* 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。<br>
* 如果使用门户，请打开 https://portal.azure.com ，并使用你的 Azure 帐户登录。<br>
* 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。<br>
* 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

帐户登录，或连接到 Azure 中使用，必须将分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或设置为[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)分配中列出的相应操作[权限](#permissions).

## <a name="create-a-route-table"></a>创建路由表

在每个 Azure 位置和订阅中可创建的路由表数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在门户左上角选择“+ 创建资源”  。
1. 依次选择“网络”、“路由表”   。
1. 输入路由表的**名称**，选择自己的**订阅**，创建新的**资源组**或选择现有的资源组，选择一个**位置**，然后选择“创建”。  如果打算将连接到本地网络通过 VPN 网关的虚拟网络中的子网路由表关联，则禁用**虚拟网络网关路由传播**，不在本地路由传播到子网中的网络接口。

### <a name="create-route-table---commands"></a>创建路由表的命令

* Azure CLI：[az network route-table create](/cli/azure/network/route-table/route)<br>
* PowerShell：[New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>查看路由表

在门户顶部的搜索框中，输入“路由表”  。 当“路由表”出现在搜索结果中时，请选择它。  随后将列出订阅中存在的路由表。

### <a name="view-route-table---commands"></a>查看路由表的命令

* Azure CLI：[az network route-table list](/cli/azure/network/route-table/route)<br>
* PowerShell：[Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>查看路由表详细信息

1. 在门户顶部的搜索框中，输入“路由表”  。 当“路由表”出现在搜索结果中时，请选择它。 
1. 在列表中选择要查看其详细信息的路由表。 在“设置”  下，可以查看路由表中的“路由”  ，以及与该路由表关联的“子网”  。
1. 若要详细了解常见的 Azure 设置，请参阅以下信息：

    * [活动日志](../azure-monitor/platform/activity-logs-overview.md)<br>
    * [访问控制 (IAM)](../role-based-access-control/overview.md)<br>
    * [标记](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [锁](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [自动化脚本](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

### <a name="view-details-of-route-table---commands"></a>查看路由表的命令的详细信息

* Azure CLI：[az network route-table show](/cli/azure/network/route-table/route)<br>
* PowerShell：[Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>更改路由表

1. 在门户顶部的搜索框中，输入“路由表”  。 当“路由表”出现在搜索结果中时，请选择它。 
1. 选择要更改的路由表。 最常见的更改包括[添加](#create-a-route)或[删除](#delete-a-route)路由，以及在子网中[关联](#associate-a-route-table-to-a-subnet)路由表或[取消关联](#dissociate-a-route-table-from-a-subnet)路由表。

### <a name="change-a-route-table---commands"></a>更改路由表的命令

* Azure CLI：[az network route-table update](/cli/azure/network/route-table/route)<br>
* PowerShell：[Set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>将路由表关联到子网

子网可以没有或有一个与其相关联的路由表。 一个路由表可与零个或多个子网相关联。 由于路由表不会关联到虚拟网络，因此，必须将路由表关联到每个相关子网。 离开子网的所有流量将都路由基于路由表中创建的路由的路由[默认路由](virtual-networks-udr-overview.md#default)，并从本地网络传播的路由，如果虚拟网络连接到一个 Azure 虚拟网络网关 （ExpressRoute 或 VPN）。 只能将路由表关联到该路由表所在的同一 Azure 位置和订阅中的虚拟网络内的子网。

1. 在门户顶部的搜索框中，输入“虚拟网络”  。 当“虚拟网络”出现在搜索结果中时，请将其选中  。
1. 在列表中选择要将路由表关联到的子网所在的虚拟网络。
1. 在“设置”下选择“子网”。  
1. 选择要将路由表关联到的子网。
1. 依次选择“路由表”、要关联到子网的路由表、“保存”。  

如果虚拟网络已连接到 Azure VPN 网关，请不要将路由表与包含目标为 0.0.0.0/0 的路由的[网关子网](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)相关联。 这样做可能会阻止网关正常工作。 有关在路由中使用 0.0.0.0/0 的详细信息，请参阅[虚拟网络流量路由](virtual-networks-udr-overview.md#default-route)。

### <a name="associate-a-route-table---commands"></a>将路由表的命令关联

* Azure CLI：[az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell：[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>从子网取消关联路由表

从子网取消关联路由表后，Azure 会根据流量的[默认路由](virtual-networks-udr-overview.md#default)来路由流量。

1. 在门户顶部的搜索框中，输入“虚拟网络”  。 当“虚拟网络”出现在搜索结果中时，请将其选中  。
1. 在列表中选择要从中取消关联路由表的子网所在的虚拟网络。
1. 在“设置”下选择“子网”。  
1. 选择要从中取消关联路由表的子网。
1. 依次选择“路由表”、“无”、“保存”。   

### <a name="dissociate-a-route-table---commands"></a>取消关联路由表的命令

* Azure CLI：[az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell：[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>删除路由表

如果路由表已关联到任何子网，则无法删除它。 在尝试删除路由表之前，请从所有子网[取消关联](#dissociate-a-route-table-from-a-subnet)该路由表。

1. 在门户顶部的搜索框中，输入“路由表”  。 当“路由表”出现在搜索结果中时，请选择它。 
1. 选择要删除的路由表右侧的“...”。 
1. 依次选择“删除”、“是”。  

### <a name="delete-a-route-table---commands"></a>删除路由表的命令

* Azure CLI：[az network route-table delete](/cli/azure/network/route-table/route)<br>
* PowerShell：[Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>创建路由

在每个 Azure 位置和订阅中，可为每个路由表创建的路由数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在门户顶部的搜索框中，输入“路由表”  。 当“路由表”出现在搜索结果中时，请选择它。 
1. 在列表中选择要将路由添加到的路由表。
1. 在“设置”下选择“路由”。  
1. 选择 **+ 添加**。
1. 输入该路由在路由表中的唯一**名称**。
1. 以 CIDR 表示法输入要将流量路由到的**地址前缀**。 该前缀不能在路由表的多个路由中重复，不过，可以包含在另一个前缀中。 例如，如果在一个路由中定义了 10.0.0.0/16 作为前缀，仍可使用 10.0.0.0/24 地址前缀定义另一个路由。 Azure 根据最长的前缀匹配项选择流量的路由。 若要详细了解 Azure 如何选择路由，请参阅[路由概述](virtual-networks-udr-overview.md#how-azure-selects-a-route)。
1. 选择“下一跃点类型”。  有关所有下一跃点类型的详细说明，请参阅[路由概述](virtual-networks-udr-overview.md)。
1. 输入“下一跃点地址”的 IP 地址。  如果为“下一跃点类型”选择了“虚拟设备”，则只能输入一个地址。  
1. 选择“确定”  。

### <a name="create-a-route---commands"></a>创建的路由-命令

* Azure CLI：[az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell：[New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>查看路由

一个路由表包含零个或多个路由。 若要详细了解查看路由时所列的信息，请参阅[路由概述](virtual-networks-udr-overview.md)。

1. 在门户顶部的搜索框中，输入“路由表”  。 当“路由表”出现在搜索结果中时，请选择它。 
1. 在列表中选择要查看其路由的路由表。
1. 在“设置”下选择“路由”。  

### <a name="view-routes---commands"></a>查看路由的命令

* Azure CLI：[az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell：[Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>查看路由详细信息

1. 在门户顶部的搜索框中，输入“路由表”  。 当“路由表”出现在搜索结果中时，请选择它。 
1. 在列表中选择要查看其路由详细信息的路由表。
1. 选择“路由”。 
1. 选择要查看其详细信息的路由。

### <a name="view-details-of-a-route---commands"></a>查看路由的命令的详细信息

* Azure CLI：[az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell：[Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>更改路由

1. 在门户顶部的搜索框中，输入“路由表”  。 当“路由表”出现在搜索结果中时，请选择它。 
1. 选择要更改其路由的路由表。
1. 选择“路由”。 
1. 选择要更改的路由。
1. 将现有设置更改为新设置，然后选择“保存”。 

### <a name="change-a-route---commands"></a>更改路由的命令

* Azure CLI：[az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell：[Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>删除路由

1. 在门户顶部的搜索框中，输入“路由表”  。 当“路由表”出现在搜索结果中时，请选择它。 
1. 选择要删除其路由的路由表。
1. 选择“路由”。 
1. 在路由列表中，选择要删除的路由右侧的“...”。 
1. 依次选择“删除”、“是”。  

### <a name="delete-a-route---commands"></a>删除路由的命令

* Azure CLI：[az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell：[Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>查看有效路由

附加到虚拟机的每个网络接口的有效路由是创建的路由表、Azure 的默认路由，以及通过 BGP 和 Azure 虚拟网络网关从本地网络传播的任何路由的组合。 排查路由问题时，了解网络接口的有效路由非常有用。 可以查看已附加到运行中虚拟机的任何网络接口的有效路由。

1. 在门户顶部的搜索框中，输入要查看其有效路由的虚拟机的名称。 如果不知道虚拟机的名称，请在搜索框中输入“虚拟机”。  当“虚拟机”出现在搜索结果中时，请选择它，并从列表中选择一个虚拟机。 
1. 在“设置”下选择“网络”   。
1. 选择网络接口的名称。
1. 在“支持 + 故障排除”下，选择“有效路由”。  
1. 查看有效路由的列表，以确定是否存在可用于路由流量的正确路由。 [路由概述](virtual-networks-udr-overview.md)中详细介绍了此列表中显示的下一跃点类型。

### <a name="view-effective-routes---commands"></a>查看有效路由的命令

* Azure CLI：[az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell：[Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>验证两个终结点之间的路由

可以确定虚拟机与另一 Azure 资源的 IP 地址、本地资源或 Internet 上某个资源之间的下一跃点类型。 排查路由问题时，确定 Azure 的路由很有帮助。 若要完成此任务，必须使用现有的网络观察程序。 如果没有网络观察程序，可以完成[创建网络观察程序实例](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步骤来创建一个。

1. 在门户顶部的搜索框中，输入“网络观察程序”  。 搜索结果中出现“网络观察程序”后，将其选中  。
1. 在“网络诊断工具”下选择“下一跃点”。  
1. 选择要从其验证路由的源虚拟机的**订阅**和**资源组**。
1. 依次选择“虚拟机”、附加到虚拟机的**网络接口**，以及分配给要从其验证路由的网络接口的**源 IP 地址**。 
1. 输入要验证的路由**目标 IP 地址**。
1. 选择“下一跃点”。 
1. 片刻之后，系统会返回信息，告知路由了流量的路由的下一跃点类型和 ID。 [路由概述](virtual-networks-udr-overview.md)中详细介绍了返回的下一跃点类型。

### <a name="validate-routing-between-two-endpoints---commands"></a>验证两个终结点的命令之间的路由

* Azure CLI：[az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell：[Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>权限

若要针对路由表和路由执行任务，必须将你的帐户分配到[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表中所列适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

| 操作                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   读取路由表                                    |
| Microsoft.Network/routeTables/write                             |   创建或更新路由表                        |
| Microsoft.Network/routeTables/delete                            |   删除路由表                                  |
| Microsoft.Network/routeTables/join/action                       |   将路由表关联到子网                   |
| Microsoft.Network/routeTables/routes/read                       |   读取路由                                          |
| Microsoft.Network/routeTables/routes/write                      |   创建或更新路由                              |
| Microsoft.Network/routeTables/routes/delete                     |   删除路由                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   为网络接口获取有效路由表 |
| Microsoft.Network/networkWatchers/nextHop/action                |   从 VM 获取下一跃点                           |

## <a name="next-steps"></a>后续步骤

* 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](template-samples.md)创建路由表<br>
* 为虚拟网络创建并应用 [Azure Policy](policy-samples.md)
