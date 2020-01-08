---
title: 创建 Azure 虚拟网络对等互连 - 不同部署模型 - 相同订阅 | Microsoft Docs
description: 了解如何在通过相同 Azure 订阅中的不同 Azure 部署模型创建的虚拟网络间创建虚拟网络对等互连。
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: kumud
ms.reviewer: anavin
ms.openlocfilehash: 720351463a9f8d5712c76401f3fbba64c3177e84
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871971"
---
# <a name="create-a-virtual-network-peering---different-deployment-models-same-subscription"></a>创建虚拟网络对等互连 - 不同部署模型，相同订阅

本文介绍如何在通过不同部署模型创建的虚拟网络间创建虚拟网络对等互连。 这两个虚拟网络位于同一订阅。 在两个虚拟网络之间建立对等互连可让不同虚拟网络中的资源以相同的带宽和延迟彼此通信，就像这些资源位于同一个虚拟网络中一样。 了解有关[虚拟网络对等互连](virtual-network-peering-overview.md)的详细信息。

创建虚拟网络对等互连的步骤有所不同，具体取决于虚拟网络是否位于相同订阅，以及创建虚拟网络的 [ Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 单击下表中的方案，了解如何采用其他方案创建虚拟网络对等互连：

|Azure 部署模型  | Azure 订阅  |
|--------- |---------|
|[均为 Resource Manager 模型](tutorial-connect-virtual-networks-portal.md) |相同|
|[均为 Resource Manager 模型](create-peering-different-subscriptions.md) |不同|
|[一个为 Resource Manager 模型，一个为经典模型](create-peering-different-deployment-models-subscriptions.md) |不同|

不能在通过经典部署模型部署的两个虚拟网络之间创建虚拟网络对等互连。 如需连接两个通过经典部署模型创建的虚拟网络，可使用 Azure [VPN 网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来连接它们。

本教程将在同一区域中的虚拟网络之间建立对等互连。 还可以将不同[受支持的区域](virtual-network-manage-peering.md#cross-region)中的虚拟网络对等互连。 建议在对等互连虚拟网络之前让自己熟悉[对等互连的要求和约束](virtual-network-manage-peering.md#requirements-and-constraints)。

可以使用 Azure 门户、Azure [命令行接口](#cli) (CLI)、Azure [PowerShell](#powershell) 或 Azure 资源管理器模板创建虚拟网络对等互连。 单击以前的任何工具链接直接转到使用所选工具创建虚拟网络对等互连的步骤。

## <a name="create-peering---azure-portal"></a>创建对等互连 - Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关权限列表，请参阅[虚拟网络对等互连权限](virtual-network-manage-peering.md#requirements-and-constraints)。
2. 依次单击“+ 新建”、“网络”、“虚拟网络”。   
3. 在“创建虚拟网络”边栏选项卡中，为以下设置输入或选择值，然后单击“创建”：  
    - **名称**：*myVnet1*
    - **地址空间**：*10.0.0.0/16*
    - **子网名称**：默认值 
    - **子网地址范围**：*10.0.0.0/24*
    - **订阅**：选择订阅
    - **资源组**：选择“新建”，并输入 myResourceGroup  
    - **位置**：*美国东部*
4. 单击“+ 新建”  。 在“在市场中搜索”框中，键入“虚拟网络”   。 单击搜索结果中出现的“虚拟网络”  。
5. 在“虚拟网络”边栏选项卡的“选择部署模型”框中选择“经典”，然后单击“创建”     。
6. 在“创建虚拟网络”边栏选项卡中，为以下设置输入或选择值，然后单击“创建”：  
    - **名称**：*myVnet2*
    - **地址空间**：10.1.0.0/16 
    - **子网名称**：默认值 
    - **子网地址范围**：10.1.0.0/24 
    - **订阅**：选择订阅
    - **资源组**：选择“使用现有项”，然后选择“myResourceGroup”  
    - **位置**：*美国东部*
7. 在门户顶部的“搜索资源”框中键入 *myResourceGroup*。  当“myResourceGroup”出现在搜索结果中时，请单击它。  随后将显示 **myresourcegroup** 资源组的边栏选项卡。 该资源组保存前面步骤中创建的两个虚拟网络。
8. 单击“myVNet1”。 
9. 在显示的“myVnet1”边栏选项卡中，单击左侧垂直选项列表中的“对等互连”。  
10. 在显示的“myVnet1 - 对等互连”边栏选项卡中，单击“+ 添加”  
11. 在显示的“添加对等互连”边栏选项卡中，输入或选择以下选项，然后单击“确定”：  
     - **名称**：*myVnet1ToMyVnet2*
     - **虚拟网络部署模型**：选择“经典”。 
     - **订阅**：选择订阅
     - **虚拟网络**：单击“选择虚拟网络”，然后单击“myVnet2”   。
     - **允许虚拟网络访问：** 确保选择“已启用”。 
    本教程不使用其他任何设置。 若要了解所有对等互连设置，请参阅[管理虚拟网络对等互连](virtual-network-manage-peering.md#create-a-peering)。
12. 在上一步骤中单击“确定”后，“添加对等互连”边栏选项卡将会关闭，并再次出现“myVnet1 - 对等互连”边栏选项卡。    几秒钟后，创建的对等互连将显示在该边栏选项卡中。 创建的 myVnet1ToMyVnet2 对等互连的“对等互连状态”列中列出了“已连接”    。

    现已建立对等互连。 在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。
13. **可选**：尽管本教程未介绍如何创建虚拟机，但可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
14. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-portal)部分中所述的步骤。

## <a name="cli"></a>创建对等互连 - Azure CLI

使用 Azure 经典 CLI 和 Azure CLI 完成以下步骤。 可在 Azure Cloud Shell 中完成这些步骤，只需在以下任一步骤中选择“试用”按钮，或者安装[经典 CLI](/cli/azure/install-cli-version-1.0?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 并在本地计算机上运行命令  。

1. 如果使用 Cloud Shell，请跳到步骤 2，因为 Cloud Shell 会自动登录到 Azure。 使用 `azure login` 命令打开命令会话并登录 Azure。
2. 输入 `azure config mode asm` 命令，在服务管理模式下运行 CLI 命令。
3. 输入以下命令创建虚拟网络（经典）：

   ```azurecli-interactive
   azure network vnet create --vnet myVnet2 --address-space 10.1.0.0 --cidr 16 --location "East US"
   ```

4. 使用 CLI 而非经典 CLI 执行以下 bash CLI 脚本。 有关在 Windows 计算机上运行 bash CLI 脚本的选项，请参阅[在 Windows 上安装 Azure CLI](/cli/azure/install-azure-cli-windows)。

   ```azurecli-interactive
   #!/bin/bash

   # Create a resource group.
   az group create \
     --name myResourceGroup \
     --location eastus

   # Create the virtual network (Resource Manager).
   az network vnet create \
     --name myVnet1 \
     --resource-group myResourceGroup \
     --location eastus \
     --address-prefix 10.0.0.0/16
   ```

5. 使用 CLI 在通过不同部署模型创建的两个虚拟网络之间创建虚拟网络对等互连。 将以下脚本复制到计算机上的文本编辑器。 将 `<subscription id>` 替换为订阅 ID。 如果不知道订阅 ID，请输入 `az account show` 命令。 输出中的 **id** 值就是订阅 ID。 将修改后的脚本粘贴到 CLI 会话，然后按 `Enter`。

   ```azurecli-interactive
   # Get the ID for VNet1.
   vnet1Id=$(az network vnet show \
     --resource-group myResourceGroup \
     --name myVnet1 \
     --query id --out tsv)

   # Peer VNet1 to VNet2.
   az network vnet peering create \
     --name myVnet1ToMyVnet2 \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --remote-vnet-id /subscriptions/<subscription id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2 \
     --allow-vnet-access
   ```

6. 执行该脚本后，请检查虚拟网络 (Resource Manager) 的对等互连。 复制以下命令，将其粘贴到 CLI 会话，然后按 `Enter`：

   ```azurecli-interactive
   az network vnet peering list \
     --resource-group myResourceGroup \
     --vnet-name myVnet1 \
     --output table
   ```

   该输出将在 PeeringState 列中显示“已连接”   。

   在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。
7. **可选**：尽管本教程未介绍如何创建虚拟机，但可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
8. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-cli)中所述的步骤。

## <a name="powershell"></a>创建对等互连 - PowerShell

1. 安装最新版本的 PowerShell [Azure](https://www.powershellgallery.com/packages/Azure) 和 [Az](https://www.powershellgallery.com/packages/Az/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 启动 PowerShell 会话。
3. 在 PowerShell 中，输入 `Add-AzureAccount` 命令登录 Azure。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关权限列表，请参阅[虚拟网络对等互连权限](virtual-network-manage-peering.md#requirements-and-constraints)。
4. 若要通过 PowerShell 创建虚拟网络（经典），必须新建网络配置文件，或修改现有网络配置文件。 了解如何[导出、更新和导入网络配置文件](virtual-networks-using-network-configuration-file.md)。 该文件应包括本教程中使用的虚拟网络的以下 VirtualNetworkSite 元素  ：

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > 导入更改的网络配置文件会导致订阅中现有虚拟网络（经典）发生变化。 请确保只添加之前的虚拟网络，且不会从订阅中更改或删除任何现有虚拟网络。
5. 输入 `Connect-AzAccount` 命令，登录 Azure，创建虚拟网络（资源管理器）。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关权限列表，请参阅[虚拟网络对等互连权限](virtual-network-manage-peering.md#requirements-and-constraints)。
6. 创建资源组和虚拟网络 (Resource Manager)。 复制该脚本，将其粘贴到 PowerShell，然后按 `Enter`。

    ```powershell
    # Create a resource group.
      New-AzResourceGroup -Name myResourceGroup -Location eastus

    # Create the virtual network (Resource Manager).
      $vnet1 = New-AzVirtualNetwork `
      -ResourceGroupName myResourceGroup `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus
    ```

7. 在通过不同部署模型创建的两个虚拟网络之间创建虚拟网络对等互连。 将以下脚本复制到计算机上的文本编辑器。 将 `<subscription id>` 替换为订阅 ID。 如果不知道订阅 ID，请输入 `Get-AzSubscription` 命令查看。 返回的输出中的 ID 值就是订阅 ID  。 若要执行该脚本，请从文本编辑器中复制修改后的脚本，然后在 PowerShell 会话中右键单击，然后按 `Enter`。

    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzVirtualNetworkPeering `
      -Name myVnet1ToMyVnet2 `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId /subscriptions/<subscription Id>/resourceGroups/Default-Networking/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnet2
    ```

8. 执行该脚本后，请检查虚拟网络（资源管理器）的对等互连。 复制以下命令，将其粘贴到 PowerShell 会话，然后按 `Enter`：

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    该输出将在 PeeringState 列中显示“已连接”   。

    在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

9. **可选**：尽管本教程未介绍如何创建虚拟机，但可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
10. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-powershell)中所述的步骤。

## <a name="delete"></a>删除资源

完成本教程后，你可能想要删除本教程中创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。

### <a name="delete-portal"></a>Azure 门户

1. 在门户的搜索框中，输入 **myResourceGroup**。 在搜索结果中，单击“myResourceGroup”。 
2. 在“myResourceGroup”边栏选项卡中，单击“删除”图标。  
3. 若要确认删除，请在“键入资源组名称”框中输入 **myResourceGroup**，然后单击“删除”。  

### <a name="delete-cli"></a>Azure CLI

1. 通过 Azure CLI，借助以下命令删除虚拟网络（资源管理器）：

    ```azurecli-interactive
    az group delete --name myResourceGroup --yes
    ```

2. 通过经典 CLI，借助以下命令删除虚拟网络（经典）：

    ```azurecli-interactive
    azure config mode asm

    azure network vnet delete --vnet myVnet2 --quiet
    ```

### <a name="delete-powershell"></a>PowerShell

1. 输入以下命令，删除虚拟网络 (Resource Manager)：

    ```powershell
    Remove-AzResourceGroup -Name myResourceGroup -Force
    ```

2. 若要通过 PowerShell 删除虚拟网络（经典），必须修改现有网络配置文件。 了解如何[导出、更新和导入网络配置文件](virtual-networks-using-network-configuration-file.md)。 删除本教程中使用的虚拟网络的以下 VirtualNetworkSite 元素：

    ```xml
    <VirtualNetworkSite name="myVnet2" Location="East US">
      <AddressSpace>
        <AddressPrefix>10.1.0.0/16</AddressPrefix>
      </AddressSpace>
      <Subnets>
        <Subnet name="default">
          <AddressPrefix>10.1.0.0/24</AddressPrefix>
        </Subnet>
      </Subnets>
    </VirtualNetworkSite>
    ```

    > [!WARNING]
    > 导入更改的网络配置文件会导致订阅中现有虚拟网络（经典）发生变化。 请确保只删除之前的虚拟网络，且不会从订阅中更改或删除任何其他现有虚拟网络。

## <a name="next-steps"></a>后续步骤

- 在为生产用途创建虚拟网络对等互连之前，请充分熟悉重要的[虚拟网络对等互连约束和行为](virtual-network-manage-peering.md#requirements-and-constraints)。
- 了解所有的[虚拟网络对等互连设置](virtual-network-manage-peering.md#create-a-peering)。
- 了解如何使用虚拟网络对等互连[创建中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。
