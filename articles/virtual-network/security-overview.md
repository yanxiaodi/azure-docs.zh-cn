---
title: Azure 安全组概述
titlesuffix: Azure Virtual Network
description: 了解网络和应用程序安全组。 安全组可以帮助你筛选 Azure 资源之间的网络流量。
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 1d9fc022a0b0d5ba96517b4ed06b4a2576245a26
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886030"
---
# <a name="security-groups"></a>安全组
<a name="network-security-groups"></a>

可以使用网络安全组来筛选 Azure [虚拟网络](virtual-networks-overview.md)中出入 Azure 资源的网络流量。 网络安全组包含[安全规则](#security-rules)，这些规则可允许或拒绝多种 Azure 资源的入站和出站网络流量。 若要了解哪些 Azure 资源可以部署到虚拟网络中并与网络安全组关联，请参阅 [Azure 服务的虚拟网络集成](virtual-network-for-azure-services.md)。 可以为每项规则指定源和目标、端口以及协议。

本文介绍网络安全组概念，目的是让你提高其使用效率。 如果从未创建过网络安全组，可以先完成一个快速[教程](tutorial-filter-network-traffic.md)，获取一些创建经验。 如果已熟悉网络安全组，需要对其进行管理，请参阅[管理网络安全组](manage-network-security-group.md)。 如果有通信问题，需要对网络安全组进行故障排除，请参阅[诊断虚拟机网络流量筛选器问题](diagnose-network-traffic-filter-problem.md)。 可以通过[网络安全组流日志](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)来[分析网络流量](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，这些流量流入和流出的资源组都有关联的网络安全组。

## <a name="security-rules"></a>安全规则

一个网络安全组包含零个或者不超过 Azure 订阅[限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)的任意数量的规则。 每个规则指定以下属性：

|属性  |说明  |
|---------|---------|
|姓名|网络安全组中的唯一名称。|
|Priority | 介于 100 和 4096 之间的数字。 规则按优先顺序进行处理。先处理编号较小的规则，因为编号越小，优先级越高。 一旦流量与某个规则匹配，处理即会停止。 因此，不会处理优先级较低（编号较大）的、其属性与高优先级规则相同的所有规则。|
|源或目标| 可以是任何值，也可以是单个 IP 地址、无类别域际路由 (CIDR) 块（例如 10.0.0.0/24）、[服务标记](#service-tags)或[应用程序安全组](#application-security-groups)。 如果为 Azure 资源指定一个地址，请指定分配给该资源的专用 IP 地址。 在 Azure 针对入站流量将公共 IP 地址转换为专用 IP 地址后，系统会处理网络安全组，然后由 Azure 针对出站流量将专用 IP 地址转换为公共 IP 地址。 详细了解 Azure [IP 地址](virtual-network-ip-addresses-overview-arm.md)。 指定范围、服务标记或应用程序安全组可以减少创建的安全规则数。 在一个规则中指定多个单独的 IP 地址和范围（不能指定多个服务标记或应用程序组）的功能称为[扩充式安全规则](#augmented-security-rules)。 只能在通过资源管理器部署模型创建的网络安全组中创建扩充式安全规则。 在通过经典部署模型创建的网络安全组中，不能指定多个 IP 地址和 IP 地址范围。 详细了解 [Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。|
|Protocol     | TCP、UDP、ICMP 或 Any。|
|Direction| 该规则是应用到入站还是出站流量。|
|端口范围     |可以指定单个端口或端口范围。 例如，可以指定 80 或 10000-10005。 指定范围可以减少创建的安全规则数。 只能在通过资源管理器部署模型创建的网络安全组中创建扩充式安全规则。 在通过经典部署模型创建的网络安全组中，不能在同一个安全规则中指定多个端口或端口范围。   |
|操作     | 允许或拒绝        |

在允许或拒绝流量之前，将使用 5 元组信息（源、源端口、目标、目标端口和协议）按优先级对网络安全组安全规则进行评估。 将为现有连接创建流记录。 是允许还是拒绝通信取决于流记录的连接状态。 流记录允许网络安全组有状态。 例如，如果针对通过端口 80 访问的任何地址指定了出站安全规则，则不需要指定入站安全规则来响应出站流量。 如果通信是从外部发起的，则只需指定入站安全规则。 反之亦然。 如果允许通过某个端口发送入站流量，则不需要指定出站安全规则来响应通过该端口发送的流量。
删除启用了流的安全规则时，现有连接不一定会中断。 当连接停止并且至少几分钟内在任一方向都没有流量流过时，流量流会中断。

在网络安全组中创建的安全规则存在数量限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="augmented-security-rules"></a>扩充式安全规则

扩充式安全规则简化了虚拟网络的安全定义，可让我们以更少的规则定义更大、更复杂的网络安全策略。 可将多个端口和多个显式 IP 地址和范围合并成一个易于理解的安全规则。 可在规则的源、目标和端口字段中使用扩充式规则。 若要简化安全规则定义的维护，可将扩充式安全规则与[服务标记](#service-tags)或[应用程序安全组](#application-security-groups)合并。 可在规则中指定的地址、范围和端口的数量存在限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="service-tags"></a>服务标记

服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。 无法创建自己的服务标记，也无法指定要将哪些 IP 地址包含在标记中。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。 创建安全规则时，可以使用服务标记代替特定的 IP 地址。 

可在[网络安全组规则](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)中使用以下服务标记。 末尾带有星号的服务标记 (即 AzureCloud *) 也可在[Azure 防火墙网络规则](https://docs.microsoft.com/azure/firewall/service-tags)中使用。 

* **ApiManagement**\*（仅限资源管理器）：此标记表示 APIM 专用部署的管理流量地址前缀。 如果指定 ApiManagement 作为值，则会允许或拒绝发往 ApiManagement 的流量。 对于入站/出站安全规则，建议使用此标记。 
* **AppService**\*（仅限资源管理器）：此标记表示 Azure AppService 服务的地址前缀。 如果指定 AppService 作为值，则会允许或拒绝发往 AppService 的流量。 如果仅希望允许访问某个特定[区域](https://azure.microsoft.com/regions)中的 AppService，则可采用以下格式 AppService.[区域名称] 指定该区域。 对于 WebApps 前端的出站安全规则，建议使用此标记。  
* **AppServiceManagement**\*（仅限资源管理器）：此标记表示应用服务环境专用部署的管理流量地址前缀。 如果指定 AppServiceManagement 作为值，则会允许或拒绝发往 AppServiceManagement 的流量。 对于入站/出站安全规则，建议使用此标记。 
* **AzureActiveDirectory**\*（仅限资源管理器）：此标记表示 AzureActiveDirectory 服务的地址前缀。 如果指定 AzureActiveDirectory 作为值，则会允许或拒绝发往 AzureActiveDirectory 的流量。 对于出站安全规则，建议使用此标记。
* **AzureActiveDirectoryDomainServices**\* (仅资源管理器):此标记表示 Azure Active Directory 域服务专用部署的管理流量的地址前缀。 如果为值指定*AzureActiveDirectoryDomainServices* , 则允许或拒绝流量发送到 AzureActiveDirectoryDomainServices。 对于入站/出站安全规则，建议使用此标记。  
* **AzureBackup**\*（仅限资源管理器）：此标记表示 AzureBackup 服务的地址前缀。 如果指定 *AzureBackup* 作为值，则会允许或拒绝发往 AzureBackup 的流量。 此标记依赖于**存储**和**AzureActiveDirectory**标记。 对于出站安全规则，建议使用此标记。 
* **AzureCloud**\* (仅资源管理器):此标记表示 Azure 的 IP 地址空间，包括所有[数据中心公共 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653)。 如果指定 AzureCloud 作为值，则会允许或拒绝发往 Azure 公共 IP 地址的流量。 如果只想允许访问特定[区域](https://azure.microsoft.com/regions)中的 AzureCloud，可以指定以下格式的区域 AzureCloud。[region name]。 对于出站安全规则，建议使用此标记。 
* **AzureConnectors**\*（仅限资源管理器）：此标记表示用于探测/后端连接的逻辑应用连接器的地址前缀。 如果指定 AzureConnectors 作为值，则会允许或拒绝发往 AzureConnectors 的流量。 如果仅希望允许访问某个特定[区域](https://azure.microsoft.com/regions)中的 AzureConnectors，则可采用以下格式 AzureConnectors.[区域名称] 指定该区域。 对于入站安全规则，建议使用此标记。 
* **AzureContainerRegistry**\*（仅限资源管理器）：此标记表示 Azure 容器注册表服务的地址前缀。 如果指定 AzureContainerRegistry 作为值，则会允许或拒绝发往 AzureContainerRegistry 的流量。 如果仅希望允许访问某个特定[区域](https://azure.microsoft.com/regions)中的 AzureContainerRegistry，则可采用以下格式 AzureContainerRegistry.[区域名称] 指定该区域。 对于出站安全规则，建议使用此标记。 
* **AzureCosmosDB**\*（仅限资源管理器）：此标记表示 Azure Cosmos 数据库服务的地址前缀。 如果指定 *AzureCosmosDB* 作为值，则会允许或拒绝发往 AzureCosmosDB 的流量。 如果仅希望允许访问特定[区域](https://azure.microsoft.com/regions)中的 AzureCosmosDB，则可以在以下格式的 AzureCosmosDB.[region name] 中指定区域。 对于出站安全规则，建议使用此标记。 
* **AzureDataLake**\* (仅资源管理器):此标记表示 Azure Data Lake 服务的地址前缀。 如果指定 AzureDataLake 作为值，则会允许或拒绝发往 AzureDataLake 的流量。 对于出站安全规则，建议使用此标记。 
* **AzureKeyVault**\*（仅限资源管理器）：此标记表示 Azure KeyVault 服务的地址前缀。 如果指定 *AzureKeyVault* 作为值，则会允许或拒绝发往 AzureKeyVault 的流量。 如果仅希望允许访问特定[区域](https://azure.microsoft.com/regions)中的 AzureKeyVault，则可以在以下格式的 AzureKeyVault.[region name] 中指定区域。 此标记依赖于 **AzureActiveDirectory** 标记。 对于出站安全规则，建议使用此标记。  
* **AzureLoadBalancer**（资源管理器）（如果是经典部署模型，则为 **AZURE_LOADBALANCER**）：此标记表示 Azure 的基础结构负载均衡器。 此标记将转换为[主机的虚拟 IP 地址](security-overview.md#azure-platform-considerations) (168.63.129.16)，Azure 的运行状况探测源于该 IP。 如果不使用 Azure 负载均衡器，则可替代此规则。
* **AzureMachineLearning**\* (仅资源管理器):此标记表示 AzureMachineLearning 服务的地址前缀。 如果指定 AzureMachineLearning 作为值，则会允许或拒绝发往 AzureMachineLearning 的流量。 对于出站安全规则，建议使用此标记。 
* **AzureMonitor**\*（仅限资源管理器）：此标记表示 Log Analytics、App Insights、AzMon 和自定义指标 (g 终结点) 的地址前缀。 如果指定 AzureMonitor 作为值，则会允许或拒绝发往 AzureMonitor 的流量。 对于 Log Analytics，此标记依赖于 **Storage** 标记。 对于出站安全规则，建议使用此标记。
* **AzurePlatformDNS**(仅资源管理器):此标记表示作为基本基础结构服务的 DNS。 如果为值指定*AzurePlatformDNS* , 则可以禁用 DNS 的默认[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 使用此标记时请务必小心。 建议在使用此标记之前进行测试。 
* **AzurePlatformIMDS**(仅资源管理器):此标记表示 IMDS, 它是一个基本基础结构服务。 如果为值指定*AzurePlatformIMDS* , 则可以禁用 IMDS 的默认[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 使用此标记时请务必小心。 建议在使用此标记之前进行测试。 
* **AzurePlatformLKM**(仅资源管理器):此标记表示 Windows 授权或密钥管理服务。 如果为值指定*AzurePlatformLKM* , 则可以禁用许可的默认[Azure 平台注意事项](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 使用此标记时请务必小心。 建议在使用此标记之前进行测试。 
* **AzureTrafficManager**\*（仅限资源管理器）：此标记表示 Azure 流量管理器探测 IP 地址的 IP 地址空间。 有关流量管理器探测 IP 地址的详细信息，请参阅 [Azure 流量管理器常见问题解答](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 对于入站安全规则，建议使用此标记。  
* **BatchNodeManagement**\*（仅限资源管理器）：此标记表示 Azure Batch 专用部署的管理流量地址前缀。 如果为值指定 *BatchNodeManagement*，则允许或拒绝从 Batch 服务到计算节点的流量。 对于入站/出站安全规则，建议使用此标记。 
* **CognitiveServicesManagement**（仅限资源管理器）：此标记表示认知服务的流量地址前缀。 如果指定*CognitiveServicesManagement* 作为值，则会允许或拒绝发往 CognitiveServicesManagement 的流量。 对于出站安全规则，建议使用此标记。  
* **Dynamics365ForMarketingEmail**(仅资源管理器):此标记表示 Dynamics 365 营销电子邮件服务的地址前缀。 如果为值指定*Dynamics365ForMarketingEmail* , 则允许或拒绝流量发送到 Dynamics365ForMarketingEmail。 如果只想允许访问特定[区域](https://azure.microsoft.com/regions)中的 Dynamics365ForMarketingEmail，可以指定以下格式的区域 Dynamics365ForMarketingEmail。[region name]。
* **EventHub**\*（仅限资源管理器）：此标记表示 Azure EventHub 服务的地址前缀。 如果指定 EventHub 作为值，则会允许或拒绝发往 EventHub 的流量。 如果仅希望允许访问某个特定[区域](https://azure.microsoft.com/regions)中的 EventHub，则可采用以下格式 EventHub.[区域名称] 指定该区域。 对于出站安全规则，建议使用此标记。 
* **GatewayManager**（仅限资源管理器）：此标记表示 VPN/应用网关专用部署的管理流量地址前缀。 如果指定 GatewayManager 作为值，则会允许或拒绝发往 GatewayManager 的流量。 对于入站安全规则，建议使用此标记。 
* **Internet**（资源管理器）（如果是经典部署模型，则为 **INTERNET**）：此标记表示虚拟网络外部的 IP 地址空间，可以通过公共 Internet 进行访问。 地址范围包括 [Azure 拥有的公共 IP 地址空间](https://www.microsoft.com/download/details.aspx?id=41653)。
* **MicrosoftContainerRegistry**\*（仅限资源管理器）：此标记表示 Microsoft 容器注册表服务的地址前缀。 如果指定 MicrosoftContainerRegistry 作为值，则会允许或拒绝发往 MicrosoftContainerRegistry 的流量。 如果仅希望允许访问某个特定[区域](https://azure.microsoft.com/regions)中的 MicrosoftContainerRegistry，则可采用以下格式 MicrosoftContainerRegistry.[区域名称] 指定该区域。 对于出站安全规则，建议使用此标记。 
* **ServiceBus**\*（仅限资源管理器）：此标记表示使用高级服务层的 Azure ServiceBus 服务的地址前缀。 如果指定 ServiceBus 作为值，则会允许或拒绝发往 ServiceBus 的流量。 如果仅希望允许访问某个特定[区域](https://azure.microsoft.com/regions)中的 ServiceBus，则可采用以下格式 ServiceBus.[区域名称] 指定该区域。 对于出站安全规则，建议使用此标记。 
* **ServiceFabric**\*（仅限资源管理器）：此标记表示 ServiceFabric 服务的地址前缀。 如果指定 ServiceFabric 作为值，则会允许或拒绝发往 ServiceFabric 的流量。 对于出站安全规则，建议使用此标记。 
* **Sql**\*（仅限资源管理器）：此标记表示 Azure SQL 数据库、Azure Database for MySQL、Azure Database for PostgreSQL 和 Azure SQL 数据仓库服务的地址前缀。 如果指定 *Sql* 作为值，则会允许或拒绝发往 Sql 的流量。 如果只想允许对特定[区域](https://azure.microsoft.com/regions)中的 Sql 进行访问，则可以使用以下格式的 sql. [region name] 来指定区域。 标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure SQL 数据库服务，但不能表示特定的 SQL 数据库或服务器。 对于出站安全规则，建议使用此标记。 
* **SqlManagement**\*（仅限资源管理器）：此标记表示 SQL 专用部署的管理流量地址前缀。 如果指定 *SqlManagement* 作为值，则会允许或拒绝发往 SqlManagement 的流量。 对于入站/出站安全规则，建议使用此标记。 
* **Storage**\*（仅限资源管理器）：此标记表示 Azure 存储服务的 IP 地址空间。 如果指定 *Storage* 作为值，则会允许或拒绝发往存储的流量。 如果只想允许访问特定[区域](https://azure.microsoft.com/regions)中的存储，则可以指定以下格式存储中的区域。[region name]。 标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure 存储服务，但不能表示特定的 Azure 存储帐户。 对于出站安全规则，建议使用此标记。 
* **VirtualNetwork**（资源管理器）（如果是经典部署模型，则为 **VIRTUAL_NETWORK**）：此标记包括虚拟网络地址空间（为虚拟网络定义的所有 CIDR 范围）、所有连接的本地地址空间、[对等互连](virtual-network-peering-overview.md)虚拟网络或连接到[虚拟网络网关](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的[虚拟网络，](security-overview.md#azure-platform-considerations)[用户定义路由](virtual-networks-udr-overview.md)上使用的主机和地址前缀的虚拟 IP 地址。 请注意，此标记可能包含默认路由。 

> [!NOTE]
> Azure 服务的服务标记表示来自所使用的特定云的地址前缀。 

> [!NOTE]
> 如果为某个服务（例如 Azure 存储或 Azure SQL 数据库）实现了[虚拟网络服务终结点](virtual-network-service-endpoints-overview.md)，Azure 会将[路由](virtual-networks-udr-overview.md#optional-default-routes)添加到该服务的虚拟网络子网。 路由中的地址前缀与相应服务标记的地址前缀或 CIDR 范围相同。

### <a name="service-tags-in-on-premises"></a>本地服务标记  
你可以使用以下 Azure[公共](https://www.microsoft.com/download/details.aspx?id=56519)、[美国政府](https://www.microsoft.com/download/details.aspx?id=57063)、[中国](https://www.microsoft.com/download/details.aspx?id=57062)和[德国](https://www.microsoft.com/download/details.aspx?id=57064)云的每周发布的前缀详细信息下载和集成本地防火墙列表。

也可以使用**服务标记发现 API**（公共预览版）- [REST](https://aka.ms/discoveryapi_rest)、[Azure PowerShell](https://aka.ms/discoveryapi_powershell) 和 [Azure CLI](https://aka.ms/discoveryapi_cli) 以编程方式检索此信息。 

> [!NOTE]
> 以下每周发布 (旧版本) 适用于 Azure[公共](https://www.microsoft.com/en-us/download/details.aspx?id=41653)、[中国](https://www.microsoft.com/en-us/download/details.aspx?id=42064)和[德国](https://www.microsoft.com/en-us/download/details.aspx?id=54770)云将于2020年6月30日弃用。 请开始使用上面所述的更新发布。 

## <a name="default-security-rules"></a>默认安全规则

Azure 在你所创建的每个网络安全组中创建以下默认规则：

### <a name="inbound"></a>入站

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priority|Source|源端口|目标|目标端口|Protocol|访问权限|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|任意|Allow|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priority|Source|源端口|目标|目标端口|Protocol|访问权限|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|任意|Allow|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priority|Source|源端口|目标|目标端口|Protocol|访问权限|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|任意|拒绝|

### <a name="outbound"></a>出站

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priority|Source|源端口| 目标 | 目标端口 | Protocol | 访问权限 |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | 任意 | Allow |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priority|Source|源端口| 目标 | 目标端口 | Protocol | 访问权限 |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | 任意 | Allow |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priority|Source|源端口| 目标 | 目标端口 | Protocol | 访问权限 |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | 任意 | 拒绝 |

在“源”和“目标”列表中，“VirtualNetwork”、“AzureLoadBalancer”和“Internet”是[服务标记](#service-tags)，而不是 IP 地址。 在 "协议" 列中,**任何**包括 TCP、UDP 和 ICMP。 创建规则时, 可以指定 TCP、UDP、ICMP 或任何。 “源”和“目标”列中的“0.0.0.0/0”表示所有地址。 Azure 门户、Azure CLI 或 Powershell 等客户端可以使用 * 或任何字符来表示此表达式。
 
不能删除默认规则，但可以通过创建更高优先级的规则来替代默认规则。

## <a name="application-security-groups"></a>应用程序安全组

使用应用程序安全组可将网络安全性配置为应用程序结构的固有扩展，从而可以基于这些组将虚拟机分组以及定义网络安全策略。 可以大量重复使用安全策略，而无需手动维护显式 IP 地址。 平台会处理显式 IP 地址和多个规则集存在的复杂性，让你专注于业务逻辑。 若要更好地理解应用程序安全组，请考虑以下示例：

![应用程序安全组](./media/security-groups/application-security-groups.png)

在上图中，*NIC1* 和 *NIC2* 是 *AsgWeb* 应用程序安全组的成员。 *NIC3* 是 *AsgLogic* 应用程序安全组的成员。 *NIC4* 是 *AsgDb* 应用程序安全组的成员。 虽然此示例中的每个网络接口只是一个应用程序安全组的成员，但实际上一个网络接口可以是多个应用程序安全组的成员，具体取决于 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。 这些网络接口都没有关联的网络安全组。 *NSG1* 关联到两个子网，包含以下规则：

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

若要让流量从 Internet 流到 Web 服务器，此规则是必需的。 由于来自 Internet 的入站流量被 [DenyAllInbound](#denyallinbound) 默认安全规则拒绝，因此 *AsgLogic* 或 *AsgDb* 应用程序安全组不需更多规则。

|Priority|Source|源端口| 目标 | 目标端口 | Protocol | 访问权限 |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Allow |

### <a name="deny-database-all"></a>Deny-Database-All

由于 [AllowVNetInBound](#allowvnetinbound) 默认安全规则允许在同一虚拟网络中的资源之间进行的所有通信，因此需要使用此规则来拒绝来自所有资源的流量。

|Priority|Source|源端口| 目标 | 目标端口 | Protocol | 访问权限 |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | 任意 | 拒绝 |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

此规则允许从 *AsgLogic* 应用程序安全组到 *AsgDb* 应用程序安全组的流量。 此规则的优先级高于 *Deny-Database-All* 规则的优先级。 因此，此规则在 *Deny-Database-All* 规则之前处理，这样系统就会允许来自 *AsgLogic* 应用程序安全组的流量，而阻止所有其他流量。

|Priority|Source|源端口| 目标 | 目标端口 | Protocol | 访问权限 |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

将应用程序安全组指定为源或目标的规则只会应用到属于应用程序安全组成员的网络接口。 如果网络接口不是应用程序安全组的成员，则规则不会应用到网络接口，即使网络安全组关联到子网。

应用程序安全组具有以下约束：

-   一个订阅中可以有的应用程序安全组存在数量限制，此外还有其他与应用程序安全组相关的限制。 有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 可将一个应用程序安全组指定为安全规则中的源和目标。 不能在源或目标中指定多个应用程序安全组。
- 分配给应用程序安全组的所有网络接口都必须存在于分配给应用程序安全组的第一个网络接口所在的虚拟网络中。 例如，如果分配给名为 *AsgWeb* 的应用程序安全组的第一个网络接口位于名为 *VNet1* 的虚拟网络中，则分配给 *ASGWeb* 的所有后续网络接口都必须存在于 *VNet1* 中。 不能向同一应用程序安全组添加来自不同虚拟网络的网络接口。
- 如果在安全规则中将应用程序安全组指定为源和目标，则两个应用程序安全组中的网络接口必须存在于同一虚拟网络中。 例如，如果 *AsgLogic* 包含来自 *VNet1* 的网络接口，*AsgDb* 包含来自 *VNet2* 的网络接口，则不能在一项规则中将 *AsgLogic* 分配为源，将 *AsgDb* 分配为目标。 源和目标应用程序安全组中的所有网络接口需存在于同一虚拟网络中。

> [!TIP]
> 为了尽量减少所需的安全规则数和需要更改规则的情况，请尽可能使用服务标记或应用程序安全组来规划所需的应用程序安全组并创建规则，而不要使用单个 IP 地址或 IP 地址范围。

## <a name="how-traffic-is-evaluated"></a>如何评估流量

可以将资源从多个 Azure 服务部署到一个 Azure 虚拟网络中。 如需完整列表，请参阅[可部署到虚拟网络中的服务](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 可将零个或一个网络安全组与虚拟机中的每个虚拟网络[子网](virtual-network-manage-subnet.md#change-subnet-settings)和[网络接口](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)相关联。 可将同一网络安全组关联到选定的任意数量的子网和网络接口。

下图描述了如何使用不同的方案来部署网络安全组，以便网络流量通过 TCP 端口 80 出入 Internet：

![NSG 处理](./media/security-groups/nsg-interaction.png)

请参阅上图和以下文本，了解 Azure 如何处理网络安全组的入站和出站规则：

### <a name="inbound-traffic"></a>入站流量

对于入站流量，Azure 先处理与某个子网相关联的网络安全组（如果有）中的规则，然后处理与网络接口相关联的网络安全组（如果有）中的规则。

- **VM1**：系统会处理 *NSG1* 中的安全规则，因为它与 *Subnet1* 关联，而 *VM1* 位于 *Subnet1* 中。 除非创建了一条允许端口 80 入站流量的规则，否则流量会被 [DenyAllInbound](#denyallinbound) 默认安全规则拒绝，并且永远不会被 *NSG2* 评估，因为 *NSG2* 关联到网络接口。 如果 *NSG1* 有一条允许端口 80 的安全规则，则流量会由 *NSG2* 处理。 若要允许从端口 80 到虚拟机的流量，*NSG1* 和 *NSG2* 必须指定一条规则来允许从 Internet 到端口 80 的流量。
- **VM2**：系统会处理 *NSG1* 中的规则，因为 *VM2* 也在 *Subnet1* 中。 *VM2* 没有关联到其网络接口的网络安全组，因此会接收 *NSG1* 所允许的所有流量，或者会拒绝 *NSG1* 所拒绝的所有流量。 当网络安全组关联到子网时，对于同一子网中的所有资源，流量要么被允许，要么被拒绝。
- **VM3**：由于没有网络安全组关联到 *Subnet2*，系统允许流量进入子网并由 *NSG2* 处理，因为 *NSG2* 关联到已附加到 *VM3* 的网络接口。
- **VM4**：允许流量发往 *VM4*，因为网络安全组没有关联到 *Subnet3* 或虚拟机中的网络接口。 如果没有关联的网络安全组，则允许所有网络流量通过子网和网络接口。

### <a name="outbound-traffic"></a>出站流量

对于出站流量，Azure 先处理与某个网络接口相关联的网络安全组（如果有）中的规则，然后处理与子网相关联的网络安全组（如果有）中的规则。

- **VM1**：系统会处理 *NSG2* 中的安全规则。 除非创建一条安全规则来拒绝从端口 80 到 Internet 的出站流量，否则 *NSG1* 和 *NSG2* 中的 [AllowInternetOutbound](#allowinternetoutbound) 默认安全规则都会允许该流量。 如果 *NSG2* 有一条拒绝端口 80 的安全规则，则流量会被拒绝，不会由 *NSG1* 评估。 若要拒绝从虚拟机到端口 80 的流量，则两个网络安全组或其中的一个必须有一条规则来拒绝从端口 80 到 Internet 的流量。
- **VM2**：所有流量都会通过网络接口发送到子网，因为附加到 *VM2* 的网络接口没有关联的网络安全组。 系统会处理 *NSG1* 中的规则。
- **VM3**：如果 *NSG2* 有一条拒绝端口 80 的安全规则，则流量会被拒绝。 如果 *NSG2* 有一条允许端口 80 的安全规则，则允许从端口 80 到 Internet 的出站流量，因为没有关联到 *Subnet2* 的网络安全组。
- **VM4**：允许来自 *VM4* 的所有网络流量，因为网络安全组没有关联到已附加到虚拟机的网络接口，或者没有关联到 *Subnet3*。

可以通过查看网络接口的[有效安全规则](virtual-network-network-interface.md#view-effective-security-rules)，轻松查看已应用到网络接口的聚合规则。 还可以使用 Azure 网络观察程序中的 [IP 流验证](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)功能来确定是否允许发往或发自网络接口的通信。 IP 流验证会告知你系统是允许还是拒绝通信，以及哪条网络安全规则允许或拒绝该流量。

> [!NOTE]
> 网络安全组关联到子网或关联到部署在经典部署模型中的虚拟机和云服务，以及关联到资源管理器部署模型中的子网或网络接口。 若要详细了解 Azure 部署模型，请参阅[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

> [!TIP]
> 建议将网络安全组关联到子网或网络接口，但不要二者都关联，除非你有特定的理由来这样做。 由于关联到子网的网络安全组中的规则可能与关联到网络接口的网络安全组中的规则冲突，因此可能会出现意外的必须进行故障排除的通信问题。

## <a name="azure-platform-considerations"></a>Azure 平台注意事项

- **主机节点的虚拟 IP**：基本的基础结构服务（例如 DHCP、DNS、IMDS和运行状况监视）是通过虚拟化主机 IP 地址 168.63.129.16 和 169.254.169.254 提供的。 这些 IP 地址属于 Microsoft，是仅有的用于所有区域的虚拟化 IP 地址，没有其他用途。
- **许可（密钥管理服务）** ：在虚拟机中运行的 Windows 映像必须获得许可。 为了确保许可，会向处理此类查询的密钥管理服务主机服务器发送请求。 该请求是通过端口 1688 以出站方式提出的。 对于使用[默认路由 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) 配置的部署，此平台规则会被禁用。
- **负载均衡池中的虚拟机**：应用的源端口和地址范围来自源计算机，而不是来自负载均衡器。 目标端口和地址范围是目标计算机的，而不是负载均衡器的。
- **Azure 服务实例**：在虚拟网络子网中部署了多个 Azure 服务的实例，例如 HDInsight、应用程序服务环境和虚拟机规模集。 有关可部署到虚拟网络的服务的完整列表，请参阅 [Azure 服务的虚拟网络](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)。 在将网络安全组应用到部署了资源的子网之前，请确保熟悉每个服务的端口要求。 如果拒绝服务所需的端口，服务将无法正常工作。
- **发送出站电子邮件**：Microsoft 建议你利用经过身份验证的 SMTP 中继服务（通常通过 TCP 端口 587 进行连接，但也经常使用其他端口）从 Azure 虚拟机发送电子邮件。 SMTP 中继服务特别重视发件人信誉，尽量降低第三方电子邮件提供商拒绝邮件的可能性。 此类 SMTP 中继服务包括但不限于：Exchange Online Protection 和 SendGrid。 在 Azure 中使用 SMTP 中继服务绝不会受限制，不管订阅类型如何。 

  如果是在 2017 年 11 月 15 日之前创建的 Azure 订阅，则除了能够使用 SMTP 中继服务，还可以直接通过 TCP 端口 25 发送电子邮件。 如果是在 2017 年 11 月 15 日之后创建的订阅，则可能无法直接通过端口 25 发送电子邮件。 经端口 25 的出站通信行为取决于订阅类型，如下所示：

     - **企业协议**：允许端口 25 的出站通信。 可以将出站电子邮件直接从虚拟机发送到外部电子邮件提供商，不受 Azure 平台的限制。 
     - **即用即付：** 阻止所有资源通过端口 25 进行出站通信。 如需将电子邮件从虚拟机直接发送到外部电子邮件提供商（不使用经身份验证的 SMTP 中继），可以请求去除该限制。 Microsoft 会自行审核和批准此类请求，并且只在进行防欺诈检查后授予相关权限。 若要提交请求，请建立一个问题类型为“技术”、“虚拟网络连接”、“无法发送电子邮件（SMTP/端口 25）”的支持案例。 在支持案例中，请详细说明为何你的订阅需要将电子邮件直接发送到邮件提供商，而不经过经身份验证的 SMTP 中继。 如果订阅得到豁免，则只有在豁免日期之后创建的虚拟机能够经端口 25 进行出站通信。
     - **MSDN、Azure Pass、Azure 开放许可、教育、BizSpark 和免费试用版**：阻止所有资源通过端口 25 进行出站通信。 不能请求去除该限制，因为不会针对请求授予相关权限。 若需从虚拟机发送电子邮件，则需使用 SMTP 中继服务。
     - **云服务提供商**：如果无法使用安全的 SMTP 中继，通过云服务提供商消耗 Azure 资源的客户可以通过其云服务提供商创建支持案例，请求提供商代表他们创建取消阻止案例。

  即使 Azure 允许经端口 25 发送电子邮件，Microsoft 也不能保证电子邮件提供商会接受来自你的虚拟机的入站电子邮件。 如果特定的提供商拒绝了来自你的虚拟机的邮件，请直接与该提供商协商解决邮件传送问题或垃圾邮件过滤问题，否则只能使用经身份验证的 SMTP 中继服务。

## <a name="next-steps"></a>后续步骤

* 连接如何[创建网络安全组](tutorial-filter-network-traffic.md)。
