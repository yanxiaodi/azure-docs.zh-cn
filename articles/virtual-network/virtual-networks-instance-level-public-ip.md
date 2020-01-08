---
title: Azure 实例层级公共 IP（经典）地址 | Microsoft Docs
description: 了解实例层级公共 IP (ILPIP) 地址以及如何使用 PowerShell 进行管理。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: d92832d1eee995e8883dc6c8ed0f58c9755e40f8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058404"
---
# <a name="instance-level-public-ip-classic-overview"></a>实例层级公共 IP（经典）概述
实例层级公共 IP (ILPIP) 是可直接分配至 VM 或云服务角色实例（而非 VM 或角色实例所在的云服务）的公共 IP 地址。 ILPIP 不会取代分配给云服务的虚拟 IP (VIP)。 而是可以用来直接连接到 VM 或角色实例的其他 IP 地址。

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同部署模型：[资源管理器部署模型和经典部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 本文介绍使用经典部署模型。 Microsoft 建议通过 Resource Manager 创建 VM。 请确保你了解 [IP 地址](virtual-network-ip-addresses-overview-classic.md)在 Azure 中的工作原理。

![ILPIP 和 VIP 之间的差异](./media/virtual-networks-instance-level-public-ip/Figure1.png)

如图 1 所示，云服务是使用 VIP 访问的，而各个 VM 通常是使用 VIP:&lt;端口号&gt;访问的。 通过将 ILPIP 分配给特定 VM，可以直接使用该 IP 地址访问该 VM。

在 Azure 中创建云服务时，系统会自动创建相应的 DNS A 记录，以便通过完全限定的域名 (FQDN) 而非实际 VIP 来访问服务。 系统会针对 ILPIP 执行相同的进程，以便通过 FQDN 而非 ILPIP 来访问 VM 或角色实例。 例如，如果创建了名为 *contosoadservice* 的云服务，且为名为 *contosoweb* 的 Web 角色配置了两个实例，且在 .cscfg 中 `domainNameLabel` 设置为 *WebPublicIP*，则 Azure 会为实例注册以下 A 记录：


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> 只能为每个 VM 或角色实例分配一个 ILPIP。 每个订阅最多可使用 5 个 ILPIP。 多 NIC VM 不支持 LIPID。
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>为什么要请求 ILPIP？
如果想要能够通过直接向其分配的 IP 地址链接到 VM 或角色实例，请为 VM 或角色实例请求 ILPIP，而不是使用云服务VIP:&lt;端口号&gt;。

* **主动 FTP** - 通过向 VM 分配 ILPIP，可在任何端口上接收流量。 VM 不需要终结点来接收流量。  有关 FTP 协议的详细信息，请参阅 [FTP 协议概述](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview)。
* **出站 IP** - 源自 VM 的出站流量会映射到充当源的 ILPIP，而 ILPIP 唯一标识针对外部实体的 VM。

> [!NOTE]
> ILPIP 地址以前被称为公共 IP (PIP) 地址。
> 

## <a name="manage-an-ilpip-for-a-vm"></a>管理 VM 的 ILPIP
在以下任务中，可通过 VM 创建、分配和删除 ILPIP：

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>在 VM 创建期间如何使用 PowerShell 请求 ILPIP
下面的 PowerShell 脚本将创建名为 *FTPService* 的云服务，然后从 Azure 中检索映像，并使用检索到的映像创建名为 *FTPInstance* 的 VM，接着将 VM 设置为使用 ILPIP，最后再将 VM 添加到新服务：

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

#Set "current" storage account for the subscription. It will be used as the location of new VM disk

Set-AzureSubscription -SubscriptionName <SubName> -CurrentStorageAccountName <StorageAccountName>

#Create a new VM configuration object

New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

```
如果要将另一个存储帐户指定为新 VM 磁盘的位置，可使用 MediaLocation 参数：

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>如何检索 VM 的 ILPIP 信息
要查看使用以上脚本创建的 VM 的 ILPIP 信息，请运行以下 PowerShell 命令，并观察 *PublicIPAddress* 和 *PublicIPName* 的值：

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

预期输出：
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>如何删除 VM 的 ILPIP
若要删除在以上脚本中添加到 VM 的 ILPIP，请运行以下 PowerShell 命令：

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>如何向现有 VM 添加 ILPIP
若要向使用以上脚本创建的 VM 添加 ILPIP，请运行以下命令：

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>管理云服务角色实例的 ILPIP

要将 ILPIP 添加到云服务角色实例，请完成以下步骤：

1. 通过完成[如何配置云服务](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg)文章中的步骤，下载云服务的 .cscfg 文件。
2. 通过添加 `InstanceAddress` 元素更新 .cscfg 文件。 以下示例将名为 *MyPublicIP* 的 ILPIP 添加到名为 *WebRole1* 的角色实例中： 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="WebPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. 通过完成[如何配置云服务](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg)文章中的步骤，上传云服务的 .cscfg 文件。

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>如何检索云服务的 ILPIP 信息
若要查看每个角色实例的 ILPIP 信息，请运行以下 PowerShell 命令，并观察 *PublicIPAddress*、*PublicIPName*、*PublicIPDomainNameLabel* 和 *PublicIPFqdns* 的值：

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

还可以使用 `nslookup` 查询子域的 A 记录：

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>后续步骤
* 了解 [IP 寻址](virtual-network-ip-addresses-overview-classic.md)在经典部署模型中的工作原理。
* 了解[保留 IP](virtual-networks-reserved-public-ip.md)。
