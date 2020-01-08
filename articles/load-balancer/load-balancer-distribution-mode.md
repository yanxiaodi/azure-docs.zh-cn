---
title: 配置 Azure 负载均衡器分发模式
titlesuffix: Azure Load Balancer
description: 如何配置 Azure 负载均衡器的分配模式以支持源 IP 关联。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 0d3ddf2e005338a19972cfcdef025579764f7f23
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114718"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>配置 Azure 负载均衡器的分配模式

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>基于哈希的分发模式

Azure 负载均衡器的默认分配模式是 5 元组哈希。 元组由源 IP、源端口、目标 IP、目标端口、和协议类型构成。 哈希用于将流量映射到可用的服务器，算法仅在传输会话内部提供粘性。 同一会话中的数据包会定向到经过负载均衡的终结点后面的同一数据中心 IP (DIP) 实例。 客户端从同一源 IP 发起新会话时，源端口会更改，并导致流量定向到其他 DIP 终结点。

![基于 5 元组哈希的分配模式](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>源 IP 关联模式

还可以使用源 IP 关联分配模式配置负载均衡器。 此分配模式也称为为会话关联或客户端 IP 关联。 该模式使用 2 元组（源 IP 和目标 IP）或 3 元组（源 IP、目标 IP 和协议）哈希将流量映射到可用的服务器。 通过使用源 IP 关联, 从同一客户端计算机启动的连接会移到同一个 DIP 终结点。

下图演示 2 元组配置。 请注意 2 元组如何从负载均衡器运行到虚拟机 1 (VM1)。 VM1 随后由 VM2 和 VM3 备份。

![2 元组会话关联分配模式](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

源 IP 关联模式解决了 Azure 负载均衡器与远程桌面网关（RD 网关）之间的不兼容问题。 使用此模式可在单个云服务中生成 RD 网关场。

另一个用例方案是媒体上传。 数据上传通过 UDP 进行，但控制平面通过 TCP 实现：

* 客户端启动与负载平衡公用地址的 TCP 会话, 并定向到特定 DIP。 通道将保持活动状态以监视连接运行状况。
* 来自同一客户端计算机的新 UDP 会话在同一个负载均衡公共终结点中发起。 连接像前面的 TCP 连接一样定向到同一个 DIP 终结点。 能够以较高的吞吐量执行媒体上传，同时通过 TCP 维护控制通道。

> [!NOTE]
> 如果通过删除或添加虚拟机来更改负载均衡集，则会重新计算客户端请求的分配。 无法确保现有客户端的新连接最终都会抵达同一台服务器。 此外，使用源 IP 关联分配模式可能导致流量的不均衡分配。 在代理后面运行的客户端可被视为唯一的客户端应用程序。

## <a name="configure-source-ip-affinity-settings"></a>配置源 IP 关联设置

### <a name="azure-portal"></a>Azure 门户

可以通过在门户中修改负载均衡规则来更改分发模式的配置。

1. 登录到 Azure 门户, 并通过单击 "**资源组**" 找到包含要更改的负载均衡器的资源组。
2. 在 "负载均衡器概述" 边栏选项卡中, 单击 "**设置**" 下的**负载均衡规则**。
3. 在 "负载均衡规则" 边栏选项卡中, 单击要更改分发模式的负载均衡规则。
4. 在规则下, 通过更改 "**会话持久性**" 下拉框更改分发模式。  你可使用以下选项：
    
    * **无 (基于哈希)** -指定任何虚拟机可能会处理来自同一客户端的后续请求。
    * **客户端 ip (源 IP 关联2元组)** -指定同一虚拟机将处理来自同一客户端 ip 地址的后续请求。
    * **客户端 ip 和协议 (源 IP 关联3元组)** -指定相同的虚拟机将处理来自同一客户端 ip 地址和协议组合的后续请求。

5. 选择分发模式, 然后单击 "**保存**"。

### <a name="azure-powershell"></a>Azure PowerShell

对于使用资源管理器部署的虚拟机, 请使用 PowerShell 更改现有负载均衡规则的负载均衡器分发设置。 以下命令将更新分发模式: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

对于经典虚拟机，请使用 Azure PowerShell 更改分发设置。 将 Azure 终结点添加到虚拟机并配置负载均衡器分配模式：

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

设置 `LoadBalancerDistribution` 元素的值，实现所需的负载均衡量。 为 2 元组（源 IP 和目标 IP）负载均衡指定 sourceIP。 为 3 元组（源 IP、目标 IP 和协议类型）负载均衡指定 sourceIPProtocol。 为 5 元组负载均衡的默认行为指定 none。

使用以下设置检索终结点负载均衡器分配模式配置：

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

如果该`LoadBalancerDistribution`元素不存在, Azure 负载均衡器将使用默认的5元组算法。

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>在负载均衡终结点集上配置分配模式

如果终结点是负载均衡终结点集的一部分，则必须在负载均衡终结点集上配置分配模式：

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>配置云服务终结点的分配模式

使用用于 .NET 的 Azure SDK 2.5 更新云服务。 在 .csdef 中指定云服务的终结点设置。 若要更新云服务部署的负载均衡器分配模式，需要进行部署升级。

下面是终结点设置的 .csdef 更改的示例：

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>API 示例

以下示例演示如何针对部署中的指定负载均衡集重新配置负载均衡器分配模式。 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>更改已部署的负载均衡集的分配模式

使用 Azure 经典部署模型更改现有的部署配置。 添加 `x-ms-version` 标头，并将值设置为版本 2014-09-01 或更高。

#### <a name="request"></a>请求

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

如前所述，针对 2 元组关联、3 元组关联或 5 元组关联，分别将 `LoadBalancerDistribution` 元素设置为 sourceIP、sourceIPProtocol 或 none（表示无关联）。

#### <a name="response"></a>响应

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>后续步骤

* [Azure 内部负载均衡器概述](load-balancer-internal-overview.md)
* [开始配置面向 Internet 的负载均衡器](load-balancer-get-started-internet-arm-ps.md)
* [配置负载均衡器的空闲 TCP 超时设置](load-balancer-tcp-idle-timeout.md)
