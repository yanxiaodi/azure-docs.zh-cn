---
title: 使用 PowerShell cmdlet 创建 Azure IoT 中心 | Microsoft Docs
description: 如何使用 PowerShell cmdlet 创建 IoT 中心。
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: fd376728a1ebdf769c7f2ae76d61a60703e13711
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66146615"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>使用 New-AzIotHub cmdlet 创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>简介

可以使用 Azure PowerShell cmdlet 创建和管理 Azure IoT 中心。 本教程介绍如何使用 PowerShell 创建 IoT 中心。

若要完成本操作说明，需要 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>连接到 Azure 订阅

如果使用的是 Cloud Shell，则表示你已登录订阅。 如果改为在本地运行 PowerShell，请输入以下命令以登录 Azure 订阅：

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>创建资源组

需要一个资源组来部署 IoT 中心。 可以使用现有资源组，也可以创建新组。

若要为 IoT 中心创建资源组，请使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) 命令。 此示例在“美国东部”区域中创建名为 **MyIoTRG1** 的资源组：

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>创建 IoT 中心

若要在上一步创建的资源组中创建 IoT 中心，请使用 [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) 命令。 此示例在“美国东部”区域中创建名为 **MyTestIoTHub** 的 **S1** 中心：

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

IoT 中心的名称必须是全局唯一的。

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

可以使用 [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) 命令列出订阅中的所有 IoT 中心：

```azurepowershell-interactive
Get-AzIotHub
```

此示例显示在上一步中创建的 S1 标准 IoT 中心。

可以使用 [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) 命令删除 IoT 中心：

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

或者，可以删除资源组和所有资源，它都包含使用[删除 AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup)命令：

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>后续步骤

现在，已使用 PowerShell cmdlet 部署了 IoT 中心，如果想要进一步探索，请查看以下文章：

* [可用于 IoT 中心的 PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.iothub/)。

* [IoT 中心资源提供程序 REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource)。

若要详细了解如何开发 IoT 中心，请参阅以下文章：

* [C SDK 简介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)
