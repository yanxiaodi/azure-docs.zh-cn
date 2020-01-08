---
title: 创建、更改或删除 Azure 网络安全组
titlesuffix: Azure Virtual Network
description: 了解如何创建、更改或删除网络安全组。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: 1c00f23570c3f8d80e39f3fe3901f866e40dc2ea
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305678"
---
# <a name="create-change-or-delete-a-network-security-group"></a>创建、更改或删除网络安全组

通过网络安全组中的安全规则，可以筛选可流入和流出虚拟网络子网和网络接口的流量类型。 如果不熟悉网络安全组，请参阅[网络安全组概述](security-overview.md)了解有关详细信息，并完成[筛选流量](tutorial-filter-network-traffic.md)教程，获得有关网络安全组的一些经验。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用门户，请打开 https://portal.azure.com ，并使用 Azure 帐户登录。
- 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.28 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

必须将登录或连接到 Azure 所用的帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有“[权限](#permissions)”中所列适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="work-with-network-security-groups"></a>使用网络安全组

对于网络安全组可执行创建、[查看所有](#view-all-network-security-groups)、[查看详细信息](#view-details-of-a-network-security-group)[更改](#change-a-network-security-group)以及[删除](#delete-a-network-security-group)操作。 也可从网络接口或子网[关联或取消关联](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)网络安全组。

### <a name="create-a-network-security-group"></a>创建网络安全组

在每个 Azure 位置和订阅中可创建的网络安全组数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在门户左上角选择“+ 创建资源”  。
2. 依次选择“网络”、“网络安全组”   。
3. 输入网络资源组的“名称”，选择自己的“订阅”，创建新的“资源组”或选择现有的资源组，选择一个“位置”，然后选择“创建”      。

**命令**

- Azure CLI: [az network vnet create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell：[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>查看所有网络安全组

在门户顶部的搜索框中，输入“网络安全组”  。 “网络安全组”出现在搜索结果中时，将其选中  。 随后将列出订阅中存在的网络安全组。

**命令**

- Azure CLI: [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell：[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>查看网络安全组的详细信息

1. 在门户顶部的搜索框中，输入“网络安全组”  。 “网络安全组”出现在搜索结果中时，将其选中  。
2. 在列表中选择要查看其详细信息的网络安全组。 在“设置”下，可查看“入站安全规则”和“出站安全规则”以及与网络安全组相关联的“网络接口”和“子网”      。 也可启用或禁用“诊断日志”和查看“有效的安全规则”   。 要了解详细信息，请参阅[诊断日志](virtual-network-nsg-manage-log.md)和[查看有效的安全规则](diagnose-network-traffic-filter-problem.md)。
3. 要了解有关列出的常见 Azure 设置的详细信息，请参阅以下文章：
    *   [活动日志](../azure-monitor/platform/activity-logs-overview.md)
    *   [访问控制 (IAM)](../role-based-access-control/overview.md)
    *   [标记](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [锁](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [自动化脚本](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**命令**

- Azure CLI: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell：[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>更改网络安全组

1. 在门户顶部的搜索框中，输入“网络安全组”  。 “网络安全组”出现在搜索结果中时，将其选中  。
2. 选择要更改的网络安全组。 最常见的更改是[添加](#create-a-security-rule)或[删除](#delete-a-security-rule)安全规则以及[将网络安全组关联到子网或网络接口或从其中取消关联](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)。

**命令**

- Azure CLI: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell：[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>将网络安全组与子网或网络接口关联或取消关联

要将网络安全组关联到网络接口，或从网络接口取消关联网络安全组，请参阅[将网络安全组与网络接口关联或取消关联](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)。 要将网络安全组关联到子网，或从子网取消关联网络安全组，请参阅[更改子网设置](virtual-network-manage-subnet.md#change-subnet-settings)。

### <a name="delete-a-network-security-group"></a>删除网络安全组

如果网络安全组与任何子网或网络接口相关联，则无法删除。 从所有子网和网络接口取消关联网络安全组，然后再尝试将其删除。

1. 在门户顶部的搜索框中，输入“网络安全组”  。 “网络安全组”出现在搜索结果中时，将其选中  。
2. 从列表中选择要删除的网络安全组。
3. 依次选择“删除”、“是”。  

**命令**

- Azure CLI: [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell：[Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>使用安全规则

网络安全组包含零个或多个安全规则。 对于安全规则，可执行创建、[查看所有](#view-all-security-rules)、[查看详细信息](#view-details-of-a-security-rule)[更改](#change-a-security-rule)以及[删除](#delete-a-security-rule)操作。

### <a name="create-a-security-rule"></a>创建安全规则

在每个 Azure 位置和订阅的每个网络安全组可创建的规则数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

1. 在门户顶部的搜索框中，输入“网络安全组”  。 “网络安全组”出现在搜索结果中时，将其选中  。
2. 从列表中选择要添加安全规则的网络安全组。
3. 在“设置”下选择“入站安全规则”   。 列出了几个现有规则。 某些规则可能尚未添加。 创建网络安全组时，会在其中创建几个默认安全规则。 要了解详细信息，请参阅[默认安全规则](security-overview.md#default-security-rules)。  无法删除默认安全规则，但可以使用更高优先级的规则将其覆盖。
4. <a name = "security-rule-settings"></a>选择“+ 添加”  。  为以下设置选择或添加值，然后选择“确定”  ：
    
    |设置  |ReplTest1  |详细信息  |
    |---------|---------|---------|
    |Source     | 为入站安全规则选择“任何项”、“应用程序安全组”、“IP 地址”或“服务标记”     。 如果要创建出站安全规则，所用选项与为“目标”列出的选项相同  。       | 如果选择“应用程序安全组”，则选择一个或多个与网络接口存在于同一区域的现有的应用程序安全组  。 了解如何[创建应用程序安全组](#create-an-application-security-group)。 如果为“源”和“目标”都选择“应用程序安全组”，则两个应用程序安全组中的网络接口必须在同一虚拟网络中    。 如果选择“IP 地址”，请指定“源 IP 地址/CIDR 范围”   。 可指定单个值或以逗号分隔的多个值的列表。 多个值的示例为 10.0.0.0/16, 192.188.1.1。 可指定的值的数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 如果选择“服务标记”，请选择一个服务标记  。 服务标记是 IP 地址类别的预定义标识符。 若要了解有关可用服务标记以及每个标记表示的含义的详细信息，请参阅[服务标记](security-overview.md#service-tags)。 如果指定的 IP 地址已分配给 Azure 虚拟机，请确保指定的是专用 IP，而不是已分配给虚拟机的公共 IP 地址。 在 Azure 将公共 IP 地址转换为专用 IP 地址以符合入站安全规则后，在 Azure 将专用 IP 地址转换为公共 IP 地址以符合出站规则之前，会处理安全规则。 若要了解有关 Azure 中的公共和专用 IP 地址的详细信息，请参阅 [IP 地址类型](virtual-network-ip-addresses-overview-arm.md)。        |
    |Source port ranges     | 指定单个端口（如 80）、端口范围（如 1024-65535）或单个端口和/或端口范围的以逗号分隔的列表（如 80, 1024-65535）。 输入星号可允许任何端口上的流量。 | 端口和范围指定规则允许或拒绝哪个端口流量。 可指定的端口数目有限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。  |
    |目标     | 为出站安全规则选择**任何** **应用程序安全组**、 **IP 地址**或**虚拟网络**。 如果创建的是入站安全规则, 则选项与 "**源**" 列中列出的选项相同。        | 如果选择“应用程序安全组”，那么必须选择一个或多个与网络接口存在于同一区域的现有的应用程序安全组  。 了解如何[创建应用程序安全组](#create-an-application-security-group)。 如果选择“应用程序安全组”，则选择一个与网络接口存在于同一区域的现有的应用程序安全组  。 如果选择“IP 地址”，则指定“目标 IP 地址/CIDR 范围”   。 类似于“源”和“源 IP 地址/CIDR 范围”，你可指定单个或多个地址或范围，并且可指定的数目有限制   。 选择“虚拟网络”，它是一个服务标记，意味着流量可到虚拟网络地址空间内的所有 IP 地址  。 如果指定的 IP 地址已分配给 Azure 虚拟机，请确保指定的是专用 IP，而不是已分配给虚拟机的公共 IP 地址。 在 Azure 将公共 IP 地址转换为专用 IP 地址以符合入站安全规则后，在 Azure 将专用 IP 地址转换为公共 IP 地址以符合出站规则之前，会处理安全规则。 若要了解有关 Azure 中的公共和专用 IP 地址的详细信息，请参阅 [IP 地址类型](virtual-network-ip-addresses-overview-arm.md)。        |
    |目标端口范围     | 指定单个值或以逗号分隔的多个值的列表。 | 类似于“源端口范围”，可指定单个或多个端口和范围，并且可指定的数目有限制  。 |
    |Protocol     | 选择 "**任何**"、" **TCP**"、" **UDP** " 或 " **ICMP**"。        |         |
    |Action     | 选择“允许”或“拒绝”   。        |         |
    |Priority     | 输入一个介于 100-4096 之间的值，该值对于网络安全组内的所有安全规则都是唯一的。 |规则按优先顺序处理。 编号越低，优先级越高。 建议创建规则时在优先级数字之间留出空隙，例如 100, 200, 300。 留出空隙后，未来在需要使规则高于或低于现有规则时，可更轻松添加规则。         |
    |名称     | 网络安全组内规则的唯一名称。        |  名称最多可包含 80 个字符。 它必须以字母或数字开头，以字母、数字或下划线结尾，且仅可包含字母、数字、下划线、句点或连字符。       |
    |描述     | 可选说明。        |         |

**命令**

- Azure CLI: [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell：[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>查看所有安全规则

网络安全组包含零个或多个规则。 要详细了解有关查看规则时所列的信息，请参阅[网络安全组概述](security-overview.md)。

1. 在门户顶部的搜索框中，输入“网络安全组”  。 “网络安全组”出现在搜索结果中时，将其选中  。
2. 从列表中选择要查看其规则的网络安全组。
3. 在“设置”下选择“入站安全规则”或“出站安全规则”    。

列表包含已创建的任何规则以及网络安全组[默认安全规则](security-overview.md#default-security-rules)。

**命令**

- Azure CLI: [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell：[Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>查看安全规则的详细信息

1. 在门户顶部的搜索框中，输入“网络安全组”  。 “网络安全组”出现在搜索结果中时，将其选中  。
2. 选择要查看其安全规则详细信息的网络安全组。
3. 在“设置”下选择“入站安全规则”或“出站安全规则”    。
4. 选择要查看其详细信息的规则。 有关所有设置的详细说明，请参阅[安全规则设置](#security-rule-settings)。

**命令**

- Azure CLI: [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell：[Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>更改安全规则

1. 完成[查看安全规则的详细信息](#view-details-of-a-security-rule)中的步骤。
2. 根据需要更改设置，然后选择“保存”  。 有关所有设置的详细说明，请参阅[安全规则设置](#security-rule-settings)。

**命令**

- Azure CLI: [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell：[Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>删除安全规则

1. 完成[查看安全规则的详细信息](#view-details-of-a-security-rule)中的步骤。
2. 依次选择“删除”、“是”。  

**命令**

- Azure CLI: [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell：[Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>使用应用程序安全组

应用程序安全组包含零个或多个网络接口。 要了解详细信息，请参阅[应用程序安全组](security-overview.md#application-security-groups)。 应用程序安全组中的所有网络接口必须存在于同一虚拟网络中。 要了解如何将网络接口添加到应用程序安全组，请参阅[将网络接口添加到应用程序安全组](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)。

### <a name="create-an-application-security-group"></a>创建应用程序安全组

1. 选择 Azure 门户左上角的“+ 创建资源”  。
2. 在“在市场中搜索”框中输入“应用程序安全组”   。 当“应用程序安全组”显示在搜索结果中时，将其选中，再次在“所有项”下选择“应用程序安全组”，然后选择“创建”     。
3. 输入或选择以下信息，然后选择“创建”  ：

    | 设置        | 值                                                   |
    | ---            | ---                                                     |
    | 名称           | 名称在资源组中必须唯一。        |
    | 订阅   | 选择订阅。                               |
    | 资源组 | 选择现有的资源组，或创建一个新的组。 |
    | Location       | 选择一个位置                                       |

**命令**

- Azure CLI: [az network asg create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell：[New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>查看所有应用程序安全组

1. 选择 Azure 门户左上角的“所有服务”  。
2. 在“所有服务筛选器”框中输入“应用程序安全组”，然后在其显示在搜索结果中时，选择“应用程序安全组”    。

**命令**

- Azure CLI: [az network asg list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell：[Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>查看特定应用程序安全组的详细信息

1. 选择 Azure 门户左上角的“所有服务”  。
2. 在“所有服务筛选器”框中输入“应用程序安全组”，然后在其显示在搜索结果中时，选择“应用程序安全组”    。
3. 选择要查看其详细信息的应用程序安全组。

**命令**

- Azure CLI: [az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell：[Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>更改应用程序安全组

1. 选择 Azure 门户左上角的“所有服务”  。
2. 在“所有服务筛选器”框中输入“应用程序安全组”，然后在其显示在搜索结果中时，选择“应用程序安全组”    。
3. 选择要更改其设置的应用程序安全组。 可以对应用程序安全组添加或删除标记，或者分配或删除权限。

- Azure CLI: [az network asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell：没有 PowerShell cmdlet。

### <a name="delete-an-application-security-group"></a>删除应用程序安全组

如果应用程序安全组中有任何网络接口，则不能将其删除。 通过更改网络接口设置或删除网络接口，从应用程序安全组中移除所有网络接口。 有关详细信息，请参阅[在应用程序安全组中添加或删除网络接口](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)或[删除网络接口](virtual-network-network-interface.md#delete-a-network-interface)。

1. 选择 Azure 门户左上角的“所有服务”  。
2. 在“所有服务筛选器”框中输入“应用程序安全组”，然后在其显示在搜索结果中时，选择“应用程序安全组”    。
3. 选择要删除的应用程序安全组。
4. 选择“删除”，然后选择“是”，删除应用程序安全组   。

**命令**

- Azure CLI: [az network asg delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell：[Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>权限

若要在网络安全组、安全规则和应用程序安全组上执行任务，必须将你的帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表中所列相应权限的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

### <a name="network-security-group"></a>网络安全组

| Action                                                        |   名称                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   获取网络安全组                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   创建或更新网络安全组                             |
| Microsoft.Network/networkSecurityGroups/delete                |   删除网络安全组                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   将网络安全组与子网或网络接口关联 


### <a name="network-security-group-rule"></a>网络安全组规则

| Action                                                        |   名称                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   获取规则                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   创建或更新规则                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   删除规则                                                         |

### <a name="application-security-group"></a>应用程序安全组

| Action                                                                     | 名称                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | 将 IP 配置加入到应用程序安全组中|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 将安全规则加入到应用程序安全组中    |
| Microsoft.Network/applicationSecurityGroups/read                           | 获取应用程序安全组                        |
| Microsoft.Network/applicationSecurityGroups/write                          | 创建或更新应用程序安全组           |
| Microsoft.Network/applicationSecurityGroups/delete                         | 删除应用程序安全组                     |

## <a name="next-steps"></a>后续步骤

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](template-samples.md)创建网络或应用程序安全组
- 为虚拟网络创建并应用 [Azure Policy](policy-samples.md)
