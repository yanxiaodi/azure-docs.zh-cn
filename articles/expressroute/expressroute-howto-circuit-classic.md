---
title: 修改 ExpressRoute 线路：PowerShell：Azure 经典部署模型 | Microsoft Docs
description: 本文逐步讲解检查状态以及更新或删除并预配 ExpressRoute 经典部署模型线路的步骤。
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: ganesr
ms.reviewer: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 7468338e7bc39128564e71831abe61bb1714ff72
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849243"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>使用 PowerShell 修改 ExpressRoute 线路（经典）

> [!div class="op_single_selector"]
> * [Azure 门户](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager 模板](expressroute-howto-circuit-resource-manager-template.md)
> * [视频 - Azure 门户](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell（经典）](expressroute-howto-circuit-classic.md)
>

本文逐步讲解检查状态以及更新或删除并预配 ExpressRoute 经典部署模型线路的步骤。 本文适用于经典部署模型。

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**关于 Azure 部署模型**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>开始之前

安装最新版本的 Azure 服务管理 (SM) PowerShell 模块和 ExpressRoute 模块。  使用以下示例时，请注意，当更新版本的 cmdlet 发布时，版本号（在此示例中为 5.1.1）将更改。

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

如果需要有关 Azure PowerShell 的更多信息，请参阅 [Azure PowerShell cmdlet 入门](/powershell/azure/overview)来获取有关如何配置计算机以使用 Azure PowerShell 模块的分步指导。

若要登录到 Azure 帐户，请使用以下示例：

1. 使用提升的权限打开 PowerShell 控制台，并连接到帐户。 使用下面的示例来帮助连接：

   ```powershell
   Connect-AzAccount
   ```
2. 检查该帐户的订阅。

   ```powershell
   Get-AzSubscription
   ```
3. 如果有多个订阅，请选择要使用的订阅。

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. 接下来，使用以下 cmdlet 将 Azure 订阅添加到经典部署模型的 PowerShell。

   ```powershell
   Add-AzureAccount
   ```

## <a name="get-the-status-of-a-circuit"></a>获取线路的状态

可以随时使用 `Get-AzureCircuit` cmdlet 检索此信息。 进行不带任何参数的调用将列出所有线路。

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

可以将服务密钥作为参数传递给调用，从而获取特定 ExpressRoute 线路的相关信息。

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

可以运行以下示例获取所有这些参数的详细说明：

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>修改线路

可以在不影响连接的情况下修改 ExpressRoute 线路的某些属性。

可以在不停机的情况下执行以下任务：

* 为 ExpressRoute 线路启用或禁用 ExpressRoute 高级版外接程序。
* 增加 ExpressRoute 线路的带宽，前提是端口上有可用容量。 不支持对线路的带宽进行降级。
* 将计量套餐从数据流量套餐更改为无限制流量套餐。 不支持将计量套餐从无限制流量套餐更改为数据流量套餐。
* 可以启用和禁用允许经典操作  。

有关限制和局限性的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。

### <a name="enable-the-expressroute-premium-add-on"></a>启用 ExpressRoute 高级版外接程序

可以使用以下 PowerShell cmdlet 为现有线路启用 ExpressRoute 高级版外接程序：

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

线路现已启用 ExpressRoute 高级版外接程序功能。 成功运行该命令后，就会开始对高级版外接程序功能计费。

### <a name="disable-the-expressroute-premium-add-on"></a>禁用 ExpressRoute 高级版外接程序

> [!IMPORTANT]
> 如果使用的资源超出了标准线路允许的范围，此操作可能会失败。
>
>

#### <a name="considerations"></a>注意事项

* 从高级版降级到标准版之前，请确保链接到线路的虚拟网络数少于 10。 否则，更新请求会失败，并且会按高级版费率计费。
* 必须取消其他地理政治区域的所有虚拟网络的链接。 否则，更新请求会失败，并且会按高级版费率计费。
* 路由表中专用对等互连的路由必须少于 4,000。 如果路由表大小超出 4,000 个路由，则会删除 BGP 会话且不会重新启用它，除非已播发前缀的数目低于 4,000。

#### <a name="to-disable-the-premium-add-on"></a>禁用高级外接程序

可以使用以下 PowerShell cmdlet 为现有线路禁用 ExpressRoute 高级版外接程序：

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>更新 ExpressRoute 线路带宽

有关提供商支持的带宽选项，请查看 [ExpressRoute 常见问题](expressroute-faqs.md)。 只要在其上创建线路的物理端口允许，即可选取大于现有线路大小的任何大小。

> [!IMPORTANT]
> 如果现有端口上的容量不足，可能需要重新创建 ExpressRoute 线路。 如果该位置没有额外的可用容量，则不能升级线路。
>
> 但是，无法在不中断的情况下降低 ExpressRoute 线路的带宽。 带宽降级需要取消对 ExpressRoute 线路的预配，并重新预配新的 ExpressRoute 线路。
>
>

#### <a name="resize-a-circuit"></a>调整线路大小

确定所需的大小后，即可使用以下命令调整线路的大小：

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

在 Microsoft 端调整线路大小后，必须联系连接提供商，让他们在那一边根据此更改更新配置。 从现在开始将按已更新的带宽选项计费。

如果在增加线路带宽时看到以下错误，这意味着创建现有线路所在的物理端口上没有足够的带宽可用。 必须删除此线路，并创建所需大小的新线路。

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>取消预配和删除线路

### <a name="considerations"></a>注意事项

* 必须取消所有虚拟网络与 ExpressRoute 线路的链接，此操作才能成功。 如果此操作失败，请查看是否有虚拟网络链接到了此线路。
* 如果 ExpressRoute 线路服务提供商预配状态为“正在预配”  或“已预配”  ，则必须与服务提供商合作，在他们一端取消预配线路。 在服务提供商完成取消设置线路并通知我们之前，我们会继续保留资源并向你收费。
* 如果服务提供商已取消预配线路（服务提供商预配状态设置为“未预配”  ），则可以删除线路。 这样就会停止对线路的计费。

#### <a name="delete-a-circuit"></a>删除线路

可以通过运行以下命令来删除 ExpressRoute 线路：

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
