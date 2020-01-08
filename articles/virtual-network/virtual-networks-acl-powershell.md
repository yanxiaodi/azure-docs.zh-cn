---
title: 管理 Azure 终结点访问控制列表 | PowerShell | 经典 | Microsoft 文档
description: 了解如何使用 PowerShell 管理 ACL
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 4fe8349863c16a15886f9f1d33056f0bbfec31f2
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056766"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>在典型部署模型中使用 PowerShell 管理终结点访问控制列表
可以使用 Azure PowerShell 或在管理门户中为终结点创建和管理网络访问控制列表 (ACL)。 在本主题中，将了解可使用 PowerShell 完成的 ACL 常见任务的过程。 有关 Azure PowerShell cmdlet 的列表，请参阅 [Azure 管理 Cmdlet](https://go.microsoft.com/fwlink/?LinkId=317721)。 有关 ACL 的详细信息，请参阅[什么是网络访问控制列表 (ACL)？](virtual-networks-acl.md)。 如果要使用管理门户管理 ACL，请参阅[如何设置虚拟机的终结点](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="manage-network-acls-by-using-azure-powershell"></a>使用 Azure PowerShell 管理网络 ACL
可以使用 Azure PowerShell cmdlet 创建、删除和配置（设置）网络访问控制列表 (ACL)。 我们已提供一些示例，介绍了通过 PowerShell 配置 ACL 的多种方法。

若要检索 ACL PowerShell cmdlet 的完整列表，可使用以下方法之一：

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>创建包含允许从远程子网进行访问的规则的网络 ACL
以下示例演示了创建包含规则的新 ACL 的方法。 此 ACL 随后会应用于虚拟机终结点。 以下示例中的 ACL 规则将允许从远程子网进行访问。 若要为远程子网创建一个包含允许规则的新网络 ACL，请打开 Azure PowerShell ISE。 复制并粘贴下面的脚本，使用自己的值配置该脚本，然后运行该脚本。

1. 创建新的网络 ACL 对象。
   
        $acl1 = New-AzureAclConfig
2. 设置允许从远程子网进行访问的规则。 在以下示例中，将设置规则 *100*（比规则 200 及以上更高的优先级）以允许远程子网 *10.0.0.0/8* 访问虚拟机终结点。 将值替换成自己的配置要求。 名称“SharePoint ACL config”应替换为要用来调用此规则的友好名称。
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. 对于其他规则，请重复该 cmdlet，并将值替换成自己的配置要求。 请务必更改规则号顺序以反映应用规则的顺序。 规则号越小，优先级越高。
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. 接下来，可以创建新终结点（添加）或为现有终结点设置 ACL（设置）。 在本例中，我们将添加称为“Web”的新虚拟机终结点并更新具有 ACL 设置的虚拟机终结点。
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. 接下来，组合 cmdlet 并运行脚本。 对于本示例，组合的 cmdlet 如下所示：
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>删除允许从远程子网进行访问的网络 ACL 规则
以下示例演示删除网络 ACL 规则的方法。  若要为远程子网删除包含允许规则的网络 ACL 规则，请打开 Azure PowerShell ISE。 复制并粘贴下面的脚本，使用自己的值配置该脚本，然后运行该脚本。

1. 第一步是获取虚拟机终结点的网络 ACL 对象。 然后，删除 ACL 规则。 在此示例中，我们将按规则 ID 删除它。 这只会从 ACL 中删除规则 ID 0， 而不会从虚拟机终结点中删除 ACL 对象。
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. 接下来，必须将网络 ACL 对象应用于虚拟机终结点并更新虚拟机。
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>从虚拟机终结点中删除网络 ACL
在某些情况下，你可能希望从虚拟机终结点中删除网络 ACL 对象。 为此，请打开 Azure PowerShell ISE。 复制并粘贴下面的脚本，使用自己的值配置该脚本，然后运行该脚本。

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>后续步骤
[什么是网络访问控制列表 (ACL)？](virtual-networks-acl.md)

