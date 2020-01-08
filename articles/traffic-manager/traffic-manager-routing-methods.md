---
title: Azure 流量管理器 - 流量路由方法
description: 本文讲解流量管理器使用的各种流量路由方法。
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: allensu
ms.openlocfilehash: 06ce7fb5d18920be6f71821b034dc13061c60032
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051460"
---
# <a name="traffic-manager-routing-methods"></a>流量管理器路由方法

Azure 流量管理器支持使用六种流量路由方法来确定如何将网络流量路由到不同的服务终结点。 对于任何配置文件，流量管理器将关联到它的流量路由方法应用于它收到的每个 DNS 查询。 流量路由方法确定要在 DNS 响应中返回哪个终结点。

流量管理器中提供了以下流量路由方法：

* **[优先级](#priority)：** 如果想要使用主服务终结点来处理所有流量，并提供备份以防主终结点或备份终结点不可用，则可以选择“优先级”。
* **[加权](#weighted)：** 如果想要将流量分配到一组终结点上，不管是平均分配还是根据所定义的权重分配，则均可选择“加权”。
* **[性能](#performance)：** 如果终结点位于不同的地理位置，并且你希望最终用户依据最低网络延迟使用“最近的”终结点，则可以选择“性能”。
* **[地理](#geographic)：** 选择“地理”，以便根据用户的 DNS 查询所源自的地理位置将他们定向到特定终结点（“Azure”、“外部”或“嵌套”）。 这使流量管理器客户可以启用这样的方案：在这些方案中，了解用户的地理区域，并基于该地理区域路由用户很重要。 示例包括遵守数据所有权要求、内容本地化和用户体验，以及测量来自不同区域的流量。
* **[多值](#multivalue)：** 对于只能包含 IPv4/IPv6 地址作为终结点的流量管理器配置文件，请选择“多值”。 当收到针对此配置文件的查询时，将返回所有正常运行的终结点。
* **[子网](#subnet)：** 若要在流量管理器配置文件中将最终用户 IP 地址范围集映射到特定终结点，请选择“子集”。 当收到请求时，将返回为该请求的源 IP 地址映射的终结点。 


所有流量管理器配置文件都包括监视终结点运行状况以及终结点自动故障转移的设置。 有关详细信息，请参阅[流量管理器终结点监视](traffic-manager-monitoring.md)。 一个流量管理器配置文件只能使用一种流量路由方法。 可以随时为配置文件选择其他流量路由方法。 一分钟内即可应用所做的更改，不会导致停机。 可以通过嵌套式流量管理器配置文件来组合使用多种流量路由方法。 使用嵌套可以启用复杂且灵活的流量路由配置，满足更大、更复杂应用程序的需求。 有关详细信息，请参阅[嵌套式流量管理器配置文件](traffic-manager-nested-profiles.md)。

## <a name = "priority"></a>“优先级”流量路由方法

组织往往会提供一个或多个备份服务来防范主服务发生故障，从而确保其服务的可靠性。 Azure 客户可以通过“优先级”流量路由方法来轻松实现此故障转移模式。

![Azure 流量管理器的“优先级”流量路由方法](media/traffic-manager-routing-methods/priority.png)

流量管理器配置文件包含服务终结点的优先顺序列表。 默认情况下，流量管理器将所有流量发送到主终结点（优先级最高）。 如果主终结点不可用，流量管理器会将流量路由到第二个终结点。 如果主终结点和辅助终结点都不可用，流量将转到第三个终结点，依此类推。 终结点的可用性取决于配置的状态（已启用或已禁用）和正在进行的终结点监视。

### <a name="configuring-endpoints"></a>配置终结点

在 Azure 资源管理器中，可以使用每个终结点的“priority”属性显式配置终结点优先级。 此属性是一个介于 1 和 1000 之间的值。 值越小，优先级越高。 终结点不能共享优先级值。 该属性的设置是可选的。 如果省略该属性，会根据终结点顺序使用默认优先级。

## <a name = "weighted"></a>“加权”流量路由方法
使用“加权”流量路由方法可以均匀分布流量，或使用预定义的权重。

![Azure 流量管理器的“加权”流量路由方法](media/traffic-manager-routing-methods/weighted.png)

在“加权”流量路由方法中，需要为流量管理器配置文件中的每个终结点分配一个权重。 该权重是从 1 到 1000 的整数。 此参数是可选的。 如果省略此参数，流量管理器将使用默认权重“1”。 权重越高，优先级就越高。

对于收到的每个 DNS 查询，流量管理器会随机选择一个可用终结点。 选择哪个终结点取决于分配到所有可用终结点的权重。 对所有终结点使用相同的权重会导致均匀分布流量。 对特定的终结点使用较高或较低的权重会导致这些终结点在 DNS 响应中的返回次数较多或较少。

加权方法可以实现一些有用的方案：

* 应用程序逐步升级：分配要路由到新终结点的流量百分比，并随着时间的推移逐步将流量增至 100％。
* 将应用程序迁移到 Azure：创建包含 Azure 终结点和外部终结点的配置文件。 调整终结点的权重，优先选择新终结点。
* 适用于提供更多容量的云爆发：通过将本地部署放在流量管理器配置文件中以将其快速扩展到云中。 当需要在云中获得额外的容量时，可以添加或启用更多终结点，并指定哪部分流量将流向每个终结点。

除了使用 Azure 门户之外，还可以使用 Azure PowerShell、CLI 和 REST API 配置权重。

必须知道，客户端及其用来解析 DNS 名称的递归 DNS 服务器会缓存 DNS 响应。 这种缓存可能会影响到加权流量分布。 如果客户端和递归 DNS 服务器的数目较大，流量分布将按预期工作。 但是，如果客户端或递归 DNS 服务器的数目较小，缓存可能会严重影响流量分布。

常见用例包括：

* 开发和测试环境
* 应用程序间通信
* 目标用户群体较小、共享一个公用递归 DNS 基础结构的应用程序（例如，公司的员工通过代理进行连接）。

这些 DNS 缓存影响常见于所有基于 DNS 的流量路由系统，不只存在于 Azure 流量管理器上。 在某些情况下，显式清除 DNS 缓存可能会解决问题。 在另外一些情况下，可能更适合使用替代性流量路由方法。

## <a name = "performance"></a>性能流量路由方法

在全球两个或更多的位置部署终结点，将流量路由到“最靠近”用户的位置，即可改善许多应用程序的响应能力。 “性能”流量路由方法提供这种能力。

![Azure 流量管理器的“性能”流量路由方法](media/traffic-manager-routing-methods/performance.png)

“最靠近”的终结点不一定是地理距离最近的终结点。 “性能”流量路由方法通过测试网络延迟来确定最靠近的终结点。 流量管理器维护一份 Internet 延迟表，用于跟踪 IP 地址范围与每个 Azure 数据中心之间的往返时间。

流量管理器在 Internet 延迟表中查找传入 DNS 请求的源 IP 地址。 然后，流量管理器在处理该 IP 地址范围的请求时具有最低延迟的 Azure 数据中心内选择一个可用终结点，并在 DNS 响应中返回该终结点。

如[流量管理器工作原理](traffic-manager-how-it-works.md)中所述，流量管理器不会直接从客户端接收 DNS 查询。 DNS 查询来自客户端配置使用的递归 DNS 服务。 因此，用于确定“最靠近”终结点的 IP 地址不是客户端的 IP 地址，而是递归 DNS 服务的 IP 地址。 在实践中，此 IP 地址是客户端的适当代理。


流量管理器定期更新 Internet 延迟表，反映全球 Internet 的变化以及新的 Azure 区域。 但是，由于 Internet 上的负载会实时变化，应用程序性能也会随之变化。 “性能”流量路由不会监视给定服务终结点上的负载。 但是，如果某个终结点变得不可用，则流量管理器不会在 DNS 查询响应中包括该终结点。


需要注意的要点：

* 如果配置文件包含同一 Azure 区域中的多个终结点，流量管理器会在该区域中的可用终结点之间均匀分布流量。 如果想要在某个区域中采用不同的流量分布，可以使用[嵌套式流量管理器配置文件](traffic-manager-nested-profiles.md)。
* 如果最靠近的 Azure 区域中的所有已启用终结点已降级，流量管理器会将流量转移到下一个最靠近的 Azure 区域中的终结点。 如果想要定义首选故障转移顺序，请使用[嵌套式流量管理器配置文件](traffic-manager-nested-profiles.md)。
* 对外部终结点或嵌套式终结点使用“性能”流量路由方法时，需要指定这些终结点的位置。 请选择离部署最近的 Azure 区域。 这些位置是 Internet 延迟表支持的值。
* 选择终结点的算法是确定性的。 同一个客户端重复发出的 DNS 查询会定向到同一个终结点。 通常，客户端在遍历时使用不同的递归 DNS 服务器。 客户端可以路由到不同的终结点。 更新 Internet 延迟表也可能会影响路由。 因此，“性能”流量路由方法不保证始终将客户端路由到同一个终结点。
* 更改 Internet 延迟表时，可能会注意到某些客户端被定向到其他终结点。 这种路由更改基于当前延迟数据，因此更加准确。 进行这些更新很重要，可以确保在 Internet 持续发展的情况下“性能”流量路由的准确性。

## <a name = "geographic"></a>地理流量路由方法

流量管理器配置文件可以配置为使用“地理”路由方法，以便根据用户的 DNS 查询所源自的地理位置将用户定向到特定终结点（“Azure”、“外部”或“嵌套”）。 这使流量管理器客户可以启用这样的方案：在这些方案中，了解用户的地理区域，并基于该地理区域路由用户很重要。 示例包括遵守数据所有权要求、内容本地化和用户体验，以及测量来自不同区域的流量。
为地理路由分配配置文件时，与该配置文件关联的每个终结点都需要有一组分配给它的地理区域。 地理区域的粒度可以是以下级别： 
- 世界 - 任何区域
- 区域分组 - 例如，非洲、中东、澳大利亚/太平洋等。 
- 国家/地区 - 例如，爱尔兰、秘鲁、香港特别行政区等。 
- 省/自治区/直辖市 - 例如，美国加利福尼亚州、澳大利亚昆士兰州、加拿大阿尔伯达省等（注意：只有澳大利亚、加拿大和美国中的省/自治区/直辖市才支持此粒度级别）。

将一个或一组区域分配到某个终结点后，来自这些区域的任何请求仅路由到该终结点。 流量管理器使用 DNS 查询的源 IP 地址来确定用户从中进行查询的区域 - 在大多数情况下，这会是代表用户执行查询的本地 DNS 解析程序的 IP 地址。  

![Azure 流量管理器的“地理”流量路由方法](./media/traffic-manager-routing-methods/geographic.png)

流量管理器读取 DNS 查询的源 IP 地址，并确定它所源自的地理区域。 随后，它会查找并查看是否存在此地理区域所映射到的终结点。 此查找从最低粒度级别（即“省/自治区/直辖市”如果支持的话，否则为“国家/地区”级别）开始，一直向上查找，直到最高级别（即“世界”）。 使用此遍历找到的第一个匹配项将指定为要在查询响应中返回的终结点。 当与“嵌套”类型终结点匹配时，将基于其路由方法返回该子配置文件中的终结点。 以下几点适用于此行为：

- 当路由类型是“地理路由”时，只能将一个地理区域映射到流量管理器配置文件中的一个终结点。 这可确保用户的路由是确定性的，并且客户可以启用需要明确地理边界的方案。
- 如果用户的区域归入两个不同终结点的地理映射下，流量管理器将选择具有最低粒度的终结点，并且不会考虑将请求从该区域路由到其他终结点。 例如，考虑包含两个终结点（终结点 1 和终结点 2）的“地理路由”类型配置文件。 终结点 1 配置为接收来自爱尔兰的流量，终结点 2 配置为接收来自欧洲的流量。 如果请求来自爱尔兰，始终会将它路由到终结点 1。
- 由于一个区域只能映射到一个终结点，因此流量管理器将返回该终结点，而不管该终结点是否处于正常状态。

    >[!IMPORTANT]
    >强烈建议客户使用地理路由方法将其与具有子配置文件（每个子配置文件中至少包含两个终结点）的“嵌套”类型终结点关联。
- 如果找到终结点匹配项，但该终结点处于“已停止”状态，则流量管理器会返回 NODATA 响应。 在这种情况下，将不在地理区域层次结构中的更高位置进一步查找。 当子配置文件处于“已停止”或“已禁用”状态时，此行为也适用于嵌套终结点类型。
- 如果终结点显示“已禁用”状态，则它不会包含在区域匹配过程中。 当终结点处于“已禁用”状态时，此行为也适用于嵌套终结点类型。
- 如果查询来自该配置文件中没有映射的地理区域，流量管理器将返回 NODATA 响应。 因此，强烈建议客户将地理路由用于一个终结点，最好是嵌套类型的终结点，其子配置文件中至少有两个终结点且分配了“世界”区域。 这还可以确保处理任何不会映射到区域的 IP 地址。

如[流量管理器工作原理](traffic-manager-how-it-works.md)中所述，流量管理器不会直接从客户端接收 DNS 查询。 DNS 查询来自客户端配置使用的递归 DNS 服务。 因此，用于确定区域的 IP 地址不是客户端的 IP 地址，而是递归 DNS 服务的 IP 地址。 在实践中，此 IP 地址是客户端的适当代理。

### <a name="faqs"></a>常见问题

* [地理路由有哪些用例？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [如何实现决定是否应使用性能路由方法或地理路由方法？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [用于地理路由的流量管理器支持哪些区域？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [流量管理器如何确定用户从何处进行查询？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [是否保证流量管理器在每种情况下都可以正确确定用户的确切地理位置？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [终结点是否需要与配置用于地理路由的终结点位于同一区域中？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [是否可以将地理区域分配给配置文件中未配置为进行地理路由的终结点？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [为什么我在尝试将现有配置文件的路由方法更改为地理时遇到错误？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [为什么强烈建议客户创建嵌套配置文件而不是启用了地理路由的配置文件中的终结点？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [对于支持此路由类型的 API 版本, 是否存在任何限制？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name = "multivalue"></a>多值流量路由方法
**多值**流量路由方法允许你在单个 DNS 查询响应中获得多个正常运行的终结点。 这使得调用方能够在返回的终结点无法响应的情况下, 使用其他终结点进行客户端重试。 此模式可以提高服务可用性，并降低与新 DNS 查询获取正常运行的终结点相关的延迟。 只有当所有终结点的类型都是“外部”并且指定为 IPv4 或 IPv6 地址时，多值路由方法才有效。 当收到对此配置文件的查询时，会根据可配置的最大返回计数返回所有正常运行的终结点。

### <a name="faqs"></a>常见问题

* [多值路由有哪些用例？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [使用多值路由时返回多少个终结点？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [使用多值路由时, 是否会获得相同的终结点集？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name = "subnet"></a>子网流量路由方法
**子网**流量路由方法允许你将一个最终用户 IP 地址范围集映射到配置文件中的特定终结点。 此后，如果流量管理器收到针对该配置文件的 DNS 查询，则它将检查该请求的源 IP 地址（大多数情况下，这是调用方使用的 DNS 解析程序的传出 IP 地址），确定它映射到哪个终结点，并在查询响应中返回该终结点。 

可以将要映射到终结点的 IP 地址指定为 CIDR 范围（例如 1.2.3.0/24）或地址范围（例如 1.2.3.4-5.6.7.8）。 与某个终结点关联的 IP 范围在该配置文件中必须是唯一的，并且不能与同一配置文件中另一个终结点的 IP 地址集重叠。
如果定义一个没有地址范围的终结点，则该终结点将用作回退终结点并从任何剩余的子网获取流量。 如果未包含回退终结点，则流量管理器会针对任何未定义的范围发送 NODATA 响应。 因此，强烈建议你定义回退终结点，或者确保在终结点上指定所有可能的 IP 范围。

子网路由可以用来为从特定 IP 空间进行连接的用户提供不同的体验。 例如，使用子网路由，客户可以将来自其公司的所有请求路由到一个不同的终结点，他们可以在这里测试其应用的仅限内部版本。 另一种情况是，你可能希望为从特定 ISP 进行连接的用户提供不同的体验（例如，阻止来自给定 ISP 的用户）。

### <a name="faqs"></a>常见问题

* [子网路由有哪些用途？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [流量管理器如何知道最终用户的 IP 地址？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [使用子网路由时如何指定 IP 地址？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [使用子网路由时如何指定回退终结点？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [如果子网路由类型配置文件中禁用了终结点, 会发生什么情况？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>后续步骤

了解如何使用[流量管理器终结点监视](traffic-manager-monitoring.md)开发高可用性应用程序




