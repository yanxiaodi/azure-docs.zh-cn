---
title: Azure Maps 中的消耗模型 | Microsoft Docs
description: 了解 Azure Maps 中的消耗模型
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5f75f656312c11a4668ca9ef9fe7b2a61a7d13e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60797896"
---
# <a name="consumption-model"></a>消耗模型

联机路由提供了一组参数，可详细描述特定车辆的消耗模型。
支持以下两种主要消耗模型（具体取决于“vehicleEngineType”的值）：  “燃烧”和“电气”   。 在同一请求中指定属于不同模型的参数是错误做法。
消耗模型不能与 travelMode 值 “bicycle”和“pedestrian”一起使用    。

## <a name="parameter-constraints-for-consumption-model"></a>消耗模型的参数约束

在两种消耗模型中，显式指定某些参数时，还需指定部分其他参数。 这些依赖关系是：

* 所有参数均要求用户指定 constantSpeedConsumption  。 如果未指定“constantSpeedConsumption”，则除“vehicleWeight”外，指定任何其他消耗模型参数都是错误的   。
* 始终必须一起指定（即指定两者或不指定）accelerationEfficiency 和 decelerationEfficiency   。
* 如果指定了 accelerationEfficiency 和 decelerationEfficiency，两者值的乘积不得大于 1（防止出现永恒运动）   。
* 始终必须一起指定（即指定两者或不指定）uphillEfficiency 和 downhillEfficiency   。
* 如果指定了 uphillEfficiency 和 downhillEfficiency，两者值的乘积不得大于 1（防止出现永恒运动）   。
* 如果用户指定了 \*Efficiency 参数，还必须指定“vehicleWeight”参数   。 如果 vehicleEngineType 为燃料，则还必须指定 fuelEnergyDensityInMJoulesPerLiter    。
* 始终必须一起指定（即指定两者或不指定） maxChargeInkWh 和 currentChargeInkWh   。

> [!NOTE]
> 如果仅指定了 constantSpeedConsumption，消耗计算不会考虑其他任何消耗（如爬坡和车辆加速）  。

## <a name="combustion-consumption-model"></a>燃料消耗模型

如果将 vehicleEngineType 设置为“燃烧”，则使用燃烧消耗模型   。
下面是属于此模型的参数列表。 参阅“参数”部分，获取详细说明。

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>电力消耗模型

如果将 vehicleEngineType 设置为“电气”，则使用电气消耗模型   。
下面是属于此模型的参数列表。 参阅“参数”部分，获取详细说明。

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>合理的消耗参数值

尽管一组特定消耗参数可能满足上面指定的所有显式需求，但该组参数仍可能遭到拒绝。 如果某个特定参数值或多个参数值组合被认为会造成不合理的消耗值，则会出现此情况。 如果出现此情况，则很可能是存在输入错误，应当注意调整所有合理的消耗参数值。 如果一组特定消耗参数遭到拒绝，随之而来的错误消息会解释其原因。
参数的详细说明中包含两种模型的合理值的示例。
