---
title: 添加或删除 Azure 虚拟机的网络接口 | Microsoft Docs
description: 了解如何向虚拟机中添加网络接口或从中删除网络接口。
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
ms.date: 12/15/2017
ms.author: kumud
ms.openlocfilehash: 23e46290af6bdb4c217d8fa0cd836673652fc81d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701375"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>向虚拟机中添加网络接口或从中删除网络接口

了解如何在创建 Azure 虚拟机 (VM) 时添加现有的网络接口，或者在处于“已停止”（“已解除分配”）状态的现有 VM 中添加或删除网络接口。 Azure 虚拟机可通过网络接口与 Internet、Azure 及本地资源进行通信。 一台 VM 可以有一个或多个网络接口。 

如果需要为网络接口添加、更改或删除 IP 地址，请参阅[管理网络接口 IP 地址](virtual-network-network-interface-addresses.md)。 如果需要创建、更改或删除网络接口，请参阅[管理网络接口](virtual-network-network-interface.md)。

## <a name="before-you-begin"></a>开始之前

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在完成本文任何部分中的步骤之前，请完成以下任务：

- 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用门户，请打开 https://portal.azure.com ，并使用 Azure 帐户登录。
- 如果使用 PowerShell 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中的命令，或从计算机运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。
- 如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本教程需要 Azure CLI 2.0.26 或更高版本。 运行 `az --version` 查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以创建与 Azure 的连接。

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>将现有网络接口添加到新 VM

通过门户创建虚拟机时，门户会使用默认设置创建一个网络接口，并将其附加到 VM。 无法使用 Azure 门户将现有网络接口添加到新的 VM，或创建具有多个网络接口的 VM。 可使用 CLI 或 PowerShell 执行这两种操作，但请务必熟悉相关[约束](#constraints)。 如果要创建具有多个网络接口的 VM，还必须将操作系统配置为在创建 VM 后正确使用它们。 了解如何将 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 或 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 配置为使用多个网络接口。

### <a name="commands"></a>命令

创建 VM 之前，请使用[创建网络接口](virtual-network-network-interface.md#create-a-network-interface)中的步骤创建网络接口。

|Tool|命令|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[New-AzVM](/powershell/module/az.compute/new-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>将网络接口添加到现有 VM

1. 登录到 Azure 门户。
2. 在门户顶部的搜索框中，键入要添加网络接口的 VM 名称，或依次选择“所有服务”、“虚拟机”以浏览 VM   。 找到 VM 后，选择它。 该 VM 必须支持要添加的网络接口的数量。 若要了解每个 VM 大小支持的网络接口数量，请参阅 [Azure 中 Linux 虚拟机的大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 [Azure 中 Windows 虚拟机的大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。  
3. 选择“设置”下的“概述”   。 选择“停止”，然后等到 VM 的“状态”更改为“已停止(已解除分配)”    。
4. 选择“设置”下的“网络”   。
5. 选择“附加网络接口”  。 在当前未附加到其他 VM 网络接口列表中，选择要附加的接口。

   >[!NOTE]
   >选择的网络接口无法启用加速网络，无法获得 IPv6 地址，并且必须位于包含当前附加到 VM 的网络接口的虚拟网络中。

   如果还没有网络接口，必须首先创建一个。 为此，请选择“创建网络接口”  。 若要详细了解如何创建网络接口，请参阅[创建网络接口](virtual-network-network-interface.md#create-a-network-interface)。 要详细了解向虚拟机添加网络接口时的其他约束，请参阅[约束](#constraints)。

6. 选择“确定”  。
7. 在“设置”下选择“概述”，然后选择“启动”来启动虚拟机。   
8. 将 VM 操作系统配置为正确使用多个网络接口。 了解如何将 [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 或 [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) 配置为使用多个网络接口。

### <a name="commands"></a>命令
|Tool|命令|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json)（引用）或[详细步骤](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)（参考）或[详细步骤](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>查看 VM 的网络接口

可以查看当前附加到 VM 的网络接口，了解每个网络接口的配置，以及分配给每个网络接口的 IP 地址。 

1. 使用分配有订阅“所有者”、“参与者”或“网络参与者”角色的帐户登录到 [Azure 门户](https://portal.azure.com)。 若要详细了解如何向帐户分配角色，请参阅[针对 Azure 基于角色的访问控制的内置角色](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“虚拟机”。   当“虚拟机”出现在搜索结果中时，请选择它。 
3. 选择单击想要查看其网络接口的 VM 的名称。
4. 在所选 VM 的“设置”部分，选择“网络”   。 要详细了解网络接口的设置及其更改方式，请参阅[管理网络接口](virtual-network-network-interface.md)。 若要了解如何添加、更改或删除分配到网络接口的 IP 地址，请参阅[管理网络接口 IP 地址](virtual-network-network-interface-addresses.md)。

### <a name="commands"></a>命令

|Tool|命令|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>从 VM 中删除网络接口

1. 登录到 Azure 门户。
2. 在门户顶部的搜索框中，搜索想要从中删除（拆离）网络接口的 VM 的名称，或依次选择“所有服务”、“虚拟机”以浏览 VM   。 找到 VM 后，选择它。
3. 选择“设置”下的“概述”，然后选择“停止”    。 等到 VM 的“状态”更改为“已停止(已解除分配)”   。
4. 选择“设置”下的“网络”   。
5. 选择“拆离网络接口”  。 在当前已附加到虚拟机的网络接口列表中，选择要拆离的网络接口。

   >[!NOTE]
   >如果只列出了一个网络接口，则无法拆离，因为虚拟机始终必须附加有至少一个网络接口。
6. 选择“确定”  。

### <a name="commands"></a>命令

|Tool|命令|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json)（引用）或[详细步骤](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)（参考）或[详细步骤](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>约束

- VM 必须附加有至少一个网络接口。
- VM 只能附加 VM 的大小能够支持的网络接口数量。 若要详细了解每个 VM 大小支持的网络接口数量，请参阅 [Azure 中 Linux 虚拟机的大小](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)或 [Azure 中 Windows 虚拟机的大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 所有大小都支持至少两个网络接口。
- 目前，添加到一个 VM 的网络接口不能附加到另一个 VM。 若要详细了解如何创建网络接口，请参阅[创建网络接口](virtual-network-network-interface.md#create-a-network-interface)。
- 过去，只能向支持多个网络接口且至少使用两个网络接口创建的 VM 中添加网络接口。 不能向使用一个网络接口创建的 VM 添加网络接口，即使 VM 大小支持多个网络接口也不可以。 相反，只能从至少有三个网络接口的 VM 中删除网络接口，因为使用至少两个网络接口创建的 VM 必须始终有至少两个网络接口。 这些约束全都不再适用。 现在，可以创建具有任何数量网络接口的 VM（只要 VM 支持该数量）。
- 默认情况下，附加到 VM 的第一个网络接口定义为主网络接口  。 VM 中的所有其他网络接口为“辅助”  网络接口。
- 虽然可以控制出站流量发送到哪个网络接口，但默认情况下，来自 VM 的所有出站流量都是通过分配给主网络接口的主 IP 配置的 IP 地址发出的。
- 过去，同一个可用性集中的所有 VM 都需要有一个或多个网络接口。 现在，同一个可用性集中可以存在具有任意数目的网络接口的 VM，只要 VM 大小支持该数目。 只能在创建 VM 时将其添加到可用性集。 若要详细了解可用性集，请参阅[在 Azure 中管理 VM 的可用性](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)。
- 尽管同一 VM 中的网络接口可以连接到虚拟网络中的不同子网，但这些网络接口必须全部连接到同一个虚拟网络。
- 可将任何主要或辅助网络接口的任何 IP 配置的任何 IP 地址添加到 Azure 负载均衡器后端池。 过去，只能将主要网络接口的主要 IP 地址添加到后端池。 若要详细了解 IP 地址和配置，请参阅[添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)。
- 删除 VM 不会删除附加到其中的网络接口。 删除 VM 时，网络接口将与该 VM 分离。 可将网络接口添加到不同的 VM，也可将其删除。
- 如果网络接口分配有专用 IPv6 地址，则在创建 VM 时必须将其添加（附加）到 VM。 创建 VM 后，无法将分配有 IPv6 地址的网络接口添加到 VM。 如果在创建虚拟机时添加分配有专用 IPv6 地址的网络接口，则无论该 VM 大小支持多少个网络接口，都只能将该网络接口添加到虚拟机。 请参阅[管理网络接口 IP 地址](virtual-network-network-interface-addresses.md)，深入了解如何将 IP 地址分配给网络接口。
- 对于 IPv6，创建 VM 后，无法向其附加已启用加速网络的网络接口。 此外，要利用加速网络，还必须完成 VM 操作系统中的步骤。 要详细了解加速网络和使用加速网络时的其他约束，请查看适用于 [Windows](create-vm-accelerated-networking-powershell.md) 或 [Linux](create-vm-accelerated-networking-cli.md) 虚拟机的加速网络。

## <a name="next-steps"></a>后续步骤
若要创建具有多个网络接口或 IP 地址的 VM，请参阅以下文章：

|任务|工具|
|---|---|
|创建具有多个 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|创建具有多个 IPv4 地址的单 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)、[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|创建具有专用 IPv6 地址的单 NIC VM（在 Azure 负载均衡器后）|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure 资源管理器模板](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
