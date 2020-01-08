---
title: 如何创建应用服务环境 v1 - Azure
description: 应用服务环境 v1 的创建流描述
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 017c79ec1341c85f3bd08393dd5553f90a2f6cef
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069747"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>如何创建应用服务环境 v1 

> [!NOTE]
> 本文介绍应用服务环境 v1。 应用服务环境有一个较新版本，此版本更易于使用并在更强大的基础结构上运行。 若要深入了解新版本，请先参阅[应用服务环境简介](intro.md)。
> 

### <a name="overview"></a>概述
应用服务环境 (ASE) 是 Azure App Service 的一个高级服务选项，可提供多租户戳记中不会提供的增强型配置功能。 ASE 功能实质上是将 Azure 应用服务部署到客户的虚拟网络。 若要更好地了解应用服务环境提供的功能, 请阅读[什么是应用服务环境][WhatisASE]文档。

### <a name="before-you-create-your-ase"></a>创建 ASE 前的准备工作
请务必了解不能更改的事项。 创建 ASE 后，不能更改的方面包括：

* Location
* 订阅
* 资源组
* 使用的 VNet
* 使用的子网 
* 子网大小

选择 VNet 及指定子网时，请确保大小足够容纳任何未来增长。 

### <a name="creating-an-app-service-environment-v1"></a>创建应用服务环境 v1
若要创建应用服务环境 v1，可以在 Azure 市场中搜索“应用服务环境 v1”，或者访问“创建资源” -> “Web + 移动” -> “应用服务环境”。 若要创建 ASEv1，请执行以下操作：

1. 提供 ASE 的名称。 为 ASE 指定的名称将用于在 ASE 中创建的应用。 如果 ASE 的名称为 appsvcenvdemo，则子域名称将是：*appsvcenvdemo.p.azurewebsites.net*。 因此，如果创建了名为 *mytestapp* 的应用，则可在 *mytestapp.appsvcenvdemo.p.azurewebsites.net* 中访问它。 不能在 ASE 名称中使用空格。 如果在名称中使用大写字符，域名将为该名称的全小写形式。 如果使用 ILB，则不在子域中使用 ASE 名称，但会在 ASE 创建过程中显式声明该名称。
   
    ![][1]
2. 选择订阅。 用于 ASE 的订阅还将应用于在该 ASE 中创建的所有应用。 不能将 ASE 放入位于其他订阅中的 VNet。
3. 选择或指定新的资源组。 用于 ASE 的资源组必须与用于 VNet 的资源组相同。 如果选择预先存在的 VNet，则 ASE 的资源组选择会更新，以反映 VNet 的资源组。
   
    ![][2]
4. 选择虚拟网络和位置。 可以选择创建新的 VNet，也可以选择预先存在的 VNet。 如果选择新的 VNet，则可以指定名称和位置。 新 VNet 的地址范围为 192.168.250.0/23，并拥有定义为 192.168.250.0/24 的名称为 **default** 的子网。 还可以直接选择预先存在的经典或 Resource Manager VNet。 选择的 VIP 类型确定是否可以从 Internet（外部）直接访问 ASE，或 ASE 是否使用内部负载均衡器 (ILB)。 若要了解详细信息, 请参阅[使用具有应用服务环境的内部负载均衡器][ILBASE]。 如果选择外部 VIP 类型，则可以选择系统创建用于 IPSSL 的外部 IP 地址数量。 如果选择内部，则需要指定 ASE 要使用的子域。 可以将 ASE 部署到使用公用地址范围*或* RFC1918 地址空间（即，专用地址）的虚拟网络。 若要使用具有公用地址范围的虚拟网络，需要提前创建 VNet。 如果选择预先存在的 VNet，需要在 ASE 创建期间创建新的子网。 **不能在门户中使用预先创建的子网。如果使用 Resource Manager 模板创建 ASE，则可以创建具有预先存在的子网的 ASE。** 若要从模板创建 ASE, 请使用此处的信息,[从模板创建应用服务环境][ILBAseTemplate], 并从[模板创建 ILB 应用服务环境][ASEfromTemplate]。

### <a name="details"></a>详细信息
创建的 ASE 具有 2 个前端和 2 个辅助角色。 前端充当 HTTP/HTTPS 终结点，并将流量发送到作为托管应用程序的角色的工作程序。 可以在创建 ASE 后调整数量，甚至可以设置这些资源池的自动缩放规则。 有关如何手动缩放、管理和监视应用服务环境的更多详细信息，请访问：[如何配置应用服务环境][ASEConfig] 

ASE 使用的子网中只能存在该 ASE。 该子网不能用于该 ASE 以外的任何对象

### <a name="after-app-service-environment-v1-creation"></a>创建应用服务环境 v1 后
在创建 ASE 后可以调整：

* 前端的数量（最小值：2）
* 辅助角色的数量（最小值：2）
* 可用于 IP SSL 的 IP 地址数量
* 前端或辅助角色使用的计算资源大小（前端最小大小为 P2）

有关如何手动缩放、管理和监视应用服务环境的更多详细信息，请参阅此文：[如何配置应用服务环境][ASEConfig] 

有关自动缩放的信息，请参阅此处的指南：[如何配置应用服务环境的自动缩放][ASEAutoscale]

存在不可用于自定义（如数据库和存储器）的其他依赖项。 这些是由 Azure 处理且随系统出现的。 系统存储对于整个应用服务环境最多可支持 500 GB，Azure 会根据系统规模的需要来调整数据库。

## <a name="getting-started"></a>入门
若要开始应用服务环境 v1, 请参阅[应用服务环境 V1 简介][WhatisASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
