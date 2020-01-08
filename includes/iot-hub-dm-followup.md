---
title: include 文件
description: include 文件
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558805"
---
## <a name="customize-and-extend-the-device-management-actions"></a>自定义和扩展设备管理操作

IoT 解决方案可扩展已定义的设备管理模式集，或通过使用设备孪生和云到设备方法基元启用自定义模式。 设备管理操作的其他示例包括恢复出厂设置、固件更新、软件更新、电源管理、网络和连接管理以及数据加密。

## <a name="device-maintenance-windows"></a>设备维护时段

通常情况下，将设备配置为在某一时间执行操作，以最大程度减少中断和停机时间。 设备维护时段是一种常用模式，用于定义设备应更新其配置的时间。 后端解决方案使用设备克隆所需属性在设备上定义并激活策略，以启用维护时段。 当设备收到维护时段策略时，它可以使用设备克隆报告属性报告策略状态。 然后，后端应用可以使用设备克隆查询来证明设备和每个策略的符合性。

## <a name="next-steps"></a>后续步骤

本教程使用直接方法触发设备上的远程重新启动。 使用报告属性报告设备上次重新启动时间，并查询设备孪生从云中发现设备上次重新启动时间。

若要继续完成 IoT 中心和设备管理模式（如远程无线固件更新）的入门内容，请参阅[如何更新固件](../articles/iot-hub/tutorial-firmware-update.md)。

若要了解如何扩展 IoT 解决方案并在多个设备上计划方法调用，请参阅[计划和广播作业](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md)。