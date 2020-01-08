---
title: 管理 Azure 保留 IP 地址（经典）| Microsoft Docs
description: 了解保留 IP 地址（经典），以及如何使用 Azure PowerShell 和 Azure CLI 管理这些 IP 地址。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 675e7a7b557a3f19ea4d8d4960316c3859cbb9c1
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058476"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>保留 IP 地址（经典部署）

 Azure 中的 IP 地址分为两类：动态 IP 地址和保留 IP 地址。 由 Azure 管理的公共 IP 地址默认为动态 IP 地址。 这意味着，用于给定云服务的 IP 地址 (VIP) 或用于直接访问 VM 或角色实例的 IP 地址 (ILPIP) 可能会在关闭资源或停止（释放）资源的情况下不时进行更改。

要防止 IP 地址更改，可将其设置为保留 IP 地址。 保留 IP 只能用作 VIP，可确保云服务的 IP 地址即使在关闭资源或停止（释放）资源的情况下也保持不变。 此外，还可以将用作 VIP 的现有动态 IP 转换为保留 IP 地址。

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型：[资源管理器部署模型和经典部署模型](../azure-resource-manager/resource-manager-deployment-model.md)。 本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 了解如何使用 [Resource Manager 部署模型](virtual-network-ip-addresses-overview-arm.md)保留静态公共 IP 地址。

若要详细了解 Azure 中的 IP 地址，请阅读 [IP 地址](virtual-network-ip-addresses-overview-classic.md)一文。

## <a name="when-do-i-need-a-reserved-ip"></a>何时需要保留 IP？
* **想要确保将 IP 保留在订阅中**。 如果想要保留一个 IP 地址，使得该 IP 地址在任何情况下都不会从订阅中释放，则应使用保留的公共 IP。  
* **想要 IP 始终与云服务相关联，即使 VM 处于停止或释放状态下**。 如果想要通过即使在云服务中的 VM 处于关闭或停止（释放）状态下也不会更改的 IP 地址来访问服务。
* **想要确保 Azure 的出站流量使用可预测的 IP 地址**。 可以将本地防火墙配置为仅允许来自特定 IP 地址的流量。 通过保留 IP，可以了解源 IP 地址，无需因为 IP 更改而更新防火墙规则。

## <a name="faqs"></a>常见问题
- 可以将保留 IP 用于所有 Azure 服务吗？
    否。 保留 IP 只能用于通过 VIP 公开的 VM 和云服务实例角色。
- 我可以有多少个保留 IP？
    有关详细信息，请参阅 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)一文。
- 保留 IP 是否收费？
    有时。 有关定价详细信息，请参阅[保留 IP 地址定价详细信息](https://go.microsoft.com/fwlink/?LinkID=398482)。
- 如何保留某个 IP 地址？
    可以使用 PowerShell、[Azure 管理 REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx) 或 [Azure 门户](https://portal.azure.com)在 Azure 区域中保留 IP 地址。 保留 IP 地址将关联到订阅。
- 我是否可将保留 IP 用于基于地缘组的 VNet？
    否。 仅区域 VNet 支持保留 IP。 与地缘组关联的 VNet 不支持保留 IP。 有关如何将 VNet 与区域或地缘组关联的详细信息，请参阅[关于区域 VNet 和地缘组](virtual-networks-migrate-to-regional-vnet.md)一文。

## <a name="manage-reserved-vips"></a>管理保留 VIP

### <a name="using-azure-powershell-classic"></a>使用 Azure PowerShell（经典）

在使用保留 IP 之前，必须先将其添加到订阅。 从美国中部位置提供的公共 IP 地址池创建保留 IP，如下所示：

> [!NOTE]
> 对于经典部署模型，必须安装 Azure PowerShell 的服务管理版本。 有关详细信息，请参阅[安装 Azure PowerShell 服务管理模块](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0)。 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
但请注意，不能指定要保留的具体 IP。 如果要查看订阅中哪些 IP 地址为保留 IP 地址，请运行以下 PowerShell 命令，并查看 *ReservedIPName* 和 *Address* 的值：

```powershell
Get-AzureReservedIP
```

预期输出：

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>使用 PowerShell 创建保留 IP 地址时，不能指定要在其中创建保留 IP 的资源组。 Azure 将其自动放置于名为默认网络的资源组中。 如果使用 [Azure 门户](https://portal.azure.com)创建保留 IP，可指定所选的任何资源组。 但是，如果在默认网络以外的资源组中创建保留 IP，每当使用 `Get-AzureReservedIP` 和 `Remove-AzureReservedIP` 等命令引用保留 IP 时，必须引用名称“Group resource-group-name reserved-ip-name”。  例如，如果在名为 myResourceGroup 的资源组中创建名为 myReservedIP 的保留 IP，必须将保留 IP 的名称引用为“Group myResourceGroup myReservedIP”。   


某个 IP 成为保留 IP 后，它就会始终与订阅相关联，直至将它删除。 删除保留 IP，如下所示：

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>使用 Azure CLI（经典）
使用 Azure 经典 CLI 从美国中部位置提供的公共 IP 地址池创建保留 IP，如下所示：

> [!NOTE]
> 对于经典部署，必须使用 Azure 经典 CLI。 有关安装 Azure 经典 CLI 的信息，请参阅[安装 Azure 经典 CLI](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 命令：
 
```azurecli
azure network reserved-ip create <name> <location>
```
例如：
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

可以使用 Azure CLI 查看订阅中保留的 IP 地址，如下所示： 

命令：
```azurecli
azure network reserved-ip list
```
某个 IP 成为保留 IP 后，它就会始终与订阅相关联，直至将它删除。 删除保留 IP，如下所示：

命令：

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  例如：  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>保留现有云服务的 IP 地址
添加 `-ServiceName` 参数即可保留现有云服务的 IP 地址。 保留美国中部位置中 TestService 云服务的 IP 地址，如下所示：

- 使用 Azure PowerShell（经典）：

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- 使用 Azure CLI（经典）：
  
    命令：

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    例如：

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>将保留 IP 关联到新的云服务
下面的脚本将创建新的保留 IP，然后将其关联到名为 TestService 的新云服务。

### <a name="using-azure-powershell-classic"></a>使用 Azure PowerShell（经典）
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> 创建用于云服务的保留 IP 时，仍需使用 VIP:&lt;端口号> 来引用 VM，以便进行入站通信。 使用保留 IP 并不意味着可以直接连接到 VM。 保留 IP 将分配给 VM 所部署到的云服务。 如果想要直接通过 IP 连接到 VM，则必须配置实例层级公共 IP。 实例层级公共 IP 是一类可直接分配给 VM 的公共 IP（称为 ILPIP）。 它不能保留。 有关详细信息，请参阅[实例层级公共 IP (ILPIP)](virtual-networks-instance-level-public-ip.md) 一文。
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>从正在运行的部署中删除保留 IP

删除添加到新的云服务的保留 IP，如下所示： 
### <a name="using-azure-powershell-classic"></a>使用 Azure PowerShell（经典）

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>使用 Azure CLI（经典）
命令：

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

例如：

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> 从正在运行的部署中删除预留 IP 并不会从订阅中删除预留 IP。 它只是释放该 IP，以供订阅中的其他资源使用。
> 

若要从订阅中完全删除保留 IP，请运行以下命令：

命令：

```azurecli
azure network reserved-ip delete <name>
```
例如：

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>将保留 IP 关联到正在运行的部署

### <a name="using-azure-powershell-classic"></a>使用 Azure PowerShell（经典）

以下命令将使用名为 TestVM2 的新 VM 创建名为 TestService2 的云服务。 然后，名为 MyReservedIP 的现有保留 IP 将关联到云服务。

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>使用 Azure CLI（经典）
可以使用 Azure CLI 将新的保留 IP 关联到正在运行的云服务部署，如下所示：

命令：
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
例如：
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>使用服务配置文件将保留 IP 关联到云服务
也可以使用服务配置 (CSCFG) 文件将保留 IP 关联到云服务。 下面的示例 xml 演示如何将云服务配置为使用名为 *MyReservedIP* 的保留 VIP：
```
   <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
```
## <a name="next-steps"></a>后续步骤
* 了解 [IP 寻址](virtual-network-ip-addresses-overview-classic.md)在经典部署模型中的工作原理。
* 了解[保留专用 IP 地址](virtual-networks-reserved-private-ip.md)。
* 了解[实例层级公共 IP (ILPIP) 地址](virtual-networks-instance-level-public-ip.md)。

