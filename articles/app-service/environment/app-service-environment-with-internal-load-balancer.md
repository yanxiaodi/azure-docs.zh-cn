---
title: 在应用服务环境中创建和使用内部负载均衡器 - Azure | Microsoft Docs
description: 创建 ASE 并将其与 ILB 搭配使用
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 65d62df954dbbfbdd221adb33eccd82f73588fae
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069899"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>搭配应用服务环境使用内部负载均衡器

> [!NOTE] 
> 本文介绍应用服务环境 v1。 应用服务环境有一个较新版本，此版本更易于使用并在更强大的基础结构上运行。 若要深入了解新版本，请先参阅[应用服务环境简介](intro.md)。
>

应用服务环境 (ASE) 功能是 Azure App Service 的一个高级服务选项，可提供多租户戳记中不会提供的增强型配置功能。 ASE 功能实质上是在 Azure 虚拟网络 (VNet) 中部署 Azure 应用服务。 若要更好地了解应用服务环境提供的功能, 请阅读[什么是应用服务环境][WhatisASE]文档。 如果你不知道在 VNet 中操作的优点, 请阅读[Azure 虚拟网络常见问题解答][virtualnetwork]。 

## <a name="overview"></a>概述
可以使用可访问 Internet 的终结点或 VNet 中的 IP 地址部署 ASE。 要将 IP 地址设置为 VNet 地址，需搭配内部负载均衡器 (ILB) 来部署 ASE。 使用 ILB 配置 ASE 时，需提供：

* 用户自己的域或子域。 为方便起见，本文档假设提供的是子域。用户也可以自行配置。 
* 用于 HTTPS 的证书
* 子域的 DNS 管理。 

相对地，用户可以执行以下操作：

* 在可以通过站点到站点或 ExpressRoute VPN 访问的云端，安全地托管 Intranet 应用程序（例如业务线应用程序）
* 在云端托管未在公用 DNS 服务器中列出的应用
* 创建与 Internet 隔离且前端应用可以与之安全集成的后端应用

#### <a name="disabled-functionality"></a>已禁用的功能
使用 ILB ASE 时，有一些操作无法执行。 这些操作包括：

* 使用 IPSSL
* 将 IP 地址分配给特定应用
* 通过门户购买证书并搭配应用使用。 当然也可以直接通过证书颁发机构获取证书并搭配应用使用，但是无法通过 Azure 门户这样做。

## <a name="creating-an-ilb-ase"></a>创建 ILB ASE
创建 ILB ASE 通常与创建 ASE 没有太大差异。 有关创建 ASE 的更深入讨论, 请参阅[如何创建应用服务环境][HowtoCreateASE]。 无论是在 ASE 创建期间创建 VNet 还是选择现有 VNet，创建 ILB ASE 的过程是相同的。 若要创建 ILB ASE，请执行以下操作： 

1. 在 Azure 门户中选择“创建资源”->“Web + 移动”->“应用服务环境”。
2. 选择订阅。
3. 选择或创建资源组。
4. 选择或创建 VNet。
5. 创建子网（如果选择 VNet）。
6. 选择“虚拟网络/位置”->“VNet 配置”，并将“VIP 类型”设置为“内部”。
7. 提供子域名称（此名称是一个子域，用于此 ASE 中创建的应用）。
8. 选择“确定”，然后选择“创建”。

![][1]

在“虚拟网络”窗格中有一个“VNet 配置”选项，用于选择“外部 VIP”或“内部 VIP”。 默认为“外部”。 如果将其设置为“外部”，ASE 会使用可通过 Internet 访问的 VIP。 如果选择“内部”，则会在 VNet 中的 IP 地址上对 ASE 配置 ILB。 

选择“内部”后，无法向 ASE 添加更多 IP 地址，取而代之的是必须提供 ASE 的子域。 在使用外部 VIP 的 ASE 中，ASE 的名称会在子域中用于在该 ASE 中创建的应用。 如果 ASE 的名称为 ***contosotest***，该 ASE 中的应用名称为 ***mytest***，则子域的格式为 ***contosotest.p.azurewebsites.net***，该应用的 URL 为 ***mytest.contosotest.p.azurewebsites.net***。 如果将“VIP 类型”设置为“内部”，ASE 名称不会在 ASE 的子域中使用。 可以显式指定子域。 如果子域为 ***contoso.corp.net***，而用户在该 ASE 中创建的应用名为 ***timereporting***，则该应用的 URL 为 ***timereporting.contoso.corp.net***。

## <a name="apps-in-an-ilb-ase"></a>ILB ASE 中的应用
在 ILB ASE 中创建应用通常与在 ASE 中创建应用相同。 

1. 在 Azure 门户中选择“创建资源”->“Web + 移动”->“Web”、“移动”或“API 应用”。
2. 输入应用的名称。
3. 选择订阅。
4. 选择或创建资源组。
5. 选择或创建应用服务计划 (ASP)。 如果创建新的 ASP，请选择 ASE 作为位置并选择希望在其中创建 ASP 的工作线程池。 创建该 ASP 时，请选择 ASE 作为位置并选择工作线程池。 指定应用的名称时，会看到应用名称下面的子域替换为 ASE 的子域。 
6. 选择“创建”。 如果希望应用显示在仪表板上，请确保选中“固定到仪表板”复选框。 

![][2]

应用名称下面的子域名称会更新，以反映 ASE 的子域。 

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE 创建后验证
ILB ASE 与非 ILB ASE 稍有不同。 如先前所述，除了管理自己的 DNS，还需提供自己的 HTTPS 连接证书。 

创建 ASE 之后，你会注意到子域显示指定的子域，且“设置”菜单中有一个名为“ILB 证书”的新项。 使用自签名证书创建 ASE，使测试 HTTPS 更加轻松。 门户会告知你需要提供自己的用于 HTTPS 的证书，但这是促使你拥有与子域搭配的证书。 

![][3]

如果你只是要进行测试，并不知道如何创建证书，可以使用 IIS MMC 控制台应用程序来创建自签名证书。 创建之后，可以将它导出为 .pfx 文件，并在 ILB 证书 UI 中上传。 访问使用自签名证书保护的站点时，浏览器会发出警告，指出正在访问的站点不安全，因为无法验证证书。 如果想避免出现此警告，则需一个与子域匹配、具有浏览器能够识别的信任链，并且已经正确签名的证书。

![][6]

使用自己的证书试用流并测试对 ASE 的 HTTP 和 HTTPS 访问：

1. 在创建 ASE 后转到 ASE UI（“ASE”->“设置”->“ILB 证书”）。
2. 通过选择证书 pfx 文件来设置 ILB 证书，并提供密码。 此步骤需要一些时间来处理，并且会显示正在进行缩放操作的消息。
3. 获取 ASE 的 ILB 地址（“ASE”->“属性”->“虚拟 IP 地址”）。
4. 创建后，在 ASE 中创建 Web 应用。 
5. 如果该 VNET 中没有 VM，则创建一个（不是在与 ASE 相同的子网中，否则会无法运作）。
6. 设置子域的 DNS。 可以在 DNS 中将通配符与子域结合使用，或者如果想要执行一些简单测试，可编辑 VM 上的主机文件，将 Web 应用名称设置为 VIP IP 地址。 如果 ASE 的子域名称为 .ilbase.com，而 Web 应用名称为 mytestapp，则 mytestapp.ilbase.com 为寻址地址，请在主机文件中进行相应的设置。 （在 Windows 上，主机文件位于 C:\Windows\System32\drivers\etc\)
7. 在该 VM 上使用浏览器并转到 https://mytestapp.ilbase.com （或者任何 Web 应用名称与子域组成的地址）。
8. 在该 VM 上使用浏览器并转到 https://mytestapp.ilbase.com 。 如果使用自签名证书，则必须接受安全性不足的缺点。 

ILB 的 IP 地址在“属性”中列为“虚拟 IP 地址”。

![][4]

## <a name="using-an-ilb-ase"></a>使用 ILB ASE
#### <a name="network-security-groups"></a>网络安全组
可以使用 ILB ASE 针对应用进行网络隔离。 无法通过 Internet 来访问应用，甚至无法通过 Internet 来感知应用。 此方法非常适用于托管 Intranet 站点，例如业务线应用程序。 当需要更进一步地限制访问时，仍然可以使用网络安全组 (NSG) 来控制网络级别的访问。 

如果想使用 NSG 进一步限制访问，则必须确保不会中断 ASE 运行所需的通信。 即使 HTTP/HTTPS 访问只通过 ASE 所使用的 ILB 进行，ASE 仍需依赖 VNet 外部资源。 若要查看仍需要哪些网络访问权限, 请参阅使用 ExpressRoute[控制到应用服务环境的入站流量][ControlInbound]和[应用服务环境的网络配置详细信息][ExpressRoute]。 

若要配置 NSG，必须知道 Azure 所使用的 IP 地址，以管理 ASE。 如果该 IP 地址发出 Internet 请求，它也会成为 ASE 的出站 IP 地址。 ASE 的出站 IP 地址在 ASE 的生命周期中保持静态。 删除并重新创建 ASE 后，将获得新的 IP 地址。 若要查找此 IP 地址，请转到“设置”->“属性”并查找“出站 IP 地址”。 

![][5]

#### <a name="general-ilb-ase-management"></a>常规 ILB ASE 管理
管理 ILB ASE 通常与管理 ASE 大致相同。 必须增加工作线程池来托管更多 ASP 实例，并增加前端服务器来处理增加的 HTTP/HTTPS 流量。 有关管理 ASE 配置的常规信息, 请参阅[配置应用服务环境][ASEConfig]。 

其他管理项为证书管理和 DNS 管理。 创建 ILB ASE 之后，必须获取并上传用于 HTTPS 的证书，并在其到期前进行替换。 Azure 拥有基础域，因此可以使用外部 VIP 提供 ASE 的证书。 ILB ASE 所使用的子域可以是任何内容，因此你必须提供自己的 HTTPS 证书。 

#### <a name="dns-configuration"></a>DNS 配置
使用外部 VIP 时，DNS 由 Azure 管理。 在 ASE 中创建的任何应用都会自动添加到 Azure DNS，这是一个公用 DNS。 在 ILB ASE 中，必须管理自己的 DNS。 针对指定的子域（例如 contoso.corp.net），必须为以下项创建指向 ILB 地址的 DNS A 记录：

    * 
    *.scm ftp publish 


## <a name="getting-started"></a>入门
若要开始应用服务环境, 请参阅[应用服务环境简介][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
