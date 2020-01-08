---
title: 创建 Azure 虚拟网络对等互连 - 资源管理器 - 不同订阅
titlesuffix: Azure Virtual Network
description: 了解如何在通过不同 Azure 订阅中的 Resource Manager 创建的虚拟网络间创建虚拟网络对等互连。
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: 11144b1595370f9eb17afce71e0302a63468a089
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305704"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>创建虚拟网络对等互连 - Resource Manager，不同订阅

本教程介绍如何在通过 Resource Manager 创建的虚拟网络间创建虚拟网络对等互连。 虚拟网络位于不同订阅。 在两个虚拟网络之间建立对等互连可让不同虚拟网络中的资源以相同的带宽和延迟彼此通信，就像这些资源位于同一个虚拟网络中一样。 了解有关[虚拟网络对等互连](virtual-network-peering-overview.md)的详细信息。

创建虚拟网络对等互连的步骤有所不同，具体取决于虚拟网络是否位于相同订阅，以及创建虚拟网络的 [ Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 选择下表中的方案，了解如何采用其他方案创建虚拟网络对等互连：

|Azure 部署模型  | Azure 订阅  |
|--------- |---------|
|[均为 Resource Manager 模型](tutorial-connect-virtual-networks-portal.md) |相同|
|[一个为资源管理器模型，一个为经典模型](create-peering-different-deployment-models.md) |相同|
|[一个为 Resource Manager 模型，一个为经典模型](create-peering-different-deployment-models-subscriptions.md) |不同|

不能在通过经典部署模型部署的两个虚拟网络之间创建对等互连。 如需连接两个通过经典部署模型创建的虚拟网络，可使用 Azure [VPN 网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来连接它们。

本教程将在同一区域中的虚拟网络之间建立对等互连。 还可以将不同[受支持的区域](virtual-network-manage-peering.md#cross-region)中的虚拟网络对等互连。 建议在对等互连虚拟网络之前让自己熟悉[对等互连的要求和约束](virtual-network-manage-peering.md#requirements-and-constraints)。

可以使用 [Azure 门户](#portal)、Azure [命令行接口](#cli) (CLI)、Azure [PowerShell](#powershell)、或 [Azure 资源管理器模板](#template)创建虚拟网络对等互连。 选择前面的任何工具链接可以直接转到使用所选工具创建虚拟网络对等互连的步骤。

如果虚拟网络在不同的订阅中, 并且订阅与不同的 Azure Active Directory 租户相关联, 请在继续操作之前完成以下步骤:
1. 将每个 Active Directory 租户中的用户添加作为所对立的 Azure Active Directory 租户中的[宾客用户](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory)。
1. 每个用户都必须接受来自相反 Azure Active Directory 租户的来宾用户邀请。

## <a name="portal"></a>创建对等互连 - Azure 门户

下述步骤对每个订阅使用不同的帐户。 如果使用的帐户可访问这两个订阅，则可使用相同帐户完成所有步骤，跳过注销门户的步骤，及为虚拟网络分配其他用户权限的步骤。

1. 以用户 A  的身份登录到 [Azure 门户](https://portal.azure.com)。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关权限列表，请参阅[虚拟网络对等互连权限](virtual-network-manage-peering.md#permissions)。
2. 选择“+ 创建资源”，然后依次选择“网络”和“虚拟网络”    。
3. 为以下设置选择或输入以下示例值，然后选择“创建”  ：
    - 名称：myVnetA  
    - **地址空间**：*10.0.0.0/16*
    - **子网名称**：默认值 
    - **子网地址范围**：*10.0.0.0/24*
    - **订阅**：选择订阅 A。
    - **资源组**：选择“新建”  ，然后输入 myResourceGroupA 
    - **位置**：*美国东部*
4. 在门户顶部的“搜索资源”框中键入 myVnetA   。 选择出现在搜索结果中的“myVnetA”  。 
5. 从左侧的垂直选项列表中选择“访问控制(IAM)”。 
6. 在“myVnetA - 访问控制(IAM)”  下，选择“+ 添加角色分配”  。
7. 在“角色”框中选择“网络参与者”。  
8. 在“选择”框中，选择 *UserB*，或者键入 UserB 的电子邮件地址来搜索该用户。 
9. 选择**保存**。
10. 在“myVnetA - 访问控制 (IAM)”下，选择左侧垂直选项列表中的“属性”   。 复制“资源 ID”，在稍后的步骤中使用  。 资源 ID 类似于以下示例: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`。
11. 以用户 A 的身份注销门户，然后以用户 B 的身份登录。
12. 完成步骤 2-3，在步骤 3 中输入或选择以下值：

    - 名称：myVnetB  
    - **地址空间**：10.1.0.0/16 
    - **子网名称**：默认值 
    - **子网地址范围**：10.1.0.0/24 
    - **订阅**：选择订阅 B。
    - **资源组**：选择“新建”  ，然后输入 myResourceGroupB 
    - **位置**：*美国东部*

13. 在门户顶部的“搜索资源”框中键入 myVnetB   。 选择出现在搜索结果中的“myVnetB”  。
14. 在“myVnetB”下，选择左侧垂直选项列表中的“属性”   。 复制“资源 ID”，在稍后的步骤中使用  。 资源 ID 类似于以下示例: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`。
15. 在“myVnetB”下选择“访问控制(IAM)”，然后为 myVnetB 完成步骤 5-10，在步骤 8 中输入 **UserA**。  
16. 以用户 B 的身份注销门户，然后以用户 A 的身份登录。
17. 在门户顶部的“搜索资源”框中键入 myVnetA   。 选择出现在搜索结果中的“myVnetA”  。
18. 选择“myVnetA”  。
19. 在“设置”  下，选择“对等”  。
20. 在“myVnetA - 对等互连”  下，选择“+ 添加”  。
21. 在“添加对等互连”  下，输入或选择以下选项，然后选择“确定”  ：
     - 名称：myVnetAToMyVnetB  
     - **虚拟网络部署模型**：选择“Resource Manager”  。
     - **我知道我的资源 ID**：选中此框。
     - **资源 ID**：输入步骤 14 中的资源 ID。
     - **允许虚拟网络访问：** 确保选择“已启用”。 
    本教程不使用其他任何设置。 若要了解所有对等互连设置，请参阅[管理虚拟网络对等互连](virtual-network-manage-peering.md#create-a-peering)。
22. 在上一步骤中选择“确定”后，等待片刻，你创建的对等互连将会出现。  创建的 myVnetAToMyVnetB 对等互连的“对等互连状态”列中列出了“已启动”    。 已将 myVnetA 对等互连到 myVnetB，但现在必须将 myVnetB 对等互连到 myVnetA。 必须朝两个方向创建对等互连才能让虚拟网络中的资源相互通信。
23. 注销用户 A 的门户登录，然后以用户 B 的身份登录。
24. 针对 myVnetB 再次完成步骤 17-21。 在步骤 21 中，命名对等互连 myVnetBToMyVnetA，为虚拟网络选择 myVnetA，并在“资源ID”框中输入步骤 10 中的 ID     。
25. 选择“确定”来为 myVnetB 创建对等互连后，几秒钟后，将会列出刚刚创建的 myVnetBToMyVnetA 对等互连，“对等互连状态”列中显示“已连接”     。
26. 以用户 B 的身份注销门户，然后以用户 A 的身份登录。
27. 再次完成步骤 17-19。 myVnetAToVNetB 对等互连的“对等互连状态”现也显示为“已连接”    。 对等互连中两个虚拟网络的“对等互连状态”列都显示为“已连接”后，即表示已成功建立对等互连。   在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。
28. **可选**：尽管本教程未介绍如何创建虚拟机，但可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
29. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-portal)部分中所述的步骤。

## <a name="cli"></a>创建对等互连 - Azure CLI

本教程为每个订阅使用不同的帐户。 如果使用的帐户可访问这两个订阅，则可使用相同帐户完成所有步骤，可跳过注销 Azure 的步骤，并删除创建用户角色分配的脚本行。 将以下所有脚本中的 UserA@azure.com 和 UserB@azure.com 替换为用户 A 和用户 B 使用的用户名。 

以下脚本：

- 需要 Azure CLI 2.0.4 或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 在 Bash shell 中操作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 上安装 Azure CLI](/cli/azure/install-azure-cli-windows)。

除安装 CLI 及其依赖项外，还可使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 选择下面脚本中的“试用”按钮，调用一个可用于登录 Azure 帐户的 Cloud Shell。 

1. 使用 `azure login` 命令打开 CLI 会话并以用户 A 的身份登录 Azure。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关权限列表，请参阅[虚拟网络对等互连权限](virtual-network-manage-peering.md#permissions)。
2. 将以下脚本复制到电脑上的文本编辑器，将 `<SubscriptionA-Id>` 替换为订阅 A 的 ID，然后复制修改后的脚本，将其粘贴到 CLI 会话，按 `Enter`。 如果不知道订阅 ID，请输入 `az account show` 命令。 输出中的 ID 值就是订阅 ID  。

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. 使用 `az logout` 命令注销用户 A 的 Azure 登录，然后以用户 B 的身份登录 Azure。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关权限列表，请参阅[虚拟网络对等互连权限](virtual-network-manage-peering.md#permissions)。
4. 创建 myVnetB。 将步骤 2 中的脚本内容复制到电脑的文本编辑器。 将 `<SubscriptionA-Id>` 替换为订阅 B 的 ID。 将 10.0.0.0/16 更改为 10.1.0.0/16，将所有 A 更改为 B，并将所有 B 更改为 A。复制修改后的脚本，将其粘贴到 CLI 会话，然后按 `Enter`。
5. 注销用户 B 的 Azure 登录，然后以用户 A 的身份登录 Azure。
6. 创建从 myVnetA 到 myVnetB 的虚拟网络对等互连。 将以下脚本内容复制到计算机上的文本编辑器。 将 `<SubscriptionB-Id>` 替换为订阅 B 的 ID。 若要执行该脚本，请复制修改后的脚本，将其粘贴到 CLI 会话，然后按 Enter。

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. 查看 myVnetA 的对等互连状态。

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    状态为“已启动”  。 创建从 myVnetB 到 myVnetA 的对等互连后，状态即会变为“已连接”  。

8. 注销用户 A 的 Azure 登录，然后以用户 B 的身份登录 Azure。
9. 创建从 myVnetB 到 myVnetA 的对等互连。 将步骤 6 中的脚本内容复制到电脑的文本编辑器。 将 `<SubscriptionB-Id>` 替换为订阅 A 的 ID，将所有 A 更改为 B，并将所有 B 更改为 A。更改完成后，复制修改后的脚本，将其粘贴到 CLI 会话，按 `Enter`。
10. 查看 myVnetB 的对等互连状态。 将步骤 7 中的脚本内容复制到电脑的文本编辑器。 将资源组和虚拟网络名称中的 A 更改为 B，复制该脚本，将修改后的脚本粘贴到 CLI 会话，然后按 `Enter`。 对等互连状态为“已连接”  。 创建从 myVnetB 到 myVnetA 的对等互连后，myVnetA 的对等互连状态变为“已连接”  。 可以用户 A 的身份重新登录 Azure，并再次完成步骤 7，验证 myVnetA 的对等互连状态。 

    > [!NOTE]
    > 直到两个虚拟网络的对等互连状态均为“已连接”时，对等互连才建立成功  。

11. **可选**：尽管本教程未介绍如何创建虚拟机，但可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
12. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-cli)中所述的步骤。

在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

## <a name="powershell"></a>创建对等互连 - PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本教程为每个订阅使用不同的帐户。 如果使用的帐户可访问这两个订阅，则可使用相同帐户完成所有步骤，可跳过注销 Azure 的步骤，并删除创建用户角色分配的脚本行。 将以下所有脚本中的 UserA@azure.com 和 UserB@azure.com 替换为用户 A 和用户 B 使用的用户名。

1. 确认 Azure PowerShell 1.0.0 或更高版本。 若要执行此`Get-Module -Name Az`操作, 我们建议安装最新版本的 PowerShell [Az module](/powershell/azure/install-az-ps)。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。 
2. 启动 PowerShell 会话。
3. 在 PowerShell 中，输入 `Connect-AzAccount` 命令以用户 A 的身份登录 Azure。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关权限列表，请参阅[虚拟网络对等互连权限](virtual-network-manage-peering.md#permissions)。
4. 创建资源组和虚拟网络 A。将以下脚本复制到电脑的文本编辑器。 将 `<SubscriptionA-Id>` 替换为订阅 A 的 ID。 如果不知道订阅 ID，请输入 `Get-AzSubscription` 命令查看。 返回的输出中的 ID 值就是订阅 ID  。 若要执行该脚本，请复制修改后的脚本，将其粘贴到 PowerShell，然后按 `Enter`。

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. 注销用户 A 的 Azure 登录，然后以用户 B 的身份登录。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关权限列表，请参阅[虚拟网络对等互连权限](virtual-network-manage-peering.md#permissions)。
6. 将步骤 4 中的脚本内容复制到电脑的文本编辑器。 将 `<SubscriptionA-Id>` 替换为订阅 B 的 ID。将 10.0.0.0/16 更改为 10.1.0.0/16。 将所有 A 更改为 B，并将所有 B 更改为 A。若要执行该脚本，请复制修改后的脚本，将其粘贴到 PowerShell，然后按 `Enter`。
7. 注销用户 B 的 Azure 登录，然后以用户 A 的身份登录。
8. 创建从 myVnetA 到 myVnetB 的对等互连。 将以下脚本复制到计算机上的文本编辑器。 将 `<SubscriptionB-Id>` 替换为订阅 B 的 ID。若要执行该脚本，请复制修改后的脚本，将其粘贴到 PowerShell，然后按 `Enter`。

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. 查看 myVnetA 的对等互连状态。

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    状态为“已启动”  。 设置从 myVnetB 到 myVnetA 的对等互连后，状态即会变为“已连接”  。

10. 注销用户 A 的 Azure 登录，然后以用户 B 的身份登录。
11. 创建从 myVnetB 到 myVnetA 的对等互连。 将步骤 8 中的脚本内容复制到电脑的文本编辑器。 将 `<SubscriptionB-Id>` 替换为订阅 A 的 ID，并分别将所有 A 更改为 B，将所有 B 更改为 A。要执行该脚本，请复制修改后的脚本，将其粘贴到 PowerShell，然后按 `Enter`。
12. 查看 myVnetB 的对等互连状态。 将步骤 9 中的脚本内容复制到电脑的文本编辑器。 将资源组和虚拟网络名称中的 A 更改为 B。 若要执行该脚本，请将修改后的脚本粘贴到 PowerShell，然后按 `Enter`。 状态为“已连接”  。 创建从 myVnetB 到 myVnetA 的对等互连后，myVnetA 的对等互连状态变为“已连接”     。 可以用户 A 的身份重新登录 Azure，并再次完成步骤 9，验证 myVnetA 的对等互连状态。

    > [!NOTE]
    > 直到两个虚拟网络的对等互连状态均为“已连接”时，对等互连才建立成功  。

    在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

13. **可选**：尽管本教程未介绍如何创建虚拟机，但可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
14. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-powershell)中所述的步骤。

## <a name="template"></a>创建对等互连 - 资源管理器模板

1. 若要创建虚拟网络并分配合适的[权限](virtual-network-manage-peering.md#permissions)，请完成本文中[门户](#portal)、[Azure CLI](#cli) 或 [PowerShell](#powershell) 部分中所述的步骤。
2. 将下面的文本保存到本地计算机上的某个文件中。 将 `<subscription ID>` 替换为用户 A 的订阅 ID。 例如，可能会将文件另存为 vnetpeeringA.json。

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. 以用户 A 的身份登录 Azure，并使用[门户](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template)、[PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template) 或 [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template) 来部署模板。 指定在步骤 2 中用于保存示例 json 文本的文件的文件名。
4. 将步骤 2 中的示例 json 复制到本地计算机上的某地文件中，并对如下开头的行进行更改：
   - **名称**：将 myVnetA/myVnetAToMyVnetB 更改为 myVnetB/myVnetBToMyVnetA   。
   - **id**：将 `<subscription ID>` 替换为用户 B 的订阅 ID，并将 myVnetB 更改为 myVnetA   。
5. 再次完成步骤 3，以用户 B 的身份登录 Azure。
6. **可选**：尽管本教程未介绍如何创建虚拟机，但可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
7. **可选**：若要删除在本教程中创建的资源，请使用 Azure 门户、PowerShell 或 Azure CLI 完成本文的[删除资源](#delete)部分中所述的步骤。

## <a name="delete"></a>删除资源
完成本教程后，你可能想要删除本教程中创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。

### <a name="delete-portal"></a>Azure 门户

1. 以用户 A 的身份登录 Azure 门户。
2. 在门户的搜索框中，输入 myResourceGroupA  。 在搜索结果中，选择“myResourceGroupA”  。
3. 选择“删除”。 
4. 若要确认删除，请在“键入资源组名称”框中输入“myResourceGroupA”，然后选择“删除”    。
5. 注销用户 A 的门户登录，然后以用户 B 的身份登录。
6. 完成 myResourceGroupB 的步骤 2-4.

### <a name="delete-cli"></a>Azure CLI

1. 以用户 A 的身份登录 Azure，并执行以下命令：

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. 注销用户 A 的 Azure 登录，然后以用户 B 的身份登录。
3. 请执行以下命令：

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="delete-powershell"></a>PowerShell

1. 以用户 A 的身份登录 Azure，并执行以下命令：

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. 注销用户 A 的 Azure 登录，然后以用户 B 的身份登录。
3. 请执行以下命令：

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>后续步骤

- 在为生产用途创建虚拟网络对等互连之前，请充分熟悉重要的[虚拟网络对等互连约束和行为](virtual-network-manage-peering.md#requirements-and-constraints)。
- 了解所有的[虚拟网络对等互连设置](virtual-network-manage-peering.md#create-a-peering)。
- 了解如何使用虚拟网络对等互连[创建中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。
