---
title: Azure 中的嵌套式流量管理器配置文件
titlesuffix: Azure Traffic Manager
description: 本文介绍了 Azure 流量管理器的“嵌套式配置文件”功能
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: allensu
ms.openlocfilehash: 8815d852ad9f8a1823e1c21cc2d233409518da33
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333792"
---
# <a name="nested-traffic-manager-profiles"></a>嵌套式流量管理器配置文件

流量管理器包含一系列流量路由方法，可用于控制流量管理器如何选择具体的终结点来从每个最终用户处接收流量。 有关详细信息，请参阅[流量管理器流量路由方法](traffic-manager-routing-methods.md)。

每个流量管理器配置文件都会指定一个流量路由方法。 但是，有些方案要求的流量路由复杂程度高于单个流量管理器配置文件所能提供的路由。 可以嵌套流量管理器配置文件以结合一种以上的流量路由方法的优势。 使用嵌套式配置文件可以重写默认的流量管理器行为，支持更大、更复杂的应用程序部署。

以下示例演示如何在不同的方案中使用嵌套式流量管理器配置文件。

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>示例 1:组合使用“性能”和“加权”流量路由

假设在以下 Azure 区域部署了一个应用程序：美国西部、西欧和东亚。 使用流量管理器的“性能”流量路由方法将流量分发到最靠近用户的区域。

![单个流量管理器配置文件][4]

现在，假设要针对服务的某项更新进行测试，再推广该更新。 想要使用“加权”流量路由方法，以便将少部分流量定向到测试部署。 在西欧设置了测试部署以及现有的生产部署。

无法在单个配置文件中结合使用“加权”和“性能”流量路由方法。 若要支持此方案，可以使用位于西欧的两个终结点和“加权”流量路由方法创建流量管理器配置文件。 接下来，将此“子”配置文件作为终结点添加到“父”配置文件。 父配置文件仍使用“性能”流量路由方法，并且仍包含终结点形式的其他全局部署。

下图演示了此示例：

![嵌套式流量管理器配置文件][2]

在此配置中，通过父配置文件定向的流量可跨区域正常分发。 在西欧，嵌套式配置文件根据分配的权重将流量分发到生产和测试终结点。

当父配置文件使用“性能”流量路由方法时，必须为每个终结点分配一个位置。 可在配置终结点时分配该位置。 请选择离部署最近的 Azure 区域。 Azure 区域是 Internet 延迟表支持的位置值。 有关详细信息，请参阅[流量管理器“性能”流量路由方法](traffic-manager-routing-methods.md#performance)。

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>示例 2：嵌套式配置文件中的终结点监视

流量管理器会主动监视每个服务终结点的运行状况。 如果终结点不正常，流量管理器会将用户定向到替代终结点，保持服务的可用性。 这种终结点监视和故障转移行为适用于所有流量路由方法。 有关详细信息，请参阅[流量管理器终结点监视](traffic-manager-monitoring.md)。 终结点监视的工作方式不同于嵌套式配置文件。 对于嵌套式配置文件，父配置文件不会针对子配置文件直接执行运行状况检查。 子配置文件的终结点运行状况用于计算子配置文件的总体运行状况。 此运行状况信息会在嵌套式配置文件层次结构中向上传播。 父配置文件使用聚合运行状况信息确定是否将流量定向到子配置文件。 有关嵌套式配置文件运行状况监视的完整详细信息，请参阅[常见问题解答](traffic-manager-FAQs.md#traffic-manager-nested-profiles)。

回到前面的示例，假设西欧的生产部署发生故障。 默认情况下，“子”配置文件会将所有流量定向到测试部署。 如果测试部署也发生故障，父配置文件将确定子配置文件是否不应接收流量，因为所有子终结点都不正常。 然后，父配置文件将流量分发到其他区域。

![嵌套式配置文件故障转移（默认行为）][3]

可能对这种流程感到满意。 或者，可能担心西欧的所有流量（而不是有限数量的流量）现在都会转到测试部署。 不管测试部署的运行状况如何，都希望西欧的生产部署发生故障时，能够故障转移到其他区域。 要启用这种故障转移，可以在将子配置文件配置为终结点时，在父配置文件中指定“MinChildEndpoints”参数。 该参数确定子配置文件中可用终结点的最小数目。 默认值为“1”。 在本方案中，可将 MinChildEndpoints 值设置为 2。 如果低于此阈值，父配置文件会将整个子配置文件视为不可用，并将流量定向到其他终结点。

下图演示了此配置：

![嵌套式配置文件故障转移，“MinChildEndpoints”设置为 2][4]

> [!NOTE]
> “优先级”流量路由方法将所有流量分发到单个终结点。 因此，为子配置文件设置除“1”以外的 MinChildEndpoints 值几乎没有作用。

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>示例 3：“性能”流量路由中的优先故障转移区域

“性能”流量路由方法的默认行为是，当终结点位于不同地理位置时，将依据最低网络延迟将最终用户路由到“最近”的终结点。

但是，假设希望将西欧的流量故障转移到美国西部，仅当这两个终结点都不可用时才将流量定向到其他区域。 可以使用包含“优先级”流量路由方法的子配置文件创建此解决方案。

![首选故障转移的“性能”流量路由][6]

由于西欧终结点的优先级高于美国西部终结点，因此当两个终结点都处于联机状态时，所有流量将发送到西欧终结点。 如果西欧发生故障，则会将其流量定向到美国西部。 如果使用嵌套式配置文件，则仅当西欧和美国西部都发生故障时，流量才定向到东亚。

可以针对所有区域重复此模式。 将父配置文件中的所有三个终结点替换为三个子配置文件，每个子配置文件提供故障转移优先顺序。

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>示例 4：控制同一区域中多个终结点之间的“性能”流量路由

假设在配置文件中使用“性能”流量路由方法时，该配置文件在特定区域有多个终结点。 默认情况下，定向到该区域的流量会平均分布到该区域的所有可用终结点。

![“性能”流量路由区域内流量分布（默认行为）][7]

无需在西欧添加多个终结点，这些终结点会包含在单独的子配置文件中。 子配置文件将作为西欧的唯一终结点添加到父配置文件。 子配置文件中的设置可以通过在西欧启用基于优先级或加权的流量路由，控制西欧的流量分布。

![“性能”流量路由，自定义区域内流量分布][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>示例 5：基于终结点的监视设置

假设希望使用流量管理器来顺利地将流量从旧的本地网站迁移到基于云的新版网站（托管在 Azure 中）。 对于旧站点，想要使用主页 URI 监视站点运行状况。 但对于基于云的新版站点，你要实现一个包含附加检查的自定义监视页面（路径为“/monitor.aspx”）。

![流量管理器终结点监视（默认行为）][9]

流量管理器配置文件中的监视设置将应用到单个配置文件中的所有终结点。 使用嵌套式配置文件时，可为每个站点使用一个不同的子配置文件来定义不同的监视设置。

![按终结点进行设置的流量管理器终结点监视][10]

## <a name="faqs"></a>常见问题

* [如何实现配置嵌套配置文件？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoint-monitoring)

* [流量管理器支持多少层嵌套？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-layers-of-nesting-does-traffic-manger-support)

* [能否在同一流量管理器配置文件中将其他终结点类型与嵌套子配置文件混合在一起？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [如何将计费模型应用于嵌套式配置文件？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-billing-model-apply-for-nested-profiles)

* [嵌套式配置文件是否会影响性能？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-performance-impact-for-nested-profiles)

* [流量管理器如何在父配置文件中计算嵌套终结点的运行状况？](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>后续步骤

深入了解[流量管理器配置文件](traffic-manager-overview.md)

了解如何[创建流量管理器配置文件](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png
