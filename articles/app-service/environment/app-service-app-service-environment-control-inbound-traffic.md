---
title: 控制应用服务环境的入站流量 - Azure
description: 了解如何配置网络安全规则，以控制发往应用服务环境的入站流量。
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: ''
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: c887ae5568bfd0f72f8d90daecd95547ed7b8b7d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070409"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>如何控制应用服务环境的入站流量
## <a name="overview"></a>概述
可以在 Azure 资源管理器虚拟网络 **或**经典部署模型[虚拟网络][virtualnetwork]中创建应用服务环境。  创建应用服务环境时，可以定义新的虚拟网络和新的子网。  或者，可以在预先存在的虚拟网络和预先存在的子网中创建应用服务环境。  2016 年 6 月进行更改以后，也可以将 ASE 部署到使用公用地址范围或 RFC1918 地址空间（即，专用地址）的虚拟网络。  有关创建应用服务环境的详细信息, 请参阅[如何创建应用服务环境][HowToCreateAnAppServiceEnvironment]。

应用服务环境始终必须在子网中创建，由于子网提供网络边界用于锁定上游设备和服务后面的入站流量，因此只接受来自特定上游 IP 地址的 HTTP 和 HTTPS 流量。

使用[网络安全组][NetworkSecurityGroups]控制子网上的入站和出站网络流量。 控制入站流量时，需要在网络安全组中创建网络安全规则，然后将网络安全组分配给包含应用服务环境的子网。

将网络安全组分配给子网后，根据网络安全组中定义的允许和拒绝规则，允许/阻止应用服务环境中应用程序的入站流量。

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>应用服务环境中使用的入站网络端口
使用网络安全组阻止入站网络流量之前，请务必知道应用服务环境所用的必需和可选网络端口集。  意外关闭发往某些端口的流量可能会在应用服务环境中导致功能丢失。

以下是应用服务环境使用的端口列表。 所有端口都是 **TCP**，除非另有明确的说明：

* 454：Azure 基础结构用来通过 SSL 管理和维护应用服务环境的“必需端口”。  不要阻止发往此端口的流量。  此端口始终绑定到 ASE 的公共 VIP。
* 455：Azure 基础结构用来通过 SSL 管理和维护应用服务环境的“必需端口”。  不要阻止发往此端口的流量。  此端口始终绑定到 ASE 的公共 VIP。
* 80：默认端口，用于将入站 HTTP 流量发往应用服务环境的应用服务计划中运行的应用。  在启用 ILB 的 ASE 中，此端口绑定到 ASE 的 ILB 地址。
* 443：默认端口，用于将入站 SSL 流量发往应用服务环境的应用服务计划中运行的应用。  在启用 ILB 的 ASE 中，此端口绑定到 ASE 的 ILB 地址。
* 21：FTP 的控制通道。  如果未使用 FTP，则可以安全地阻止此端口。  在启用 ILB 的 ASE 中，此端口可以绑定到 ASE 的 ILB 地址。
* 990：FTPS 的控制通道。  如果未使用 FTPS，则可以安全地阻止此端口。  在启用 ILB 的 ASE 中，此端口可以绑定到 ASE 的 ILB 地址。
* 10001-10020：FTP 的数据通道。  和控制通道一样，如果未使用 FTP，则可以放心地阻止这些端口。  在启用 ILB 的 ASE 中，此端口可以绑定到 ASE 的 ILB 地址。
* 4016：用于 Visual Studio 2012 的远程调试。  如果未使用该功能，则可以安全地阻止此端口。  在启用 ILB 的 ASE 中，此端口绑定到 ASE 的 ILB 地址。
* 4018：用于 Visual Studio 2013 的远程调试。  如果未使用该功能，则可以安全地阻止此端口。  在启用 ILB 的 ASE 中，此端口绑定到 ASE 的 ILB 地址。
* 4020：用于 Visual Studio 2015 的远程调试。  如果未使用该功能，则可以安全地阻止此端口。  在启用 ILB 的 ASE 中，此端口绑定到 ASE 的 ILB 地址。

## <a name="outbound-connectivity-and-dns-requirements"></a>出站连接和 DNS 要求
若要让应用服务环境正常运行，还需具有对各个终结点的出站访问权限。 [ExpressRoute 的网络配置](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) 一文的“所需的网络连接”部分中提供了 ASE 所用外部终结点的完整列表。

应用服务环境要求为虚拟网络配置有效 DNS 基础结构。  如果 DNS 配置在创建应用服务环境之后因为任何原因而更改，开发人员可以强制应用服务环境选择新的 DNS 配置。  使用位于[Azure 门户][NewPortal]中应用服务环境管理边栏选项卡顶部的 "重启" 图标触发滚动环境重启, 将导致环境选取新的 DNS 配置。

此外，还建议事先在 VNet 上设置任何自定义 DNS 服务器，并创建应用服务环境。  如果在创建应用服务环境时更改虚拟网络的 DNS 配置，会导致应用服务环境创建过程失败。  同样地，如果自定义 DNS 服务器存在于 VPN 网关的另一端，且 DNS 服务器无法连接或无法使用，则应用服务环境创建过程也会失败。

## <a name="creating-a-network-security-group"></a>创建网络安全组
有关网络安全组工作原理的完整详细信息, 请参阅以下[信息][NetworkSecurityGroups]。  以下 Azure 服务管理示例涉及网络安全组的要点，着重讲解如何配置网络安全组并将其应用到包含应用服务环境的子网。

**注意：** 可使用 [Azure 门户](https://portal.azure.com)或通过 Azure PowerShell 以图形方式配置网络安全组。

网络安全组最初创建为与订阅关联的独立实体。 由于网络安全组是在 Azure 区域中创建的，因此请确保在与应用服务环境相同的区域中创建网络安全组。

下面演示了如何创建网络安全组：

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

创建网络安全组后，将一个或多个网络安全规则添加到其中。  由于规则集会随时改变，因此建议对规则优先级使用单独的编号方案，以便陆续插入其他规则。

以下示例显示的规则可显式授予 Azure 基础结构管理和维护应用服务环境所需的管理端口的访问权限。  请注意，所有管理流量都通过 SSL 传送并受客户端证书的保护，因此即使端口已打开，Azure 管理基础结构以外的任何实体也无法访问这些端口。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


阻止对端口 80 和 443 的访问以便“隐藏”上游设备或服务后面的应用服务环境时，必须知道上游 IP 地址。  例如，如果使用 Web 应用程序防火墙 (WAF)，则 WAF 将有自己的 IP 地址，以便在将流量代理到下游应用服务环境时使用。  必须在网络安全规则的 *SourceAddressPrefix* 参数中使用此 IP 地址。

在以下示例中，显式允许来自特定上游 IP 地址的入站流量。  地址 *1.2.3.4* 用作上游 WAF 的 IP 地址占位符。  请更改此值，以便与上游设备或服务使用的地址匹配。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

如果需要 FTP 支持，可以使用以下规则作为模板，以授予对 FTP 控制端口和数据通道端口的访问权限。  由于 FTP 是有状态协议，无法通过传统 HTTP/HTTPS 防火墙或代理设备路由 FTP 流量。  在此情况下，需要将 *SourceAddressPrefix* 设为其他值 - 例如，FTP 客户端运行所在的开发人员或部署计算机的 IP 地址范围。 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

（**注意：** 数据通道端口范围在预览期可能会更改。）

以下规则演示了在使用 Visual Studio 远程调试时，如何授予访问权限。  由于每个支持的 Visual Studio 版本使用不同的端口进行远程调试，因此每个版本有不同的规则。  对于 FTP 访问，远程调试流量可能不会通过传统 WAF 或代理设备正确传送。  可以改为将 *SourceAddressPrefix* 设置成运行 Visual Studio 的开发人员计算机的 IP 地址范围。

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>将网络安全组分配给子网
网络安全组包含拒绝访问所有外部流量的默认安全规则。  将上述网络安全规则与用于阻止入站流量的默认安全规则相结合后，只有来自与*允许*操作关联的源地址范围的流量能够发送到应用服务环境中运行的应用。

在网络安全组中填充安全规则后，需要将该组分配给包含应用服务环境的子网。  分配命令将引用应用服务环境所在的虚拟网络的名称，以及应用服务环境创建所在的子网的名称。  

以下示例显示了要分配给子网和虚拟网络的网络安全组：

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

网络安全组分配成功后（分配是一项长时间运行的操作，可能需要几分钟才能完成），只有符合*允许*规则的入站流量才能成功抵达应用服务环境中的应用。

出于完整性考虑，以下示例演示了如何删除网络安全组，从而取消它与子网之间的关联：

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>有关显式 IP-SSL 的特殊注意事项
如果使用显式 IP-SSL 地址配置应用（仅适用于具有公共 VIP 的 ASE）而不是使用应用服务环境的默认 IP 地址，则 HTTP 和 HTTPS 流量将通过一组非 80 和 443 的端口发往子网。

可以从门户用户界面的应用服务环境的详细信息 UX 边栏选项卡中找到每个 IP-SSL 地址所使用的单个端口对。  选择“所有设置”-->“IP 地址”。  “IP 地址”边栏选项卡显示应用服务环境的所有显式配置的 IP-SSL 地址表，以及用于路由与每个 IP-SSL 地址关联的 HTTP 和 HTTPS 流量的特殊端口对。  在网络安全组中配置规则时，需要针对 DestinationPortRange 参数使用此端口对。

当 ASE 中的应用程序配置为使用 IP-SSL 时，外部客户看不到也无需担心特殊的端口对映射。  发往应用程序的流量正常情况下将流向已配置的 IP-SSL 地址。  在将流量路由到包含 ASE 的子网的最后阶段，会在内部自动发生到特殊端口对的转换。 

## <a name="getting-started"></a>入门
若要开始使用应用服务环境，请参阅 [Introduction to App Service Environment][IntroToAppServiceEnvironment]（应用服务环境简介）

有关从应用服务环境安全地连接到后端资源的详细信息, 请参阅[从应用服务环境安全连接到后端资源][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

