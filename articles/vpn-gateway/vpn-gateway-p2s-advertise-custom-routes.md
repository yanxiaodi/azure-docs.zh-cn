---
title: 为点到站点 VPN 客户端播发自定义路由 |Azure |Microsoft Docs
description: 将自定义路由播发到点到站点客户端的步骤
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/26/2019
ms.author: cherylmc
ms.openlocfilehash: 38250d1cd9853013ba9721ece0201a8df6dd1b4a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336296"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>播发 P2S VPN 客户端的自定义路由

你可能想要将自定义路由播发到你的所有点到站点 VPN 客户端。 例如，你在 VNet 中启用了存储终结点，并且希望远程用户能够通过 VPN 连接访问这些存储帐户。 可以将存储终结点的 IP 地址播发到所有远程用户，以便到存储帐户的流量通过 VPN 隧道，而不是公共 Internet。

![Azure VPN 网关多站点连接示例](./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png)

## <a name="to-advertise-custom-routes"></a>公布自定义路由

若要播发自定义路由， `Set-AzVirtualNetworkGateway cmdlet`请使用。 下面的示例演示如何为[Contoso 存储帐户表](https://contoso.table.core.windows.net)公布 IP。

1. Ping *contoso.table.core.windows.net*并记录 IP 地址。 例如：

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 运行以下 PowerShell 命令：

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 若要添加多个自定义路由，请使用逗号和空格分隔地址。 例如：

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```
## <a name="to-view-custom-routes"></a>查看自定义路由

使用以下示例查看自定义路由：

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```

## <a name="next-steps"></a>后续步骤

有关其他 P2S 路由信息，请参阅[关于点到站点路由](vpn-gateway-about-point-to-site-routing.md)。
