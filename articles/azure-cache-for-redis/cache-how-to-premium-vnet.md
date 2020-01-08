---
title: 为高级 Azure Redis 缓存配置虚拟网络 | Microsoft Docs
description: 了解如何为高级层 Azure Redis 缓存实例创建和管理虚拟网络支持
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: yegu
ms.openlocfilehash: ec21c26c705dab94b15c1f76be5e62207b9f206f
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815673"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>如何为高级 Azure Redis 缓存配置虚拟网络支持
Azure Redis 缓存有不同的缓存套餐，因此在缓存大小和功能（包括群集、暂留和虚拟网络支持等高级层功能）的选择上很灵活。 VNet 是云中的专用网络。 为 Azure Redis 缓存实例配置了 VNet 后，该实例不可公开寻址，而只能从 VNet 中的虚拟机和应用程序进行访问。 本文说明如何为高级 Azure Redis 缓存实例配置虚拟网络支持。

> [!NOTE]
> Azure Redis 缓存同时支持经典 VNet 和资源管理器 VNet。
> 
> 

有关其他高级缓存功能的信息，请参阅 [Azure Redis 缓存高级层简介](cache-premium-tier-intro.md)。

## <a name="why-vnet"></a>为何使用 VNet？
[Azure 虚拟网络 (VNet)](https://azure.microsoft.com/services/virtual-network/) 部署为 Azure Redis 缓存提供增强的安全性和隔离性，并提供子网、访问控制策略以及其他进一步限制访问的功能。

## <a name="virtual-network-support"></a>虚拟网络支持
在创建缓存期间，可在“新建 Azure Redis 缓存”边栏选项卡中配置虚拟网络 (VNet) 支持。 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

选择高级定价层后，可以通过选择与缓存相同的订阅和位置的 VNet 来配置 Redis VNet 集成。 若要使用新 VNet，请先创建 VNet，方法是执行[使用 Azure 门户创建虚拟网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)或[使用 Azure 门户创建虚拟网络（经典）](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)中的步骤，然后返回“新建 Azure Redis 缓存”边栏选项卡来创建并配置高级缓存。

若要为新缓存配置 VNet，请单击“新建 Azure Redis 缓存”边栏选项卡上的“虚拟网络”，然后从下拉列表中选择所需的 VNet。

![虚拟网络][redis-cache-vnet]

从“子网”下拉列表中选择所需的子网，并指定所需的“静态 IP 地址”。 如果使用经典 VNet，则“静态 IP 地址”字段是可选的；如果未指定任何地址，将从选定的子网中选择一个。

> [!IMPORTANT]
> 将 Azure Redis 缓存部署到资源管理器 VNet 时，缓存必须位于专用子网中，该子网中只能包含 Azure Redis 缓存实例，而不能包含其他资源。 如果尝试将 Azure Redis 缓存部署到包含其他资源的资源管理器 VNet 子网，部署会失败。
> 
> 

![虚拟网络][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure 会保留每个子网中的某些 IP 地址，但是这些地址不能使用。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> 除了 Azure VNET 基础结构使用的 IP 地址外，子网中的每个 Redis 实例还会针对负载均衡器使用每分片两个 IP 地址和一个附加 IP 地址。 非群集缓存视为具有一个分片。
> 
> 

创建缓存之后，可以在“资源菜单”中单击“虚拟网络”，查看 VNet 的配置。

![虚拟网络][redis-cache-vnet-info]

若要在使用 VNet 时连接到 Azure Redis 缓存实例，请在连接字符串中指定缓存的主机名，如以下示例所示：

    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

## <a name="azure-cache-for-redis-vnet-faq"></a>Azure Redis 缓存 VNet 常见问题解答
以下列表包含有关 Azure Redis 缓存缩放的常见问题的解答。

* Azure Redis 缓存和 VNet 有哪些常见的错误配置问题？
* [如何验证 VNET 中缓存是否正常工作？](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* 尝试连接到 VNET 中的 Azure Redis 缓存时，为何会收到一项指出远程证书无效的错误？
* [是否可以通过标准或基本缓存使用 VNet？](#can-i-use-vnets-with-a-standard-or-basic-cache)
* 为什么在某些子网中创建 Azure Redis 缓存会失败，而在其他子网中不会失败？
* [子网地址空间有哪些要求？](#what-are-the-subnet-address-space-requirements)
* [在 VNET 中托管缓存时，是否可以使用所有缓存功能？](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Azure Redis 缓存和 VNet 有哪些常见的错误配置问题？
在 VNet 中托管 Azure Redis 缓存时，将使用下表中的端口。 

>[!IMPORTANT]
>如果下表中的端口受阻，则缓存可能无法正常工作。 在 VNet 中使用 Azure Redis 缓存时，阻止这些端口中的一个或多个是最常见的错误配置问题。
> 
> 

- [出站端口要求](#outbound-port-requirements)
- [入站端口要求](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>出站端口要求

有 7 个出站端口要求。

- 与 Internet 的所有出站连接都可以通过客户端的本地审核设备建立。
- 3 个端口将流量路由到服务 Azure 存储和 Azure DNS 的 Azure 终结点。
- 剩余端口用途不同，针对内部 Redis 子网通信。 内部 Redis 子网通信不需要子网 NSG 规则。

| 端口 | Direction | 传输协议 | 用途 | 本地 IP | 远程 IP |
| --- | --- | --- | --- | --- | --- |
| 80、443 |出站 |TCP |Azure 存储/PKI (Internet) 上的 Redis 依赖关系 | （Redis 子网） |* |
| 53 |出站 |TCP/UDP |DNS (Internet/VNet) 上的 Redis 依赖关系 | （Redis 子网） | 168.63.129.16 和 169.254.169.254 <sup>1</sup> 以及子网的任何自定义 DNS 服务器 <sup>3</sup> |
| 8443 |出站 |TCP |Redis 的内部通信 | （Redis 子网） | （Redis 子网） |
| 10221-10231 |出站 |TCP |Redis 的内部通信 | （Redis 子网） | （Redis 子网） |
| 20226 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 13000-13999 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 15000-15999 |出站 |TCP |Redis 和异地复制的内部通信 | （Redis 子网） |（Redis 子网）（异地副本对等子网） |
| 6379-6380 |出站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |

<sup>1</sup> Microsoft 拥有的这些 IP 地址用于对为 Azure DNS 提供服务的主机 VM 进行寻址。

<sup>3</sup> 没有自定义 DNS 服务器的子网或忽略自定义 DNS 的更新 redis 缓存不需要。

#### <a name="geo-replication-peer-port-requirements"></a>异地复制对等端口要求

如果在 Azure 虚拟网络中的缓存之间使用 georeplication，请注意，建议的配置是将入站和出站方向的整个子网的端口15000-15999 取消阻止到这两个缓存，以便所有副本组件即使发生了异地故障转移，子网中也可以直接相互通信。

#### <a name="inbound-port-requirements"></a>入站端口要求

有 8 个入站端口范围要求。 这些范围内的入站请求是指从同一 VNET 上托管的其他服务入站或者来自 Redis 子网通信内部。

| 端口 | Direction | 传输协议 | 用途 | 本地 IP | 远程 IP |
| --- | --- | --- | --- | --- | --- |
| 6379、6380 |入站 |TCP |与 Redis 的客户端通信、Azure 负载均衡 | （Redis 子网） | （Redis 子网）、虚拟网络、Azure 负载均衡器 <sup>2</sup> |
| 8443 |入站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |
| 8500 |入站 |TCP/UDP |Azure 负载均衡 | （Redis 子网） |Azure 负载均衡器 |
| 10221-10231 |入站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网）、Azure 负载均衡器 |
| 13000-13999 |入站 |TCP |与 Redis 群集的客户端通信、Azure 负载均衡 | （Redis 子网） |虚拟网络、Azure 负载均衡器 |
| 15000-15999 |入站 |TCP |与 Redis 群集的客户端通信、Azure 负载平衡和异地复制 | （Redis 子网） |虚拟网络，Azure 负载均衡器，（异地副本对等子网） |
| 16001 |入站 |TCP/UDP |Azure 负载均衡 | （Redis 子网） |Azure 负载均衡器 |
| 20226 |入站 |TCP |Redis 的内部通信 | （Redis 子网） |（Redis 子网） |

<sup>2</sup> 可以使用服务标记“AzureLoadBalancer”（资源管理器）或“AZURE_LOADBALANCER”（经典）来创作 NSG 规则。

#### <a name="additional-vnet-network-connectivity-requirements"></a>其他 VNET 网络连接要求

在虚拟网络中，可能一开始不符合 Azure Redis 缓存的网络连接要求。 在虚拟网络中使用时，Azure Redis 缓存需要以下所有项才能正常运行。

* 与全球 Azure 存储终结点建立的出站网络连接。 这包括位于 Azure Redis 缓存实例区域的终结点，以及位于**其他** Azure 区域的存储终结点。 Azure 存储终结点在以下 DNS 域下解析：*table.core.windows.net*、*blob.core.windows.net*、*queue.core.windows.net* 和 *file.core.windows.net*。 
* 与 *ocsp.msocsp.com*、*mscrl.microsoft.com* 和 *crl.microsoft.com* 建立的出站网络连接。 需要此连接才能支持 SSL 功能。
* 虚拟网络的 DNS 设置必须能够解析前面几点所提到的所有终结点和域。 确保已针对虚拟网络配置并维护有效的 DNS 基础结构即可符合这些 DNS 要求。
* 与以下 Azure 监视终结点（在下列 DNS 域下进行解析）的出站网络连接：shoebox2-black.shoebox2.metrics.nsatc.net、north-prod2.prod2.metrics.nsatc.net、azglobal-black.azglobal.metrics.nsatc.net、shoebox2-red.shoebox2.metrics.nsatc.net、east-prod2.prod2.metrics.nsatc.net、azglobal-red.azglobal.metrics.nsatc.net。

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>如何验证 VNET 中缓存是否正常工作？

>[!IMPORTANT]
>连接到 VNET 中托管的 Azure Redis 缓存实例时，缓存客户端必须位于同一 VNET 中或已启用 VNET 对等互连的 VNET 中。 这包括任何测试应用程序或诊断 ping 工具。 无论客户端应用程序在哪里托管，都必须配置网络安全组，这样客户端的网络流量才能到达 Redis 实例。
>
>

端口需求按上一部分所述进行配置后，可以通过执行以下步骤验证缓存是否正常运行。

- [重新启动](cache-administration.md#reboot)所有缓存节点。 如果无法访问所有所需的缓存依赖项（如[入站端口需求](cache-how-to-premium-vnet.md#inbound-port-requirements)和[出站端口需求](cache-how-to-premium-vnet.md#outbound-port-requirements)中所述），缓存将无法成功重启。
- 重启缓存节点后（依据 Azure 门户里的缓存状态），可以执行以下测试：
  - 使用 [tcping](https://www.elifulkerson.com/projects/tcping.php)，对与缓存位于同一 VNET 中的计算机的缓存终结点（使用 6380 端口）执行 ping 操作。 例如：
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    如果 `tcping` 工具报告该端口已打开，则可使用缓存连接 VNET 中的客户端。

  - 进行测试的另一种方法是创建测试缓存客户端（可以是使用 StackExchange.Redis 的简单控制台应用程序），使其连接到缓存，以便添加和检索缓存的某些项。 将示例客户端应用程序安装到与缓存位于同一 VNET 中的 VM 上，并运行以验证与缓存的连接性。


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>尝试连接到 VNET 中的 Azure Redis 缓存时，为何会收到一项指出远程证书无效的错误？

尝试连接到 VNET 中的 Azure Redis 缓存时，会看到类似于以下内容的证书验证错误：

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

这可能是因为你在通过 IP 地址来连接主机。 建议使用主机名。 换而言之，请使用以下方法：     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

避免使用类似于以下连接字符串的 IP 地址：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

如果无法解析 DNS 名称，某些客户端库包括了 `sslHost`（这是由 StackExchange.Redis 客户端提供的）之类的配置选项。 这允许你替代用于证书验证的主机名。 例如：

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>是否可以对标准或基本缓存使用 VNet？
只能对高级缓存使用 VNet。

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>为什么在某些子网中创建 Azure Redis 缓存会失败，而在其他子网中不会失败？
如果要将 Azure Redis 缓存部署到资源管理器 VNet，缓存必须位于不包含其他资源类型的专用子网中。 如果尝试将 Azure Redis 缓存部署到包含其他资源的资源管理器 VNet 子网，部署会失败。 必须先删除该子网中的现有资源，然后才能创建新的 Azure Redis 缓存。

只要有足够的可用 IP 地址，就可以将多种类型的资源部署到经典 VNet。

### <a name="what-are-the-subnet-address-space-requirements"></a>子网地址空间有哪些要求？
Azure 会保留每个子网中的某些 IP 地址，但是这些地址不能使用。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure VNET 基础结构使用的 IP 地址外，子网中的每个 Redis 实例还会针对负载均衡器使用每分片两个 IP 地址和一个附加 IP 地址。 非群集缓存视为具有一个分片。

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>在 VNET 中托管缓存时，是否可以使用所有缓存功能？
如果缓存是 VNET 的一部分，则只有 VNET 中的客户端可以访问缓存。 因此，以下缓存管理功能目前不起作用。

* Redis 控制台 - 由于 Redis 控制台在本地浏览器中运行（这在 VNET 的外部），因此它无法连接到缓存。


## <a name="use-expressroute-with-azure-cache-for-redis"></a>将 ExpressRoute 与 Azure Redis 缓存配合使用

客户可以将 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 线路连接到虚拟网络基础结构，从而将其本地网络扩展到 Azure。 

默认情况下，新创建的 ExpressRoute 线路不会在 VNET 上执行强制隧道（默认路由播发，0.0.0.0/0）。 因此，出站 Internet 连接可以直接来自 VNET，而客户端应用程序能够连接到其他 Azure 终结点（包括 Azure Redis 缓存）。

但是，常见的客户配置是使用强制隧道（播发默认路由），以强制出站 Internet 流量改为流向本地。 如果接下来出站流量在本地遭到阻止，此流量将断开与 Azure Redis 缓存的连接，这样 Azure Redis 缓存实例将无法与其依赖项通信。

解决方法是在包含 Azure Redis 缓存的子网上定义一个（或多个）用户定义的路由 (UDR)。 UDR 定义了要遵循的子网特定路由，而不是默认路由。

如果可能，建议使用以下配置：

* ExpressRoute 配置播发 0.0.0.0/0 并默认使用强制隧道将所有输出流量发送到本地。
* 已应用到包含 Azure Redis 缓存的子网的 UDR 使用公共 Internet 的 TCP/IP 流量工作路由来定义 0.0.0.0/0；例如，可以将下一跃点类型设置为“Internet”。

这些步骤的组合效应是子网级 UDR 优先于 ExpressRoute 强制隧道，因此可确保来自 Azure Redis 缓存的出站 Internet 访问。

由于性能原因，从本地应用程序使用 ExpressRoute 连接到 Azure Redis 缓存实例不是典型使用方案（为了获得最佳性能，Azure Redis 缓存客户端应与 Azure Redis 缓存位于同一区域中）。

>[!IMPORTANT] 
>UDR 中定义的路由**必须**足够明确，以便优先于 ExpressRoute 配置所播发的任何路由。 以下示例使用广泛 0.0.0.0/0 地址范围，因此使用更明确的地址范围，有可能意外地被路由播发重写。

>[!WARNING]  
>**从公共对等路径到专用对等路径未正确交叉播发路由**的 ExpressRoute 配置不支持 Azure Redis 缓存。 已配置公共对等互连的 ExpressRoute 配置会收到来自 Microsoft 的大量 Microsoft Azure IP 地址范围的路由播发。 如果这些地址范围在专用对等路径上未正确交叉播发，则结果是来自 Azure Redis 缓存实例子网的所有出站网络数据包都不会正确地使用强制隧道发送到客户的本地网络基础结构。 此网络流会破坏 Azure Redis 缓存。 此问题的解决方法是停止从公共对等路径到专用对等路径的交叉播发路由。


有关用户定义路由的背景信息，请参阅此[概述](../virtual-network/virtual-networks-udr-overview.md)。

有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 技术概述](../expressroute/expressroute-introduction.md)。

## <a name="next-steps"></a>后续步骤
了解如何使用更多的高级版缓存功能。

* [Azure Redis 缓存高级层简介](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

