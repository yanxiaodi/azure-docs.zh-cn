---
title: 将 Azure API 管理服务部署到多个 Azure 区域 | Microsoft 文档
description: 了解如何将 Azure API 管理服务实例部署到多个 Azure 区域。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: 7cd0533dcbc9b367fa9a1e138b1aa1257989a3d7
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072430"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>如何将 Azure API 管理服务实例部署到多个 Azure 区域

Azure API 管理支持多区域部署, 该部署可使 API 发布者在任意数量的受支持的 Azure 区域之间分发单个 Azure API 管理服务。 多区域功能可帮助减少地理上分散的 API 使用者发现的请求延迟, 并提高在一个区域脱机时的服务可用性。

新的 Azure API 管理服务最初仅包含单个 Azure 区域 (主要区域) 中的一个[单元][unit]。 可以向主要区域或次要区域添加其他区域。 API 管理网关组件将部署到每个选定的主要区域和次要区域。 传入的 API 请求会自动定向到最近的区域。 如果某个区域处于脱机状态, 则 API 请求会自动路由到下一个最近的网关。

> [!NOTE]
> 仅 API 管理的网关组件部署到所有区域。 仅在主要区域中托管服务管理组件和开发人员门户。 因此, 在主要区域发生中断时, 访问开发人员门户和更改配置 (例如, 添加 Api、应用策略) 的能力会受到影响, 直到主要区域恢复联机状态。 当主要区域脱机可用时, 辅助区域将继续使用可用的最新配置为 API 通信提供服务。

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>将 API 管理服务实例部署到新区域

> [!NOTE]
> 如果尚未创建 API 管理服务实例，请参阅[创建 API 管理服务实例][create an api management service instance]。

在 Azure 门户中，导航到 API 管理服务实例的“规模和定价”页。

![“缩放”选项卡][api-management-scale-service]

若要部署到新的区域，请单击工具栏中的“+ 添加区域”。

![添加区域][api-management-add-region]

从下拉列表中选择位置，并通过滑块为其设置单位数。

![指定单位][api-management-select-location-units]

单击“添加”将选择放置在“位置”表中。

重复此过程，直到配置所有位置，并单击工具栏中的“保存”，启动部署过程。

## <a name="remove-region"> </a>从位置中删除 API 管理服务实例

在 Azure 门户中，导航到 API 管理服务实例的“规模和定价”页。

![“缩放”选项卡][api-management-scale-service]

若要删除位置，请使用表右端的“...”按钮打开上下文菜单。 选择“删除”选项。

确认删除，并单击“保存”应用所做的更改。

## <a name="route-backend"> </a>将 API 调用路由到区域后端服务

Azure API 管理只有一个后端服务 URL。 即使不同的区域中存在 Azure API 管理实例，API 网关也仍会将请求转发到只在一个区域中部署的同一后端服务。 在这种情况下，只有特定于该请求的区域中 Azure API 管理缓存的响应才能提升性能，但访问全球范围的后端时仍可能导致较高的延迟。

若要充分利用系统的地理分布性，应在 Azure API 管理实例所在的同一区域中部署后端服务。 然后，可以使用策略和 `@(context.Deployment.Region)` 属性将流量路由到后端的本地实例。

1. 导航到 Azure API 管理实例，然后在左侧菜单中单击“API”。
2. 选择所需的 API。
3. 在“入站处理”中的箭头式下拉列表内单击“代码编辑器”。

    ![API 代码编辑器](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. 结合使用 `set-backend` 和 `choose` 条件策略，在文件的 `<inbound> </inbound>` 节中构建适当的路由策略。

    例如，以下 XML 文件适用于美国西部和东亚区域：

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> 还可以使用 [Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)来配置后端服务的前端，将 API 调用定向到流量管理器中，然后让流量管理器自动解析路由。

## <a name="custom-routing"> </a>使用 API 管理区域网关的自定义路由

API 管理根据[最低延迟](../traffic-manager/traffic-manager-routing-methods.md#performance)将请求路由到区域网关。 尽管无法在 API 管理中替代此设置，但可以结合自定义路由规则使用自己的流量管理器。

1. 创建自己的 [Azure 流量管理器](https://azure.microsoft.com/services/traffic-manager/)。
1. 如果使用自定义域，请[将它与流量管理器配合使用](../traffic-manager/traffic-manager-point-internet-domain.md)，而不要与 API 管理服务配合使用。
1. [在流量管理器中配置 API 管理区域终结点](../traffic-manager/traffic-manager-manage-endpoints.md)。 区域终结点遵循 `https://<service-name>-<region>-01.regional.azure-api.net` URL 模式，例如 `https://contoso-westus2-01.regional.azure-api.net`。
1. [在流量管理器中配置 API 管理区域状态终结点](../traffic-manager/traffic-manager-monitoring.md)。 区域状态终结点遵循 `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` URL 模式，例如 `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`。
1. 指定流量管理器的[路由方法](../traffic-manager/traffic-manager-routing-methods.md)。

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png
[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png
[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
