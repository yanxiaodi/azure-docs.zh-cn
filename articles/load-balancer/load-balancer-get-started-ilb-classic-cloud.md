---
title: 为 Azure 云服务创建内部负载均衡器 - 经典部署
titlesuffix: Azure Load Balancer
description: 了解如何在经典部署模型中使用 PowerShell 创建内部负载均衡器
services: load-balancer
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: e0df334cb4836138f9f7b9c70d299d87d159fefc
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090922"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>开始为云服务创建内部负载均衡器（经典）

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [云服务](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。  本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 了解如何[使用 Resource Manager 模型执行这些步骤](load-balancer-get-started-ilb-arm-ps.md)。

## <a name="configure-internal-load-balancer-for-cloud-services"></a>为云服务配置内部负载均衡器

虚拟机和云服务都支持内部负载均衡器。 在区域虚拟网络外部的云服务中创建的内部负载均衡器终结点将只能在该云服务中进行访问。

在云服务中创建第一个部署期间必须设置内部负载均衡器配置，如下面的示例中所示。

> [!IMPORTANT]
> 运行以下步骤的先决条件是已为云部署创建虚拟网络。 需要虚拟网络名称和子网名称，以便创建内部负载均衡。

### <a name="step-1"></a>步骤 1

在 Visual Studio 中打开云部署的服务配置文件 (.cscfg)，并在网络配置的最后一个“`</Role>`”项下添加以下节，以便创建内部负载均衡。

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="name of the load balancer">
        <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

让我们为网络配置文件添加值，以便显示其外观。 在此示例中，假定你创建了一个名为“test_vnet”的 VNet，其中包含一个名为 test_subnet 的子网 10.0.0.0/24 并具有静态 IP 10.0.0.4。 负载均衡器将名为 testLB。

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="testLB">
        <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

有关负载均衡器架构的详细信息，请参阅[添加负载均衡器](https://msdn.microsoft.com/library/azure/dn722411.aspx)。

### <a name="step-2"></a>步骤 2

更改服务定义 (.csdef) 文件，以便向内部负载均衡添加终结点。 创建角色实例的那一刻，服务定义文件会将角色实例添加到内部负载均衡。

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
    </Endpoints>
</WorkerRole>
```

按照上面的示例的相同值，让我们将值添加到服务定义文件。

```xml
<WorkerRole name="WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
    </Endpoints>
</WorkerRole>
```

将使用 testLB 负载均衡器对网络流量进行负载均衡，将端口 80 用于传入请求，也在端口 80 上发送到辅助角色实例。

## <a name="next-steps"></a>后续步骤

[使用源 IP 关联配置负载均衡器分发模式](load-balancer-distribution-mode.md)

[配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)

