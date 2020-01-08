---
title: 创建、更改或删除 Azure 公共 IP 地址 | Microsoft Docs
description: 了解如何创建、更改或删除公共 IP 地址。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 6103a88aa06dac5c0a7e6f1fba39b0d1d0706d51
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854342"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>创建、更改或删除公共 IP 地址

了解公共 IP 地址，以及如何创建、更改和删除此类地址。 公共 IP 地址是具有自身可配置设置的资源。 将公共 IP 地址分配给支持公共 IP 地址的 Azure 资源以启用：
- 从 Internet 到资源的入站通信，如 Azure 虚拟机 (VM)、Azure 应用程序网关、Azure 负载均衡器、Azure VPN 网关等。 如果 VM 没有分配有公共 IP 地址，则仍可通过 Internet 与某些资源（如 VM）进行通信，前提是 VM 是负载均衡器后端池的一部分且负载均衡器分配有公共 IP 地址。 若要确定是否可向特定 Azure 服务的资源分配公共 IP 地址，或是否可通过其他 Azure 资源的公共 IP 地址与之通信，请参阅该服务的文档。
- 使用可预测的 IP 地址与 Internet 建立出站连接。 例如，如果某虚拟机未分配有公共 IP 地址，但其地址由 Azure 网络地址转换为可预测的公共地址，则默认情况下，该虚拟机可与 Internet 建立出站通信。 通过将公共 IP 地址分配给资源，可了解哪个 IP 地址用于出站连接。 尽管可预测，但地址可根据所选分配方法进行更改。 有关详细信息，请参阅[创建公共 IP 地址](#create-a-public-ip-address)。 有关从 Azure 资源建立出站连接的详细信息，请参阅[了解出站连接](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用门户，请打开 https://portal.azure.com ，并使用 Azure 帐户登录。
- 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

登录或连接到 Azure 所用的帐户必须分配有[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或者分配有可执行[权限](#permissions)中列出的适当操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

公共 IP 地址会产生少许费用。 若要查看定价，请参阅 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)页。

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

1. 在门户左上角的顶部选择“+ 创建资源”。
2. 在“在市场中搜索”框中输入“公共 IP 地址”。 当“公共 IP 地址”出现在搜索结果中时，请选择它。
3. 在“公共 IP 地址”下，选择“创建”。
4. 在“创建公共 IP 地址”下为以下设置输入或选择值，然后选择“创建”：

   |设置|必需?|详细信息|
   |---|---|---|
   |名称|是|该名称在所选的资源组中必须唯一。|
   |SKU|是|引入 SKU 之前创建的所有公共 IP 地址均为基本 SKU 公共 IP 地址。 创建公共 IP 地址后，无法更改此 SKU。 独立虚拟机、可用性集内的虚拟机或虚拟机规模集可使用基本 SKU 或标准 SKU。 不允许在可用性集或规模集内的虚拟机之间混用 SKU。 **基本** SKU：如果要在支持可用性区域的区域内创建公共 IP 地址，“可用性区域”设置默认设为“无”。 基本公共 Ip 不支持可用性区域。 **标准** SKU：标准 SKU 公共 IP 可关联到虚拟机或负载均衡器前端。 如果要在支持可用性区域的区域内创建公共 IP 地址，“可用性区域”设置默认设为“区域冗余”。 有关可用性区域的详细信息，请参阅“可用性区域”设置。 将地址关联到标准负载均衡器时需使用标准 SKU。 若要了解标准 负载均衡器的详细信息，请参阅 [Azure 负载均衡器标准 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 将标准 SKU 公共 IP 地址分配到虚拟机的网络接口时，必须使用[网络安全组](security-overview.md#network-security-groups)显式允许预期流量。 创建并关联网络安全组且显式允许所需流量之后，才可与资源通信。|
   |IP 版本|是| 选择 IPv4 或 IPv6。 虽然可将公共 IPv4 地址分配给多个 Azure 资源，但只可将 IPv6 公共 IP 地址分配给面向 Internet 的负载均衡器。 负载均衡器可将 IPv6 流量负载均衡到 Azure 虚拟机。 详细了解如何[将 IPv6 流量负载均衡到虚拟机](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 如果已选择“标准 SKU”，则不可选择“IPv6”。 仅在使用标准 SKU 时才可创建 IPv4 地址。|
   |IP 地址分配|是|**动态：** 只有在将公共 IP 与 Azure 资源相关联并首次启动该资源时，才分配动态地址。 如果将动态地址分配到某个资源（如虚拟机），且虚拟机已停止（已解除分配）并重启，则动态地址可能发生更改。 如果虚拟机重启或停止（但未解除分配），该地址将保持不变。 当公共 IP 地址资源与其关联的资源取消关联时，便会发布动态地址。 **静态：** 静态地址是在创建公共 IP 地址时分配的。 删除公共 IP 地址资源前，不会发布静态地址。 如果地址未与资源相关联，则可以在创建地址后更改分配方法。 如果地址已与资源相关联，则不能更改分配方法。 如果选择 IPv6 作为“IP 版本”，则分配方法为“动态”。 如果选择“标准”作为 SKU，则分配方法为“静态”。|
   |空闲超时(分钟)|否|不依赖于客户端发送 keep-alive 消息，将 TCP 或 HTTP 连接保持打开的分钟数。 如果选择 IPv6 作为“IP 版本”，则不能更改此值。 |
   |DNS 名称标签|否|必须在创建该名称的 Azure 位置（所有订阅和所有客户位置）中保持唯一。 Azure 会在其 DNS 中自动注册该名称和 IP 地址，使你能够连接到使用该名称的资源。 Azure 会将“location.cloudapp.azure.com”（其中 location 是所选的位置）此类默认子网追加到提供的名称后面，以创建完全限定的 DNS 名称。 如果选择同时创建这两个地址版本，则会将相同的 DNS 名称分配给 IPv4 和 IPv6 地址。 Azure 的默认 DNS 服务包含 IPv4 A 和 IPv6 AAAA 名称记录，并在查找 DNS 名称时响应这两个记录。 客户端选择要与哪个地址（IPv4 或 IPv6）通信。 除了使用带有默认后缀的 DNS 名称标签，还可以改用 Azure DNS 服务来配置带有自定义后缀（可解析为公用 IP 地址）的 DNS 名称。 有关详细信息，请参阅[将 Azure DNS 与 Azure 公用 IP 地址配合使用](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)。|
   |创建 IPv6（或 IPv4）地址|否| 为“IP 版本”选择的选项决定了是显示 IPv6 还是 IPv4。 例如，如果选择 IPv4 作为“IP 版本”，则此处将显示 IPv6。 如果选择“标准”作为 SKU，则无法选择创建 IPv6 地址。
   |名称（仅在勾选“创建 IPv6 (或 IPv4)地址”复选框时可见）|是，如果选择“创建 IPv6”（或 IPv4）复选框。|该名称必须不同于在此列表中的第一个“名称”中输入的名称。 如果选择同时创建 IPv4 和 IPv6 地址，门户将创建两个单独的公共 IP 地址资源，每个资源中分配有一个 IP 地址版本。|
   |IP 地址分配（仅在“创建 IPv6 (或 IPv4)地址”复选框时可见）|是，如果选择“创建 IPv6”（或 IPv4）复选框。|如果复选框显示“创建 IPv4 地址”，可选择分配方法。 如果复选框显示“创建 IPv6 地址”，则不能选择分配方法，因为必须采用“动态”方法。|
   |订阅|是|必须与要将公共 IP 地址关联到的资源位于同一[订阅](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)中。|
   |资源组|是|可与要将公共 IP 地址关联到的资源位于相同或不同的[资源组](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)中。|
   |Location|是|必须与要将公共 IP 地址关联到的资源位于同一[位置](https://azure.microsoft.com/regions)（也称为“区域”）。|
   |可用性区域| 否 | 选择受支持的位置时才会显示此设置。 有关支持位置的列表，请参阅[可用性区域概述](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 如果选择“标准”SKU，则自动选择“无”。 如果倾向于保证特定区域，可以选择一个特定区域。 任一选择皆不是区域冗余。 如果选择**标准** SKU：则会自动选择区域冗余，让数据路径在区域故障时具有恢复能力。 如果倾向于保证特定区域（区域故障时不具有恢复能力），可以选择一个特定区域。

**命令**

虽然门户提供了用于创建两个公共 IP 地址资源（一个 IPv4 和一个 IPv6）的选项，但以下 CLI 和 PowerShell 命令可使用任一 IP 版本的地址创建一个资源。 如果需要两个公共 IP 地址资源（每个 IP 版本一个），必须运行此命令两次，为公共 IP 地址资源指定不同的名称和版本。

|Tool|Command|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>查看、删除公共 IP 地址或更改其设置

1. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“公共 IP 地址”。 当“公共 IP 地址”出现在搜索结果中时，请选择它。
2. 选择要查看、更改其设置或从列表中删除的公共 IP 地址的名称。
3. 根据是要查看、删除还是更改公共 IP 地址，完成以下选项之一。
   - **视图**：“概述”部分显示公共 IP 地址的主要设置，例如与之关联的网络接口（如果地址与某个网络接口关联）。 门户不显示地址的版本（IPv4 或 IPv6）。 若要查看版本信息，请使用 PowerShell 或 CLI 命令查看公共 IP 地址。 如果 IP 地址版本为 IPv6，则门户、PowerShell 或 CLI 不显示分配的地址。
   - **删除**：若要删除公共 IP 地址，请在“概述”部分中选择“删除”。 如果该地址当前已关联到某个 IP 配置，则无法将其删除。 如果该地址当前已关联到某个配置，请选择“取消关联”，从该 IP 配置中取消关联该地址。
   - 更改：选择“配置”。 使用[创建公共 IP 地址](#create-a-public-ip-address)的步骤 4 中的信息更改设置。 要将 IPv4 地址的分配方法从静态更改为动态，必须先从该公共 IPv4 地址关联的 IP 配置中取消关联该地址。 然后，可将分配方法更改为动态，并选择“关联”将该 IP 地址关联到相同或不同的 IP 配置，或者让它保持取消关联状态。 若要取消关联公共 IP 地址，请在“概述”部分中选择“取消关联”。

   >[!WARNING]
   >将分配方法从静态更改为动态时，将丢失已分配到公共 IP 地址的 IP 地址。 尽管 Azure 公共 DNS 服务器会保留静态或动态地址与任何 DNS 名称标签（若已定义）之间的映射，但如果虚拟机在处于停止（解除分配）状态之后启动，动态 IP 地址可能更改。 若要防止地址更改，请分配静态 IP 地址。

**命令**

|Tool|Command|
|---|---|
|CLI|[az network public-ip list](/cli/azure/network/public-ip#az-network-public-ip-list) 用于列出公共 IP 地址；[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) 用于显示设置；[az network public-ip update](/cli/azure/network/public-ip#az-network-public-ip-update) 用于更新；[az network public-ip delete](/cli/azure/network/public-ip#az-network-public-ip-delete) 用于删除|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) 用于检索公共 IP 地址对象并查看其设置；[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) 用于更新设置；[Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) 用于删除|

## <a name="assign-a-public-ip-address"></a>分配公共 IP 地址

了解如何将公共 IP 地址分配给以下资源：

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM（创建时）或[现有 VM](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [面向 Internet 的负载均衡器](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 应用程序网关](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [使用 Azure VPN 网关建立站点到站点连接](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure 虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>权限

若要在公共 IP 地址上执行任务，必须将你的帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表中所列适当操作的[自定义](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

| Action                                                             | 名称                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | 读取公共 IP 地址                                          |
| Microsoft.Network/publicIPAddresses/write                          | 创建或更新公共 IP 地址                           |
| Microsoft.Network/publicIPAddresses/delete                         | 删除公共 IP 地址                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | 将公共 IP 地址关联到资源                    |

## <a name="next-steps"></a>后续步骤

- 使用 [PowerShell](powershell-samples.md) 或 [Azure CLI](cli-samples.md) 示例脚本或使用 Azure [资源管理器模板](template-samples.md)创建公共 IP 地址
- 为公共 IP 地址创建并应用 [Azure Policy](policy-samples.md)
