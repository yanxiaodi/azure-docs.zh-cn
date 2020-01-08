---
title: 使用 PowerShell 管理 Azure 中的流量管理器
description: 使用包含 Azure 资源管理器的流量管理器 PowerShell
services: traffic-manager
documentationcenter: na
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: allensu
ms.openlocfilehash: a2065ba51b74d7f55464a22df0f55cac4c6defcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071031"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>使用 PowerShell 管理流量管理器

Azure Resource Manager 是 Azure 中的首选服务管理接口。 可以使用基于 Azure 资源管理器的 API 和工具来管理 Azure 流量管理器配置文件。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>资源模型

Azure 流量管理器是使用名为流量管理器配置文件的一系列设置进行配置的。 该配置文件包含 DNS 设置、流量路由设置、终结点监视设置，以及流量路由到的服务终结点列表。

每个流量管理器配置文件由“TrafficManagerProfiles”类型的资源表示。 在 REST API 级别，每个配置文件的 URI 如下：

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>设置 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

这些说明使用 Microsoft Azure PowerShell。 下文介绍了如何安装和配置 Azure PowerShell。

* [如何安装和配置 Azure PowerShell](/powershell/azure/overview)

本文中的示例假设已有一个资源组。 可以使用以下命令创建资源组：

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure 资源管理器要求所有资源组拥有一个位置。 此位置将用作在该资源组中创建的资源的默认值。 但是，由于流量管理器配置文件所有资源都是全局性而不是区域性的，因此，所选的资源组位置不会影响 Azure 流量管理器。

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

若要创建流量管理器配置文件，请使用 `New-AzTrafficManagerProfile` cmdlet：

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

下表描述了参数：

| 参数 | 描述 |
| --- | --- |
| Name |流量管理器配置文件资源的资源名称。 同一资源组中的配置文件必须具有唯一的名称。 此名称不同于用于 DNS 查询的 DNS 名称。 |
| ResourceGroupName |包含配置资源的资源组的名称。 |
| TrafficRoutingMethod |指定用于确定在响应 DNS 查询时返回的终结点的流量路由方法。 可能的值为“Performance”、“Weighted”或“Priority”。 |
| RelativeDnsName |指定此流量管理器配置文件提供的 DNS 名称中的主机名部分。 将此值与 Azure 流量管理器使用的 DNS 域名相结合，可以构成配置文件的完全限定域名 (FQDN)。 例如，设置“contoso”值将成为“contoso.trafficmanager.net”。 |
| TTL |指定 DNS 生存时间 (TTL)，以秒为单位。 此 TTL 通知本地 DNS 解析器和 DNS 客户端缓存此流量管理器配置文件的 DNS 响应的时长。 |
| MonitorProtocol |指定用于监视终结点运行状况的协议。 可能的值为“HTTP”和“HTTPS”。 |
| MonitorPort |指定用于监视终结点运行状况的 TCP 端口。 |
| MonitorPath |指定用于探测终结点运行状况的终结点域名的相对路径。 |

该 cmdlet 会在 Azure 中创建流量管理器配置文件，并将相应的配置文件对象返回至 PowerShell。 此时，配置文件不包含终结点。 有关将终结点添加到流量管理器配置文件的详细信息，请参阅“添加流量管理器终结点”。

## <a name="get-a-traffic-manager-profile"></a>获取流量管理器配置文件

若要检索现有的流量管理器配置文件对象，请使用 `Get-AzTrafficManagerProfle` cmdlet：

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

此 cmdlet 将返回流量管理器配置文件对象。

## <a name="update-a-traffic-manager-profile"></a>更新流量管理器配置文件

修改流量管理器配置文件遵循 3 步流程：

1. 使用 `Get-AzTrafficManagerProfile` 检索配置文件，或使用 `New-AzTrafficManagerProfile` 返回的配置文件。
2. 修改配置文件。 可以添加或删除终结点，也可以更改终结点或配置文件参数。 这些更改属于脱机操作。 只是更改了内存中代表配置文件的本地对象。
3. 使用 `Set-AzTrafficManagerProfile` cmdlet 提交更改。

所有的配置文件属性都可更改，配置文件的 RelativeDnsName 除外。 若要更改 RelativeDnsName，必须删除配置文件和带有新名称的新配置文件。

下面的示例演示如何更改配置文件的 TTL：

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

有三种类型的流量管理器终结点：

1. **Azure 终结点**是托管在 Azure 中的服务
2. **外部终结点**是托管在 Azure 外部的服务
3. **嵌套式终结点**用于构造流量管理器配置文件的嵌套层次结构。 嵌套式终结点支持对复杂应用程序进行高级流量路由配置。

在所有三种情况下，可以通过两种方式来添加终结点：

1. 使用前面所述的 3 步流程。 此方法的优点是可以通过一次更新更改多个终结点。
2. 使用 New-AzTrafficManagerEndpoint cmdlet。 此 cmdlet 可在一个操作中将终结点添加到现有的流量管理器配置文件中。

## <a name="adding-azure-endpoints"></a>添加 Azure 终结点

Azure 终结点会引用托管在 Azure 中的服务。 支持 2 种类型的 Azure 终结点：

1. Azure 应用服务
2. Azure PublicIpAddress 资源（可以附加到负载均衡器或虚拟机 NIC）。 必须为 publicIpAddress 指定 DNS 名称，才能在流量管理器中使用它。

在每种情况下：

* 服务是使用 `Add-AzTrafficManagerEndpointConfig` 或 `New-AzTrafficManagerEndpoint` 的“targetResourceId”参数指定。
* “Target”和“EndpointLocation”由 TargetResourceId 表明。
* 指定“加权”是可选项。 仅当配置文件被配置为使用“加权”流量路由方法时，才使用加权。 否则会忽视加权。 如果要指定，值必须是介于 1 和 1000 之间的数字。 默认值为“1”。
* 指定“优先级”是可选项。 仅当配置文件被配置为使用“优先级”流量路由方法时，才使用优先级。 否则会忽视优先级。 有效值为 1 到 1000 之间的值，值越小指示优先级越高。 如果为一个终结点指定了该值，则必须为所有终结点指定该值。 如果省略，则会按排列终结点的顺序从“1”开始应用默认值。

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>示例 1：使用 `Add-AzTrafficManagerEndpointConfig` 添加应用服务终结点

在此示例中，创建了一个流量管理器配置文件，并使用 `Add-AzTrafficManagerEndpointConfig` cmdlet 添加了两个应用服务终结点。

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>示例 2：使用 `New-AzTrafficManagerEndpoint` 添加 publicIpAddress 终结点

在此示例中，公共 IP 地址资源添加到了流量管理器配置文件中。 公共 IP 地址必须配置了 DNS 名称，并且可以绑定到 VM 的 NIC 或者绑定到负载均衡器。

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>添加外部终结点

流量管理器使用外部终结点将流量定向到在 Azure 外部承载的服务。 与 Azure 终结点一样，可以使用 `Add-AzTrafficManagerEndpointConfig`（后跟 `Set-AzTrafficManagerProfile`）或 `New-AzTrafficManagerEndpoint` 添加外部终结点。

指定外部终结点时：

* 必须使用“Target'”参数指定终结点域名
* 如果使用“性能”流量路由方法，则需要“EndpointLocation”。 否则其则是可选项。 此值必须是[有效的 Azure 区域名称](https://azure.microsoft.com/regions/)。
* “加权”和“优先级”是可选项。

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>示例 1：使用 `Add-AzTrafficManagerEndpointConfig` 和 `Set-AzTrafficManagerProfile` 添加外部终结点

在此示例中，我们创建了一个流量管理器配置文件，添加了两个外部终结点，并提交了所做的更改。

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>示例 2：使用 `New-AzTrafficManagerEndpoint` 添加外部终结点

在此示例中，我们向已有配置文件添加了一个外部终结点。 该配置文件由配置文件和资源组名称指定。

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>添加“嵌套式”终结点

每个流量管理器配置文件都会指定一个流量路由方法。 但是，有些方案要求的流量路由复杂程度高于单个流量管理器配置文件所能提供的路由。 可以嵌套流量管理器配置文件以结合一种以上的流量路由方法的优势。 嵌套式配置文件允许覆盖默认的流量管理器行为以支持更大更为复杂的应用程序部署。 有关更多详细示例，请参阅[嵌套式流量管理器配置文件](traffic-manager-nested-profiles.md)。

在父配置文件中使用特定的终结点类型“NestedEndpoints”配置嵌套式终结点。 指定嵌套式终结点时：

* 必须使用“targetResourceId”参数指定终结点
* 如果使用了“'Performance”流量路由方法，则需要获取“EndpointLocation”。 否则其则是可选项。 此值必须是[有效的 Azure 区域名称](https://azure.microsoft.com/regions/)。
* 对于 Azure 终结点，“加权”和“优先级”是可选项。
* “MinChildEndpoints”参数是可选项。 默认值为“1”。 如果可用终结点数低于此阈值，则父配置文件会将此子配置文件视为“已降级”，并将流量转移到父配置文件中的其他终结点。

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>示例 1：使用 `Add-AzTrafficManagerEndpointConfig` 和 `Set-AzTrafficManagerProfile` 添加嵌套式终结点

在此示例中，我们将创建新的流量管理器子配置文件和父配置文件，将子配置文件添加为父配置文件中的嵌套式终结点，并提交所做的更改。

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

在本示例中为方便起见，我们未将任何其他终结点添加到子或父配置文件。

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>示例 2：使用 `New-AzTrafficManagerEndpoint` 添加嵌套式终结点

在此示例中，我们将现有子配置文件作为嵌套式终结点添加到了现有父配置文件。 该配置文件由配置文件和资源组名称指定。

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>从其他订阅添加终结点

流量管理器可以使用不同订阅中的终结点。 需要使用要添加的终结点切换到订阅，以检索所需的流量管理器输入。 然后，您需要切换到这些订阅与流量管理器配置文件，并向其添加终结点。 下面的示例演示如何使用公共 IP 地址执行此操作。

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>更新流量管理器终结点

有两种方法可以更新现有的流量管理器终结点：

1. 使用 `Get-AzTrafficManagerProfile` 获取流量管理器配置文件，更新配置文件中的终结点属性，并使用 `Set-AzTrafficManagerProfile` 提交更改。 此方法的优势在于能够通过单个操作更新多个终结点。
2. 使用 `Get-AzTrafficManagerEndpoint` 获取流量管理器终结点，更新终结点属性，并使用 `Set-AzTrafficManagerEndpoint` 提交所做的更改。 此方法更简单，因为不需要在配置文件的终结点数组中进行索引操作。

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>示例 1：使用 `Get-AzTrafficManagerProfile` 和 `Set-AzTrafficManagerProfile` 更新终结点

在此示例中，我们将修改现有配置文件中两个终结点上的优先级。

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>示例 2：使用 `Get-AzTrafficManagerEndpoint` 和 `Set-AzTrafficManagerEndpoint` 更新终结点

在此示例中，我们将修改现有配置文件中单个终结点的权重。

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>启用和禁用终结点和配置文件

可以通过流量管理器启用和禁用各个终结点，以及启用和禁用整个配置文件。
这些更改可以通过获取/更新/设置终结点或配置文件资源来完成。 为了简化这些常用操作，也可通过专用 cmdlet 来完成它们。

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>示例 1：启用和禁用流量管理器配置文件

若要启用流量管理器配置文件，请使用 `Enable-AzTrafficManagerProfile`。 可以使用配置文件对象指定该配置文件。 配置文件对象可以通过管道或使用“TrafficManagerProfile”参数传递。 在本示例中，我们通过配置文件和资源组名称指定配置文件。

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

若要禁用流量管理器配置文件：

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Disable-AzTrafficManagerProfile cmdlet 提示进行确认。 可以使用“-Force”参数取消该提示。

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>示例 2：启用和禁用流量管理器终结点

若要启用流量管理器终结点，请使用 `Enable-AzTrafficManagerEndpoint`。 有两种方法可以指定终结点

1. 使用通过管道或使用“-TrafficManagerEndpoint”参数传递的 TrafficManagerEndpoint object 对象
2. 使用终结点名称、终结点类型、配置文件名称和资源组名称：

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

同样，若要禁用流量管理器终结点，请执行以下操作：

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

借助 `Disable-AzTrafficManagerProfile``Disable-AzTrafficManagerEndpoint` cmdlet 提示进行确认。 可以使用“-Force”参数取消该提示。

## <a name="delete-a-traffic-manager-endpoint"></a>删除流量管理器终结点

若要单独删除终结点，请使用 `Remove-AzTrafficManagerEndpoint` cmdlet：

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

此 cmdlet 提示进行确认。 可以使用“-Force”参数取消该提示。

## <a name="delete-a-traffic-manager-profile"></a>删除流量管理器配置文件

若要删除流量管理器配置文件，请使用 `Remove-AzTrafficManagerProfile`，同时指定配置文件和资源组名称：

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

此 cmdlet 提示进行确认。 可以使用“-Force”参数取消该提示。

也可以使用配置文件对象指定要删除的配置文件：

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

还可以通过管道执行此序列：

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>后续步骤

[流量管理器监视](traffic-manager-monitoring.md)

[流量管理器性能注意事项](traffic-manager-performance-considerations.md)
