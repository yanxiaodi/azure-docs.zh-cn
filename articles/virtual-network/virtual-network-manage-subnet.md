---
title: 添加、更改或删除 Azure 虚拟网络子网
titlesuffix: Azure Virtual Network
description: 了解如何在 Azure 中添加、更改或删除虚拟网络子网。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: 4af8c6dbbcbb877351018eb881f4d3b0b447bbce
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098976"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>添加、更改或删除虚拟网络子网

了解如何添加、更改或删除虚拟网络子网。 部署到虚拟网络的所有 Azure 资源都将部署到虚拟网络内的子网中。 如果不熟悉虚拟网络，可在[虚拟网络概述](virtual-networks-overview.md)中或通过完成[教程](quick-create-portal.md)了解相关详细信息。 若要创建、更改或删除虚拟网络，请参阅[管理虚拟网络](manage-virtual-network.md)。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用门户，请打开 https://portal.azure.com ，并使用 Azure 帐户登录。
- 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

登录或连接到 Azure 所用的帐户必须分配有[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或者分配有可执行[权限](#permissions)中列出的适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="add-a-subnet"></a>添加子网

1. 在门户顶部的搜索框中，输入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请将其选中。
2. 从虚拟网络列表中，选择要将子网添加到的虚拟网络。
3. 在“设置”下选择“子网”。
4. 选择“+子网”。
5. 输入以下参数的值：
   - **名称**：该名称必须在虚拟网络中唯一。 为了最大程度地兼容其他 Azure 服务，我们建议使用字母作为名称的第一个字符。 例如，Azure 应用程序网关不会部署到名称以数字开头的子网中。
   - **地址范围**：此范围在该虚拟网络的地址空间内必须唯一。 其范围不能与虚拟网络中其他子网地址范围重叠。 必须使用无类别域际路由选择 (CIDR) 表示法指定地址空间。 例如，在地址空间为 10.0.0.0/16 的虚拟网络中，可将子网地址空间定义为 10.0.0.0/24。 可以指定的最小范围为 /29，为子网提供八个 IP 地址。 Azure 保留每个子网中的第一个地址和最后一个地址，以确保协议一致性。 此外还会保留三个地址供 Azure 服务使用。 因此，使用 /29 地址范围定义子网时，子网中会有三个可用 IP 地址。 如果你打算将虚拟网络连接到 VPN 网关，则必须创建网关子网。 详细了解[网关子网地址范围具体考虑事项](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)。 在特定条件下，可在添加子网后更改地址范围。 若要了解有关如何更改子网地址范围的相关信息，请参阅[更改子网设置](#change-subnet-settings)。
   - **网络安全组**：可将零个或一个现有的网络安全组关联到子网，以筛选子网的入站和出站网络流量。 网络安全组必须存在于与虚拟网络相同的订阅和位置。 若要详细了解[网络安全组](security-overview.md)，请参阅[如何创建网络安全组](tutorial-filter-network-traffic.md)。
   - **路由表**：可以选择将现有的路由表关联到子网，控制目标为其他网络的网络流量路由。 路由表必须存在于与虚拟网络相同的订阅和位置。 详细了解 [Azure 路由](virtual-networks-udr-overview.md)和[如何创建路由表](tutorial-create-route-table-portal.md)
   - **服务终结点：** 子网可以有零个或多个为其启用的服务终结点。 若要启用的服务的服务终结点，选择的服务或服务，想要启用服务终结点从**服务**列表。 系统会自动为终结点配置位置。 默认情况下，系统会为虚拟网络所在的区域配置服务终结点。 对于 Azure 存储，为了支持区域故障转移方案，系统会将终结点自动配置到 [Azure 配对区域](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)。
   - **子网委派**：可以为子网启用零到多个委派。 子网委派为服务提供了显式权限，以便能够在部署服务时使用唯一标识符在子网中创建服务专属资源。 若要向服务委派，请从“服务”列表中选择要委派给的服务。

       若要删除的服务终结点，请取消选择你想要删除的服务终结点的服务。 如需详细了解服务终结点以及可为其启用的服务，请参阅[虚拟网络服务终结点概述](virtual-network-service-endpoints-overview.md)。 一旦启用服务的服务终结点，还必须启用与服务创建的资源的子网的网络访问权限。 例如，如果启用的服务终结点*Microsoft.Storage*，还必须启用到你想要授予对网络访问权限的所有 Azure 存储帐户的网络访问权限。 有关如何启用到为启用服务终结点的子网的网络访问的详细信息，请参阅各个启用的服务终结点的服务的文档。

     要验证是否为某个子网启用了服务终结点，请查看[有效路由](diagnose-network-routing-problem.md)，获取该子网中的任何网络接口。 如果配置了终结点，将会看到服务地址前缀和 nextHopType 为“VirtualNetworkServiceEndpoint”的“默认”路由。 若要了解有关路由的详细信息，请参阅[路由概述](virtual-networks-udr-overview.md)。
6. 单击“确定”，将子网添加到所选的虚拟网络。

**命令**

- Azure CLI：[az network vnet subnet create](/cli/azure/network/vnet/subnet)
- PowerShell：[Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>更改子网设置

1. 在门户顶部的搜索框中，输入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请将其选中。
2. 从虚拟网络列表中，选择要为其更改对等设置的虚拟网络。
3. 在“设置”下选择“子网”。
4. 在子网的列表中，选择想要更改设置的子网。 可以更改以下设置：

    - **地址范围：** 如果没有资源部署在子网内，可以更改的地址范围。 如果子网中存在的任何资源，必须将资源移到另一个子网，或从子网中先删除它们。 删除资源所采取的步骤因资源而异。 若要了解如何删除子网中的资源，请阅读针对要删除的每种资源类型的相关文档。 请参阅[添加子网](#add-a-subnet)步骤 5 中的**地址范围**约束。
    - **用户**：可使用内置角色或自己的自定义角色控制对子网的访问。 若要了解有关分配访问子网的角色和用户的详细信息，请参阅[使用角色分配管理对 Azure 资源的访问权限](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment)。
    - **网络安全组**和**路由表**：请参阅[添加子网](#add-a-subnet)的步骤 5。
    - **服务终结点**：请参阅[添加子网](#add-a-subnet)的步骤 5 中的服务终结点。 如果为某个现有子网启用服务终结点，请确保该子网的任何资源上未运行任何关键任务。 服务终结点可将子网每个网络接口上的路由，从使用地址前缀为 0.0.0.0/0 且下一跃点类型为 Internet 的默认路由，切换到使用服务地址前缀且下一跃点类型为 VirtualNetworkServiceEndpoint 的新路由。 切换过程中，可能会终止任何打开的 TCP 连接。 对于使用新路由更新的所有网络接口，除非流量流向服务，否则不会启用服务终结点。 若要了解有关路由的详细信息，请参阅[路由概述](virtual-networks-udr-overview.md)。
    - **子网委派**：请参阅[添加子网](#add-a-subnet)的步骤 5 中的服务终结点。 可以将子网委派修改为，为子网启用零到多个委派。 如果已在子网中部署了服务的资源，则在删除该服务的所有资源之前，无法添加或删除子网委派。 若要向其他服务委派，请从“服务”列表中选择要委派给的服务。
5. 选择**保存**。

**命令**

- Azure CLI：[az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell：[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>删除子网

仅当子网中无任何资源时，才可删除该子网。 如果子网中存在资源，则必须先删除子网中的资源，然后才能删除该子网。 删除资源所采取的步骤因资源而异。 若要了解如何删除子网中的资源，请阅读针对要删除的每种资源类型的相关文档。

1. 在门户顶部的搜索框中，输入“虚拟网络”。 当“虚拟网络”出现在搜索结果中时，请将其选中。
2. 从虚拟网络列表中，选择要删除子网的虚拟网络。
3. 在“设置”下选择“子网”。
4. 在子网的列表中，选择要删除的子网右侧的“...”。
5. 依次选择“删除”、“是”。

**命令**

- Azure CLI：[az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell：[Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>权限

若要在子网中执行任务，必须将帐户分配到[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配到下表中所列出的适当操作的[自定义](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

|操作                                                                   |   姓名                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   读取虚拟网络子网              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   创建或更新虚拟网络子网  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   删除虚拟网络子网            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   加入虚拟网络                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   为子网启用服务终结点     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   获取子网中的虚拟机       |

## <a name="next-steps"></a>后续步骤

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](template-samples.md)创建虚拟网络和子网
- 为虚拟网络创建并应用 [Azure Policy](policy-samples.md)
