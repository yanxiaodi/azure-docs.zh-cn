---
title: 了解 Azure IoT Central 应用的设备模板版本控制 | Microsoft Docs
description: 通过创建新版本循环访问设备模板，不影响实时连接设备
author: sandeeppujar
ms.author: sandeepu
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 155f392410c5722a28ba09acafc1480e72586773
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100911"
---
# <a name="create-a-new-device-template-version"></a>创建新设备模板版本

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Azure IoT Central 用于快速开发 IoT 应用程序。 可以通过添加、编辑或删除度量、设置或属性来快速地循环访问设备模板设计。 对于当前的连接设备，这其中的某些更改可能是侵入性的。 Azure IoT Central 可以标识这些中断性变更，并且可以通过某种方式将这些更新安全地部署到设备。

设备模板在创建后会有一个版本号。 版本号默认为 1.0.0。 如果在编辑设备模板时，所做的更改可能影响实时连接设备，Azure IoT Central 会提示创建新的设备模板版本。

> [!NOTE]
> 若要详细了解如何创建设备模板，请参阅[设置设备模板](howto-set-up-template.md)

## <a name="changes-that-prompt-a-version-change"></a>会提示进行版本更改的更改

通常情况下，对设备模板的设置或属性进行的更改会提示进行版本更改。

> [!NOTE]
> 在没有设备处于连接状态或最多只有一个设备处于连接状态的情况下，对设备模板进行更改不会提示创建新版本。

以下列表介绍了可能需要新版本的用户操作：

* 属性（必需）
    * 添加或删除必需属性
    * 更改属性的字段名称，该字段名称可供设备用来发送消息。
*  属性（可选）
    * 删除可选属性
    * 更改属性的字段名称，该字段名称可供设备用来发送消息。
    * 将可选属性更改为必需属性
*  设置
    * 添加或删除设置
    * 更改设置的字段名称，该字段名称可供设备用来发送和接收消息。

## <a name="what-happens-on-version-change"></a>进行版本更改时会发生什么？

进行版本更改时，规则和设备仪表板会发生什么？

先前版本的设备模板上的**规则**将继续工作。 规则不会自动迁移到新的设备模板版本。 你可以像平常一样在新的模板版本上创建规则。 有关详细信息, 请参阅在[Azure IoT Central 应用程序操作指南文章中创建遥测规则并设置通知](howto-create-telemetry-rules.md)。

**设备仪表板**可能包含多种类型的磁贴。 某些磁贴可能包含设置和属性。 删除在某个磁贴中使用的属性或设置时，该磁贴会遭到完全破坏或部分破坏。 可以转到该磁贴并修复问题，方法是删除磁贴或更新磁贴的内容。

## <a name="migrate-a-device-across-device-template-versions"></a>跨设备模板版本迁移设备

可以创建多个版本的设备模板。 一段时间过后，会有多个使用这些设备模板的连接设备。 可以将设备从设备模板的一个版本迁移到另一个版本。 以下步骤介绍如何迁移设备：

1. 请参阅**Device Explorer**页面。
1. 选择需迁移到另一版本的设备。
1. 选择“迁移设备”。
1. 选择需将设备迁移到其中的版本号，然后选择“迁移”。

![如何迁移设备](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中使用设备模板版本后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [如何创建遥测规则](howto-create-telemetry-rules.md)