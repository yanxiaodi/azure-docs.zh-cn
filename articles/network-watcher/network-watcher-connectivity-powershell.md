---
title: 使用 Azure 网络观察程序排查连接问题 - PowerShell | Microsoft Docs
description: 了解如何通过 PowerShell 使用 Azure 网络观察程序的排查连接问题功能。
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: kumud
ms.openlocfilehash: 82bd92de8b2cbb0da4d6d37911a6a3f71186b592
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802045"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-powershell"></a>通过 PowerShell 使用 Azure 网络观察程序排查连接问题

> [!div class="op_single_selector"]
> - [门户](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

了解如何使用排查连接问题来验证是否可以建立从虚拟机到给定终结点的直接 TCP 连接。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>开始之前

* 要排查连接问题的区域中的网络观察程序实例。
* 用以排查连接问题的虚拟机。

> [!IMPORTANT]
> 连接故障排除需要从中进行故障排除的 VM 安装了 `AzureNetworkWatcherExtension` VM 扩展。 有关在 Windows VM 上安装扩展的信息，请访问[适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)；有关 Linux VM 的信息，请访问[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。 在目标终结点上不需要该扩展。

## <a name="check-connectivity-to-a-virtual-machine"></a>检查与虚拟机的连接

此示例通过端口 80 检查与目标虚拟机的连接。 此示例要求在包含源 VM 的区域中启用网络观察程序。  

### <a name="example"></a>示例

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"
$destVMName = "Database0"

$RG = Get-AzResourceGroup -Name $rgName

$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName
$VM2 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $destVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location 

Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationId $VM2.Id -DestinationPort 80
```

### <a name="response"></a>响应

以下响应来自前面的示例。  在此响应中，`ConnectionStatus` 为“不可访问”。 可以看到所有探测都发送失败。 由于用户配置的名为 **UserRule_Port80** 的 `NetworkSecurityRule` 已配置为阻止端口 80 上的传入流量，虚拟设备上的连接失败。 可以使用此信息来了解连接问题。

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "0f1500cd-c512-4d43-b431-7267e4e67017",
                       "Address": "10.1.3.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/auNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "a88940f8-5fbe-40da-8d99-1dee89240f64"
                       ],
                       "Issues": [
                         {
                           "Origin": "Outbound",
                           "Severity": "Error",
                           "Type": "NetworkSecurityRule",
                           "Context": [
                             {
                               "key": "RuleName",
                               "value": "UserRule_Port80"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "Type": "VnetLocal",
                       "Id": "a88940f8-5fbe-40da-8d99-1dee89240f64",
                       "Address": "10.1.4.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/dbNic0/ipConfigurati
                   ons/ipconfig1",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="validate-routing-issues"></a>验证路由问题

该示例检查虚拟机与远程终结点之间的连接。 此示例要求在包含源 VM 的区域中启用网络观察程序。  

### <a name="example"></a>示例

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzResourceGroup -Name $rgName
$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location 

Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress 13.107.21.200 -DestinationPort 80
```

### <a name="response"></a>响应

在以下示例中，`ConnectionStatus` 显示为“不可访问”。 在 `Hops` 详细信息中，可以在 `Issues` 下看到由于 `UserDefinedRoute` 流量已被阻止。 

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "b4f7bceb-07a3-44ca-8bae-adec6628225f",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "3fee8adf-692f-4523-b742-f6fdf6da6584"
                       ],
                       "Issues": [
                         {
                           "Origin": "Outbound",
                           "Severity": "Error",
                           "Type": "UserDefinedRoute",
                           "Context": [
                             {
                               "key": "RouteType",
                               "value": "User"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "Type": "Destination",
                       "Id": "3fee8adf-692f-4523-b742-f6fdf6da6584",
                       "Address": "13.107.21.200",
                       "ResourceId": "Unknown",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="check-website-latency"></a>检查网站延迟

以下示例检查与网站的连接。 此示例要求在包含源 VM 的区域中启用网络观察程序。  

### <a name="example"></a>示例

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzResourceGroup -Name $rgName
$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location 


Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress https://bing.com/
```

### <a name="response"></a>响应

在以下响应中，可以看到 `ConnectionStatus` 显示为“可以访问”。 连接成功后，提供了延迟值。

```
ConnectionStatus : Reachable
AvgLatencyInMs   : 1
MinLatencyInMs   : 0
MaxLatencyInMs   : 7
ProbesSent       : 100
ProbesFailed     : 0
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "1f0e3415-27b0-4bf7-a59d-3e19fb854e3e",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "f99f2bd1-42e8-4bbf-85b6-5d21d00c84e0"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "Internet",
                       "Id": "f99f2bd1-42e8-4bbf-85b6-5d21d00c84e0",
                       "Address": "204.79.197.200",
                       "ResourceId": "Internet",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>检查与存储终结点的连接

以下示例检查从虚拟机到博客存储帐户的连接。 此示例要求在包含源 VM 的区域中启用网络观察程序。  

### <a name="example"></a>示例

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzResourceGroup -Name $rgName

$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location

Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress https://contosostorageexample.blob.core.windows.net/ 
```

### <a name="response"></a>响应

以下 json 是运行前面 cmdlet 的示例响应。 因为目标可访问，`ConnectionStatus` 属性显示为“可以访问”。  提供了有关到达存储 blob 所需的跃点数和延迟的详细信息。

```json
ConnectionStatus : Reachable
AvgLatencyInMs   : 1
MinLatencyInMs   : 0
MaxLatencyInMs   : 8
ProbesSent       : 100
ProbesFailed     : 0
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "9e7f61d9-fb45-41db-83e2-c815a919b8ed",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "1e6d4b3c-7964-4afd-b959-aaa746ee0f15"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "Internet",
                       "Id": "1e6d4b3c-7964-4afd-b959-aaa746ee0f15",
                       "Address": "13.71.200.248",
                       "ResourceId": "Internet",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="next-steps"></a>后续步骤

访问[查看“IP 流验证”](diagnose-vm-network-traffic-filtering-problem.md)，确定是否允许某些流量传入和传出 VM。

如果流量不应被阻止但却被阻止，请参阅[管理网络安全组](../virtual-network/manage-network-security-group.md)，查找已定义的网络安全组和安全规则。
