---
title: 云服务的多个 VIP
titlesuffix: Azure Load Balancer
description: 概述 MultiVIP，以及如何在云服务上设置多个 VIP
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3e97bea85d4d97b159168b21b4a6e932e655ccfb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274709"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>为云服务配置多个 VIP

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

可以使用 Azure 提供的 IP 地址通过公共 Internet 访问 Azure 云服务。 此公共 IP 地址称为 VIP（虚拟 IP），因为它链接到 Azure 负载均衡器，而不是云服务中的虚拟机 (VM) 实例。 可以使用单个 VIP 访问云服务中的任何 VM 实例。

但是，在某些情况下，可能需要多个 VIP 作为同一云服务的入口点。 例如，云服务可能托管了多个网站，而这些网站需要使用默认端口 443 建立 SSL 连接，因为每个站点是针对不同的客户或租户托管的。 在此情况下，每个网站都需要有不同的面向公众的 IP 地址。 下图阐明了一个典型的多租户 Web 托管，它需要在同一个公共端口上使用多个 SSL 证书。

![多 VIP SSL 方案](./media/load-balancer-multivip/Figure1.png)

在上面的示例中，所有 VIP 使用相同的公共端口 (443)，流量重定向到托管所有网站的云服务的内部 IP 地址中唯一专用端口上的一个或多个负载均衡的 VM。

> [!NOTE]
> 需要使用多个 VIP 的另一种情形是在同一组虚拟机器上托管多个 SQL AlwaysOn 可用性组侦听器。

默认情况下，VIP 是动态的，这意味着，分配给云服务的实际 IP 地址会随着时间改变。 为了防止发生这种情况，可以为服务保留 VIP。 若要了解有关保留 VIP 的详细信息，请参阅[保留的公共 IP](../virtual-network/virtual-networks-reserved-public-ip.md)。

> [!NOTE]
> 有关 VIP 和保留 IP 的定价信息，请参阅 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses/)。

可以使用 PowerShell 来验证云服务使用的 VIP、添加和删除 VIP、将 VIP 关联到终结点，以及在特定 VIP 上配置负载均衡。

## <a name="limitations"></a>限制

目前，多 VIP 功能只能用于以下场景：

* **仅限 IaaS**。 只能为包含 VM 的云服务启用多个 VIP。 无法在 PaaS 场景中将多个 VIP 用于角色实例。
* **仅限 PowerShell**。 只能使用 PowerShell 来管理多个 VIP。

这些限制都是暂时性的，以后随时可能更改。 请务必重新访问此页，以了解将来发生的更改。

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>如何将 VIP 添加到云服务
要将 VIP 添加到你的服务，请运行以下 PowerShell 命令：

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

此命令显示的结果类似于下面的示例：

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>如何从云服务中删除 VIP
若要删除在上述示例中添加到服务的 VIP，请运行以下 PowerShell 命令：

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> 只能删除没有任何关联终结点的 VIP。


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>如何从云服务检索 VIP 信息
若要检索与云服务关联的 VIP，请运行以下 PowerShell 脚本：

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

脚本显示的结果类似于下面的示例：

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

在此示例中，云服务有 3 个 VIP：

* **Vip1** 是默认 VIP，知道这一点的原因是 IsDnsProgrammedName 的值设置为 true。
* **Vip2** 和 **Vip3** 未使用，因为它们没有任何 IP 地址。 仅当已将某个终结点关联到 VIP 时，才会使用相应的 VIP。

> [!NOTE]
> 订阅将只收取额外的 VIP 费用（在 VIP 与终结点关联后收取）。 有关定价的详细信息，请参阅 [IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>如何将 VIP 关联到终结点

要将云服务上的 VIP 关联到终结点，请运行以下 PowerShell 命令：

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

该命令使用端口 *8080* 上的 *TCP* 创建一个终结点（该终结点链接到端口 *80* 上名为 *Vip2* 的 VIP），并将它链接到名为 *myService* 的云服务中名为 *myVM1* 的 VM。

若要验证配置，请运行以下 PowerShell 命令：

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

输出内容类似于下面的示例：

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>如何在特定 VIP 上启用负载均衡

可以将单个 VIP 与多个虚拟机相关联，以实现负载均衡。 例如，有名为 *myService* 的云服务，以及名为 *myVM1* 和 *myVM2* 的两个虚拟机。 而云服务有多个 VIP，其中一个名为 *Vip2*。 要确保发往 *Vip2* 上端口 *81* 的所有流量都在端口 *8181* 上的 *myVM1* 与 *myVM2* 之间平衡，请运行以下 PowerShell 脚本：

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

也可以更新负载均衡器，以使用不同的 VIP。 例如，如果运行以下 PowerShell 命令，则会将负载均衡集更改为使用名为 Vip1 的 VIP：

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>后续步骤

[Azure 负载平衡 Azure Monitor 日志](load-balancer-monitor-log.md)

[面向 Internet 的负载均衡器概述](load-balancer-internet-overview.md)

[面向 Internet 的负载均衡器入门](load-balancer-get-started-internet-arm-ps.md)

[虚拟网络概述](../virtual-network/virtual-networks-overview.md)

[保留 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)
