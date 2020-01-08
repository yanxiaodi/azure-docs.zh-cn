---
title: 教程：使用 PowerShell 部署到现有虚拟网络中 - Azure 专用 HSM | Microsoft Docs
description: 本教程介绍如何使用 PowerShell 将专用 HSM 部署到现有虚拟网络中
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 53fb4fa344839957a3f98275d174bbb787fa5e38
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70880986"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>教材 - 使用 PowerShell 将 HSM 部署到现有虚拟网络中

Azure 专用 HSM 服务提供供单个客户使用的物理设备，由客户对设备进行完全的管理控制并承担完全的管理责任。 由于提供物理硬件，因此 Microsoft 必须控制这些设备的分配，确保对容量进行有效的管理。 因此，在 Azure 订阅中，专用 HSM 服务通常不可见，不可用于资源预配。 Azure 客户如果需要访问专用 HSM 服务，必须首先联系其 Microsoft 客户主管，提交专用 HSM 服务注册请求。 只有在此流程成功完成以后，才可以进行预配。
本教程的目的是介绍一个典型的预配过程，其中：

- 客户已经有一个虚拟网络
- 客户有一个虚拟机
- 客户需将 HSM 资源添加到现有环境中。

典型的高可用性多区域部署体系结构看起来如下所示：

![多区域部署](media/tutorial-deploy-hsm-powershell/high-availability.png)

本教程重点介绍一对 HSM 和必需的 ExpressRoute 网关（参见上面的子网 1），该网关会集成到现有的虚拟网络（参见上面的 VNET 1）中。  所有其他资源都是标准的 Azure 资源。 同一集成过程可以用于上述 VNET 3 上的子网 4 中的 HSM。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

Azure 专用 HSM 目前在 Azure 门户中不可用，因此与该服务的所有交互将通过命令行或 PowerShell 进行。 本教程将在 Azure Cloud Shell 中使用 PowerShell。 如果不熟悉 PowerShell，请按以下入门说明操作：[Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

假设：

- 已完成 Azure 专用 HSM 注册过程，并已获允使用此服务。 否则，请与 Microsoft 客户代表联系，了解详细信息。 
- 已为这些资源创建一个资源组，在本教程中部署的新资源将加入该组。
- 已根据上图创建必需的虚拟网络、子网和虚拟机，现在需将 2 个 HSM 集成到该部署中。

下面的所有说明假定你已导航到 Azure 门户并打开 Cloud Shell（选择门户右上角的“\>\_”）。

## <a name="provisioning-a-dedicated-hsm"></a>预配专用 HSM

可以通过 ExpressRoute 网关预配 HSM 并将其集成到现有虚拟网络中，这一操作将通过 ssh 命令行工具进行验证，确保 HSM 设备的可访问性和基本的可用性，以便进行进一步的配置活动。 以下命令将使用资源管理器模板创建 HSM 资源和关联的网络资源。

### <a name="validating-feature-registration"></a>验证功能注册

如上所述，任何预配活动都要求为订阅注册专用 HSM 服务。 若要对此进行验证，请在 Azure 门户 Cloud Shell 中运行以下 PowerShell 命令。 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

以下命令验证专用 HSM 服务所需的网络功能。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowBaremetalServers
```

两个命令都应该返回“已注册”状态（如下所示），然后才能执行下一步的操作。  如果需要注册此服务，请与 Microsoft 客户代表联系。

![订阅状态](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>创建 HSM 资源

HSM 设备预配到客户的虚拟网络中。 这意味着子网的要求。 HSM 依赖 ExpressRoute 网关在虚拟网络和物理设备之间通信。最终如果需要使用 Gemalto 客户端软件来访问 HSM 设备，则虚拟机是必需的。 这些资源已收集到一个带有相应参数文件的模板文件中，以方便使用。 若要获取这些文件，请通过 HSMrequest@Microsoft.com 直接联系 Microsoft。

有了这些文件以后，必须编辑该参数文件，插入资源的首选名称。 这意味着使用“值”: “”格式编辑行。

- `namingInfix` HSM 资源名称的前缀
- `ExistingVirtualNetworkName` 用于 HSM 的虚拟网络的名称
- `DedicatedHsmResourceName1` 数据中心戳 1 中 HSM 资源的名称
- `DedicatedHsmResourceName2` 数据中心戳 2 中 HSM 资源的名称
- `hsmSubnetRange` HSM 的子网 IP 地址范围
- `ERSubnetRange` VNET 网关的子网 IP 地址范围

这些更改的示例如下所示：

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

关联的资源管理器模板文件将根据以下信息创建 6 个资源：

- 指定 VNET 中的 HSM 的子网
- 虚拟网关的子网 
- 将 VNET 连接到 HSM 设备的虚拟网关
- 网关的公共 IP 地址
- 戳 1 中的 HSM
- 戳 2 中的 HSM

设置参数值以后，需将文件上传到 Azure 门户 Cloud Shell 文件共享以供使用。 在 Azure 门户中单击右上角的“\>\_”Cloud Shell 符号，这样就会使屏幕的底部成为一个命令环境。 此处的选项为 BASH 和 PowerShell，应该选择 BASH（如果尚未设置）。

命令 shell 在工具栏上有一个上传/下载选项。应该选择该选项，将模板和参数文件上传到文件共享：

![文件共享](media/tutorial-deploy-hsm-powershell/file-share.png)

上传文件以后，即可创建资源。
在创建新的 HSM 资源之前，应确保某些先决条件资源到位： 必须有一个子网范围适用于计算、HSM 和网关的虚拟网络。 以下命令以示例方式说明了如何才能创建此类虚拟网络。

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>此虚拟网络需要注意的最重要配置，是 HSM 设备的子网必须将委托设置为“Microsoft.HardwareSecurityModules/dedicatedHSMs”。  否则，HSM 预配将不起作用。

所有先决条件都已准备到位以后，请运行使用资源管理器模板所需的以下命令，确保已使用唯一名称（至少已使用资源组名称）更新值：

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

完成此命令应该需要大约 20 分钟。 所使用的“-verbose”选项会确保状态持续显示。

![预配状态](media/tutorial-deploy-hsm-powershell/progress-status.png)

成功完成以后，会显示“provisioningState”:“成功”，此时可以登录到现有的虚拟机，并使用 SSH 确保 HSM 设备的可用性。

## <a name="verifying-the-deployment"></a>验证部署

若要验证设备是否已预配并查看设备属性，请运行以下命令集。 确保资源组已正确设置且资源名称与参数文件中的名称完全一样。

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![预配状态](media/tutorial-deploy-hsm-powershell/progress-status2.png)

现在也可通过 [Azure 资源浏览器](https://resources.azure.com/)来查看资源。   进入浏览器中以后，请依次展开左侧的“订阅”、专用 HSM 的特定订阅、“资源组”、所使用的资源组，最后选择“资源”项。

## <a name="testing-the-deployment"></a>测试部署

测试部署就是先连接到能够访问 HSM 的虚拟机，然后直接连接到 HSM 设备。 上述操作将确认 HSM 是否可以访问。
ssh 工具用于连接到虚拟机。 命令将如下所示，但使用在参数中指定的管理员名称和 dns 名称。

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

要使用的密码是参数文件中的密码。
登录到 Linux VM 以后，则可使用在门户中发现的资源 \<prefix>hsm_vnic 的专用 IP 地址登录到 HSM。

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
有了 IP 地址以后，请运行以下命令：

`ssh tenantadmin@<ip address of HSM>`

如果成功，系统会提示你输入密码。 默认密码为 PASSWORD。 HSM 会要求你更改密码。因此，请设置一个强密码，并使用组织首选的机制来存储密码，防止丢失。  

>[!IMPORTANT]
>如果丢失此密码，则需重置 HSM，这意味着丢失密钥。

使用 ssh 连接到 HSM 设备以后，请运行以下命令，确保 HSM 可以正常运行。

`hsm show`

输出应如下图所示：

![预配状态](media/tutorial-deploy-hsm-powershell/output.png)

目前已为一个高度可用的双 HSM 型部署分配了所有资源，并验证了访问权限和运行状态。 进一步的配置或测试涉及更多针对 HSM 设备本身的工作。 因此，应该按照《Gemalto Luna 网络 HSM 7 管理指南》第 7 章中的说明操作，以便初始化 HSM 并创建分区。 在 Gemalto 客户支持门户中注册并获得客户 ID 以后，即可直接从 Gemalto 下载所有文档和软件。 下载客户端软件 7.2 版即可获取所有必需的组件。

## <a name="delete-or-clean-up-resources"></a>删除或清理资源

如果已完成 HSM 设备的操作，则可将其作为资源删除，并让其返回到可用池中。 执行该操作时，最需要关注的问题是设备上的敏感客户数据。 若要删除敏感客户数据，应通过 Gemalto 客户端对设备进行出厂重置。 请参阅适用于 SafeNet 网络 Luna 7 设备的 Gemalto 管理员指南，并考虑按顺序执行以下命令。

1. `hsm factoryReset -f`
2. `sysconf config factoryReset -f -service all`
3. `network interface delete -device eth0`
4. `network interface delete -device eth1`
5. `network interface delete -device eth2`
6. `network interface delete -device eth3`
7. `my file clear -f`
8. `my public-key clear -f`
9. `syslog rotate`


> [!NOTE]
> 如果有 Gemalto 设备配置的问题，则应联系 [Gemalto 客户支持](https://safenet.gemalto.com/technical-support/)。

如果已完成此资源组中资源的相关操作，则可使用以下命令将其全部删除：

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>后续步骤

完成本教程中的步骤以后，你就预配好了专用 HSM 资源，可以在虚拟网络中使用它了。 现在可以根据首选部署体系结构的要求，使用更多资源来补充此部署了。 若要详细了解如何进行部署规划，请参阅概念文档。 建议在进行设计时，在主要区域使用两个 HSM 解决机架级可用性问题，在次要区域使用两个 HSM 解决区域可用性问题。 在本教程中使用的模板文件可以轻松地用作双 HSM 型部署的基础，但你需要根据自己的要求对其参数进行修改。

* [高可用性](high-availability.md)
* [物理安全性](physical-security.md)
* [网络](networking.md)
* [监视](monitoring.md)
* [可支持性](supportability.md)
