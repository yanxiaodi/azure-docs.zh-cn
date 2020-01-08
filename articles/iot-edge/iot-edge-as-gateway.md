---
title: 用于下游设备的网关 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 创建一个透明、不透明或代理网关设备，以将数据从多个下游设备发送到云或在本地对其进行处理。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c3a49c4333838652f7063d6a89cfd8cceace1cf8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054202"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>如何将 IoT Edge 设备用作网关

在 IoT Edge 解决方案中的网关提供设备连接和边缘分析，否则不会有这些功能的 IoT 设备。 Azure IoT Edge 可用于满足 IoT 网关的所有需求，无论它们是否与连接、标识或边缘分析相关。 本文中的网关模式仅指下游设备连接和设备标识的特征，而不是指在网关上处理设备数据的方式。

## <a name="patterns"></a>模式

将 IoT Edge 设备用作网关有三种模式：透明、协议转换和标识转换：
* 透明  – 理论上可以连接到 IoT 中心的设备可以改为连接到网关设备。 下游设备有其自己的 IoT 中心标识，并将使用任一 MQTT、AMQP 或 HTTP 协议。 网关只是在设备与 IoT 中心之间传递通信。 设备不知道它们与网关，通过云进行通信和与 IoT 中心的设备交互的用户不会意识到中间网关设备。 因此，网关是透明的。 请参阅[创建透明网关](how-to-create-transparent-gateway.md)，了解有关将 IoT Edge 设备用作透明网关的详细信息。
* **协议转换** - 也称为不透明网关模式，不支持 MQTT、AMQP 或 HTTP 的设备可以使用网关设备以它们的名义将数据发送到 IoT 中心。 在网关能够理解下游设备使用的协议和是唯一的标识在 IoT 中心的设备。 所有信息好像都来自一台设备，即网关。 如果云应用程序想要以设备位单位分析数据，则下游设备就必须在其消息中嵌入额外的标识信息。 此外，IoT 中心基元（例如孪生和方法）仅适用于网关设备，而不适用于下游设备。
* **标识转换** - 无法连接到 IoT 中心的设备可以改为连接到网关设备。 网关代表下游设备提供 IoT 中心标识和协议转换。 网关非常智能，它能够理解下游设备使用的协议，为其提供标识，并转换 IoT 中心基元。 下游设备作为一流设备出现在 IoT 中心，随附克隆和方法。 用户可以与 IoT 中心的设备进行交互，但并觉察不到中间网关设备。

![关系图 - 透明、协议和标识网关模式](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>用例
所有网关模式提供以下优势：
* **在边缘分析**– 使用 AI 服务本地来处理来自下游设备，而无需向云发送完全保真的遥测数据。 本地查找和响应见解，并仅将一部分数据发送到 IoT 中心。 
* 下游设备隔离  – 网关设备可以屏蔽所有下游设备，而不对 Internet 公开。 它可以位于无连接的 OT 网络和提供 Web 访问权限的 IT 网络之间。 
* **连接多路复用** - 通过 IoT Edge 网关连接到 IoT 中心的所有设备使用同一个基础连接。
* 流量平滑  - 在本地保存消息的同时，如果 IoT 中心对流量进行限制，IoT Edge 设备将自动执行指数回退。 此优点使解决方案能灵活应对流量高峰。
* **脱机支持**-网关设备存储的消息和成对更新不能传递到 IoT 中心。

此外，执行协议转换的网关还可以对现有设备和资源受限的新设备执行边缘分析、设备隔离、流量平滑和脱机支持。 许多现有设备将生成能够为企业提供见解的数据；然而，它们的设计并未考虑云连接。 不透明网关允许解锁和 IoT 解决方案中使用此数据。

实现标识转换的网关提供了协议转换的好处，并且还允许从云完全管理下游设备。 IoT 解决方案中的所有设备都显示在 IoT 中心内，不管它们使用的是什么协议。

## <a name="cheat-sheet"></a>备忘单
下面一个速查表，用于在使用透明、不透明（协议）和代理网关时比较 loT 中心基元。

| &nbsp; | 透明网关 | 协议转换 | 标识转换 |
|--------|-------------|--------|--------|
| 存储在 IoT 中心标识注册表中的标识 | 所有已连接的设备的标识 | 仅网关设备的标识 | 所有已连接的设备的标识 |
| 设备孪生 | 每个已连接的设备均有自己的设备孪生 | 仅网关具有设备和模块孪生 | 每个已连接的设备均有自己的设备孪生 |
| 直接方法和云到设备的消息 | 云可以对每个已连接的设备单独寻址 | 云只能对网关设备寻址 | 云可以对每个已连接的设备单独寻址 |
| [IoT 中心限制和配额](../iot-hub/iot-hub-devguide-quotas-throttling.md) | 适用于每个设备 | 适用于网关设备 | 适用于每个设备 |

使用不透明网关（协议转换）模式时，通过该网关连接的所有设备共享同一个可包含最多 50 条消息的云到设备的队列。 它遵循的原则是，仅当很少设备通过各字段网关进行连接以及云到设备的流量较低时，才使用不透明网关模式。

## <a name="next-steps"></a>后续步骤

了解如何设置透明网关： 

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [到 Azure IoT 中心下游设备进行身份验证](how-to-authenticate-downstream-device.md)
* [下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)