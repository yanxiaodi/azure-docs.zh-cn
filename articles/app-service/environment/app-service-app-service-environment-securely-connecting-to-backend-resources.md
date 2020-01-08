---
title: 从应用服务环境安全地连接到后端资源 - Azure
description: 了解如何从应用服务环境安全连接到后端资源。
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: adb7c246a9f8c8d202d45b58f4d22eeb8d51a773
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069957"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>从应用服务环境安全地连接到后端资源
## <a name="overview"></a>概述
由于应用服务环境始终在 Azure 资源管理器虚拟网络**或**经典部署模型[虚拟网络][virtualnetwork]中创建, 因此, 从应用服务环境到其他后端资源的出站连接可以独占方式通过虚拟网络进行流式传输。  随着 2016 年 6 月的最新更改，可以将 ASE 部署到使用公用地址范围或 RFC1918 地址空间（即，专用地址）的虚拟网络中。  

例如，SQL Server 可能在已锁定端口 1433 的虚拟机群集上运行。  此终结点可能已纳入 ACL，只允许从相同虚拟网络上的其他资源进行访问。  

另一个示例是, 敏感终结点可能在本地运行, 并通过[站点到站点][SiteToSite]或[azure ExpressRoute][ExpressRoute]连接连接到 Azure。  因此，只有虚拟网络中已连接到站点到站点或 ExpressRoute 隧道的资源能够访问本地终结点。

在上述这些方案中，在应用服务环境上运行的应用能够安全地连接到各种服务器和资源。  从应用服务环境中运行的应用程序发往相同虚拟网络中专用终结点（或连接到相同的虚拟网络）的出站流量，只通过虚拟网络发送。  发往专用终结点的出站流量不通过公共 Internet 发送。

在从应用服务环境发往虚拟网络中终结点的出站流量方面，有一点需要注意。  应用服务环境无法访问与应用服务境处于**同一**子网中的虚拟机终结点。  只要将应用服务环境部署在供应用服务环境独占使用的子网内，通常就不会出现任何问题。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>出站连接和 DNS 要求
若要让应用服务环境正常运行，需具有对各个终结点的出站访问权限。 [Network Configuration for ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)（ExpressRoute 的网络配置）一文的“所需的网络连接”部分中提供了 ASE 所用外部终结点的完整列表。

应用服务环境要求为虚拟网络配置有效 DNS 基础结构。  如果 DNS 配置在创建应用服务环境之后因为任何原因而更改，开发人员可以强制应用服务环境选择新的 DNS 配置。  使用位于门户中“应用服务环境管理”边栏选项卡顶部的“重新启动”图标触发轮流环境重新启动，会导致环境选择新的 DNS 配置。

此外，还建议事先在 VNet 上设置任何自定义 DNS 服务器，并创建应用服务环境。  如果在创建应用服务环境时更改虚拟网络的 DNS 配置，会导致应用服务环境创建过程失败。  同样地，如果自定义 DNS 服务器存在于 VPN 网关的另一端，且 DNS 服务器无法连接或无法使用，则应用服务环境创建过程也会失败。

## <a name="connecting-to-a-sql-server"></a>连接到 SQL Server
常见的 SQL Server 配置包含一个侦听端口 1433 的终结点：

![SQL Server 终结点][SqlServerEndpoint]

有两种方法可限制发往此终结点的流量：

* [网络访问控制列表][NetworkAccessControlLists](网络 Acl)
* [网络安全组][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>使用网络 ACL 限制访问
使用网络访问控制列表可以保护端口 1433。  以下示例将源自虚拟网络内部的客户端地址列入允许列表，并阻止对所有其他客户端的访问。

![网络访问控制列表示例][NetworkAccessControlListExample]

在与 SQL Server 相同虚拟网络的应用服务环境中运行的所有应用程序，都能够使用 SQL Server 虚拟机的 **VNet 内部** IP 地址连接到 SQL Server 实例。  

以下连接字符串示例使用其专用 IP 地址引用 SQL Server。

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

尽管虚拟机还有公共终结点，但使用公共 IP 地址的连接尝试将由于网络 ACL 而遭到拒绝。 

## <a name="restricting-access-with-a-network-security-group"></a>使用网络安全组限制访问
另一种保护访问安全的方法是使用网络安全组。  网络安全组可以应用到单个虚拟机，或应用到包含虚拟机的子网。

首先需要创建网络安全组：

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

使用网络安全组来限制仅允许访问 VNet 内部流量的操作十分简单。  网络安全组中的默认规则只允许从相同虚拟网络中的其他网络客户端访问。

因此锁定 SQL Server 的访问权限，就如同将网络安全组及其默认规则应用到运行 SQL Server 的虚拟机或包含虚拟机的子网一样简单。

以下示例将网络安全组应用到包含的子网：

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

最终结果是一组可阻止外部访问，同时允许 VNet 内部访问的安全规则：

![默认网络安全规则][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>入门
若要开始使用应用服务环境，请参阅 [Introduction to App Service Environment][IntroToAppServiceEnvironment]（应用服务环境简介）

有关控制到应用服务环境的入站流量的详细信息, 请参阅[控制到应用服务环境的入站流量][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
