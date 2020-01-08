---
title: Azure IoT 中心从设备到云选项 | Microsoft Docs
description: 开发人员指南 - 指导用户何时使用从设备到云的消息、报告属性或文件上传，以进行从云到设备的通信。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fffa064b912a96b05feb901d1d2d44533c4681b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60885510"
---
# <a name="device-to-cloud-communications-guidance"></a>从设备到云通信指南

将信息从设备应用发送到解决方案后端时，IoT 中心会公开三个选项：

* [设备到云消息](iot-hub-devguide-messages-d2c.md)，用于时序遥测和警报。

* [设备孪生的报告属性](iot-hub-devguide-device-twins.md)，用于报告设备状态信息，例如可用功能、条件或长时间运行的工作流的状态。 例如，配置和软件更新。

* [文件上传](iot-hub-devguide-file-upload.md)，用于由间歇性连接的设备上传的或为了节省带宽而压缩的媒体文件和大型遥测批文件。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

下面是各种设备到云通信选项的详细比较。

|  | 设备到云的消息 | 设备克隆的报告属性 | 文件上传 |
| ---- | ------- | ---------- | ---- |
| 场景 | 遥测时序和警报。 例如，每隔 5 分钟发送 256-KB 的传感器数据批。 | 可用功能和条件。 例如，当前设备连接模式，诸如手机网络或 WiFi。 同步长时间运行的工作流，如配置和软件更新。 | 媒体文件。 大型（通常为压缩的）遥测批。 |
| 存储和检索 | IoT 中心的临时存储，最多可保存 7 天。 仅顺序读取。 | 由 IoT 中心存储在设备孪生中。 可使用 [IoT 中心查询语言](iot-hub-devguide-query-language.md)进行检索。 | 存储在用户提供的 Azure 存储帐户中。 |
| 大小 | 消息大小最大为 256-KB。 | 报告属性大小最大为 8 KB。 | Azure Blob 存储支持的最大文件大小。 |
| 频率 | 高。 有关详细信息，请参阅 [IoT 中心限制](iot-hub-devguide-quotas-throttling.md)。 | 中。 有关详细信息，请参阅 [IoT 中心限制](iot-hub-devguide-quotas-throttling.md)。 | 低。 有关详细信息，请参阅 [IoT 中心限制](iot-hub-devguide-quotas-throttling.md)。 |
| Protocol | 适用于所有协议。 | 使用 MQTT 或 AMQP 时可用。 | 在使用任何协议时可用，但设备上必须具备 HTTPS。 |

应用程序可能需要同时将信息作为遥测时序或警报发送，并且使其在设备孪生中可用。 在这种情况下，可以选择以下选项之一：

* 设备应用发送一条设备到云消息并报告属性更改。
* 解决方案后端在收到消息时可以将信息存储在设备孪生的标记中。

由于设备到云消息允许的吞吐量远高于设备孪生更新，因此有时需要避免为每条设备到云消息更新设备孪生。