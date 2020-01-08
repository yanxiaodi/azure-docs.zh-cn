---
title: 从 Azure IoT Central 规则运行多个操作 |Microsoft Docs
description: 从单个 IoT Central 规则运行多个操作, 并创建可从多个规则运行的可重复使用的操作组。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: ad5f660ff72eceecbb6db2e9557b023ed2c6ea99
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875812"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>将多个操作分组, 以从一个或多个规则运行

*本文适用于构建者和管理员。*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

在 Azure IoT Central 中, 可以创建规则以在满足条件时运行操作。 规则基于设备遥测或事件。 例如, 当设备温度超过阈值时, 可以通知操作员。 本文介绍如何使用[Azure Monitor](../azure-monitor/overview.md) *操作组*将多个操作附加到 IoT Central 规则。 可以将操作组附加到多个规则。 [操作组](../azure-monitor/platform/action-groups.md)是 Azure 订阅的所有者定义的通知首选项的集合。

## <a name="prerequisites"></a>先决条件

- 即用即付应用程序
- 用于创建和管理 Azure Monitor 操作组的 Azure 帐户和订阅

## <a name="create-action-groups"></a>创建操作组

可以在 Azure 门户中或使用[Azure 资源管理器模板](../azure-monitor/platform/action-groups-create-resource-manager-template.md)来[创建和管理操作组](../azure-monitor/platform/action-groups.md)。

操作组可以:

- 发送电子邮件通知, 如电子邮件、短信或呼叫。
- 运行操作, 如调用 webhook。

以下屏幕截图显示了一个操作组, 它将发送电子邮件和短信通知并调用 webhook:

![操作组](media/howto-use-action-groups/actiongroup.png)

若要在 IoT Central 规则中使用操作组, 操作组必须与 IoT Central 应用程序位于同一 Azure 订阅中。

## <a name="use-an-action-group"></a>使用操作组

若要在 IoT Central 应用程序中使用操作组, 请首先创建遥测或事件规则。 向规则添加操作时, 请选择**Azure Monitor 操作组**:

![选择操作](media/howto-use-action-groups/chooseaction.png)

从 Azure 订阅中选择操作组:

![选择操作组](media/howto-use-action-groups/chooseactiongroup.png)

选择**保存**。 操作组现在显示在触发规则时要运行的操作的列表中:

![保存的操作组](media/howto-use-action-groups/savedactiongroup.png)

下表总结了发送到支持的操作类型的信息:

| 操作类型 | 输出格式 |
| ----------- | -------------- |
| Email       | 标准 IoT Central 电子邮件模板 |
| 短信         | Azure IoT Central 警报: $ {applicationName}-"$ {ruleName}" 在 $ {triggerDate} $ {triggerTime} 的 "$ {deviceName}" 上触发 |
| 语音       | Azure i-o Central 警报: 在应用程序 $ {applicationName} 的 $ {triggerDate} $ {triggerTime} 上, 在设备 "$ {deviceName}" 上触发的规则 "$ {ruleName}" |
| Webhook     | { "schemaId" :"AzureIoTCentralRuleWebhook", "data": {[常规 webhook 负载](#payload)}} |

以下文本是操作组中的一个示例 SMS 消息:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a>以下 JSON 显示了一个示例 webhook 操作有效负载:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何结合使用操作组和规则, 接下来是了解如何[管理你的设备](howto-manage-devices.md)。
