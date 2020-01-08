---
title: 纵向扩展 Azure Service Fabric 节点类型 | Microsoft Docs
description: 了解如何通过添加虚拟机规模集缩放 Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: atsenthi
ms.openlocfilehash: 272bc571a0ea71fd6e7bd45a426460d2e0faf1d7
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599283"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>纵向扩展 Service Fabric 群集主节点类型
本文介绍如何通过增加虚拟机资源来纵向扩展 Service Fabric 群集主节点类型。 Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 属于群集一部分的计算机或 VM 称为节点。 虚拟机规模集是一种 Azure 计算资源，用于将一组 VM 作为一个集进行部署和管理。 Azure 群集中定义的每个节点类型[设置为独立的规模集](service-fabric-cluster-nodetypes.md)。 然后可以单独管理每个节点类型。 创建 Service Fabric 群集后，可以纵向缩放群集节点类型（更改节点的资源）或升级节点类型 VM 的操作系统。  随时可以缩放群集，即使该群集上正在运行工作负荷。  在缩放群集的同时，应用程序也会随之自动缩放。

> [!WARNING]
> 如果群集运行状况不正常，请勿开始更改主节点类型 VM SKU。 群集运行状况不正常时，如果尝试更改 VM SKU，只会进一步破坏群集的稳定性。
>
> 我们建议不要更改规模集/节点类型的 VM SKU，除非它在[银级持久性或更高的级别](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)运行。 更改 VM SKU 大小是一种破坏数据的就地基础结构操作。 由于无法延迟或监视此更改，此操作可能会导致有状态服务的数据丢失或其他意外操作问题（甚至可能影响无状态工作负载）。 这表示运行有状态 Service Fabric 系统服务的主节点类型，或运行有状态应用程序工作负载的任何节点类型。
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>升级主节点类型 VM 的大小和操作系统
以下是主节点类型 VM 的 VM 大小和操作系统的更新过程。  升级后，主节点类型 VM 的大小为标准 D4_V2，并且运行带容器的 Windows Server 2016 Datacenter。

> [!WARNING]
> 在生产群集上尝试执行此过程之前，建议先研究示例模板并对测试群集验证此过程。 该群集也会有段时间不可用。 不能并行对声明为相同 NodeType 的多个 VMSS 执行更改，需要执行单独的部署操作来单独为每个 NodeType VMSS 应用更改。

1. 使用这些示例[模板](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json)和[参数](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json)文件部署包含两种节点类型和两个规模集（每种节点类型一个规模集）的初始群集。  这两个规模集的大小均为标准 D2_V2，并且都运行 Windows Server 2012 R2 Datacenter。  等待群集完成基线升级。   
2. 可选：向群集部署有状态示例。
3. 在决定升级主节点类型 VM 以后，使用这些示例[模板](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json)和[参数](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json)文件向主节点类型添加新的规模集，这样一来，主节点类型现在就有两个规模集。  系统服务和用户应用程序能够在两个不同规模集中的 VM 之间迁移。  新规模集 VM 的大小为标准 D4_V2，并运行带容器的 Windows Server 2016 Datacenter。  添加新的规模集时也会添加新的负载均衡器和公共 IP 地址。  
    若要在模板中查找新的规模集，请搜索由 *vmNodeType2Name* 参数命名的“Microsoft.Compute/virtualMachineScaleSets”资源。  系统使用 properties->virtualMachineProfile->extensionProfile->extensions->properties->settings->nodeTypeRef 设置将新的规模集添加到主节点类型中。
4. 检查群集运行状况并验证所有节点是否都处于正常状态。
5. 禁用主节点类型的旧规模集中的节点，以便删除节点。 可以一次禁用所有节点，并且这些操作会排入队列。 等到所有节点都被禁用，这可能需要一些时间。  由于禁用了节点类型中较旧的节点，因此，系统服务和种子节点会迁移到主节点类型中新规模集的 VM。
6. 从主节点类型中删除较旧的规模集。
7. 删除与旧规模集关联的负载均衡器。 在为新规模集配置新的公共 IP 地址和负载均衡器时，群集不可用。  
8. 将与旧的主节点类型规模集关联的公共 IP 地址的 DNS 设置存储在变量中，并删除该公共 IP 地址。
9. 将与新的主节点类型规模集关联的公共 IP 地址的 DNS 设置替换为已删除的公共 IP 地址的 DNS 设置。  现在可以再次访问群集。
10. 从群集中删除节点的节点状态。  如果旧规模集的持续性级别为银级或金级，则此步骤由系统自动完成。
11. 如果在之前的步骤中部署了有状态的应用程序，请验证该应用程序能否正常运行。

```powershell
# Variables.
$groupname = "sfupgradetestgroup"
$clusterloc="southcentralus"  
$subscriptionID="<your subscription ID>"

# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $subscriptionID 

# Create a new resource group for your deployment and give it a name and a location.
New-AzResourceGroup -Name $groupname -Location $clusterloc

# Deploy the two node type cluster.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-2ScaleSets.json" -Verbose

# Connect to the cluster and check the cluster health.
$ClusterName= "sfupgradetest.southcentralus.cloudapp.azure.com:19000"
$thumb="F361720F4BD5449F6F083DDE99DC51A86985B25B"

Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Get-ServiceFabricClusterHealth

# Deploy a new scale set into the primary node type.  Create a new load balancer and public IP address for the new scale set.
New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.parameters.json" `
    -TemplateFile "C:\temp\cluster\Deploy-2NodeTypes-3ScaleSets.json" -Verbose

# Check the cluster health again. All 15 nodes should be healthy.
Get-ServiceFabricClusterHealth

# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}

Write-Host "Checking node status..."
foreach($name in $nodeNames){
 
    $state = Get-ServiceFabricNode -NodeName $name 

    $loopTimeout = 50

    do{
        Start-Sleep 5
        $loopTimeout -= 1
        $state = Get-ServiceFabricNode -NodeName $name
        Write-Host "$name state: " $state.NodeDeactivationInfo.Status
    }

    while (($state.NodeDeactivationInfo.Status -ne "Completed") -and ($loopTimeout -ne 0))
    

    if ($state.NodeStatus -ne [System.Fabric.Query.NodeStatus]::Disabled)
    {
        Write-Error "$name node deactivation failed with state" $state.NodeStatus
        exit
    }
}

# Remove the scale set
$scaleSetName="NTvm1"
Remove-AzVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force
Write-Host "Removed scale set $scaleSetName"

$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP

# Check the cluster health
Get-ServiceFabricClusterHealth

# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

## <a name="next-steps"></a>后续步骤
* 了解如何[向群集添加节点类型](virtual-machine-scale-set-scale-node-type-scale-out.md)
* 了解[应用程序可伸缩性](service-fabric-concepts-scalability.md)。
* [横向扩展或缩减 Azure 群集](service-fabric-tutorial-scale-cluster.md)
* 使用 fluent Azure 计算 SDK [以编程方式缩放 Azure 群集](service-fabric-cluster-programmatic-scaling.md)。
* [横向扩展或缩减独立群集](service-fabric-cluster-windows-server-add-remove-nodes.md)。

