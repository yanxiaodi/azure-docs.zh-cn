---
title: Azure IoT 中心设备预配服务中的服务概念 | Microsoft Docs
description: 介绍服务预配概念，特定于使用设备预配服务和 IoT 中心的设备
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 51486da6b34c0ff1e9b6d05558c2132a416913e9
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104367"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>IoT 中心设备预配服务概念

IoT 中心设备预配服务是一项 IoT 中心帮助程序服务，该服务用于将零接触设备预配到指定 IoT 中心。 使用设备预配服务，可以通过安全且可缩放的方式[自动预配](concepts-auto-provisioning.md)数百万台设备。

设备预配是一个两部分过程。 第一部分是通过注册设备来建立设备和 IoT 解决方案之间的初始连接。 第二部分是根据解决方案的具体要求将适当的配置应用于设备。 在这两个步骤都完成后，设备已完全预配。 设备预配服务自动执行这两个步骤，为设备提供无缝的预配体验。

本文概述了最适用于管理服务的预配概念。 本文与设备部署准备工作的[云设置步骤](about-iot-dps.md#cloud-setup-step)中提及的角色最为相关。

## <a name="service-operations-endpoint"></a>服务操作终结点

服务操作终结点是用于管理服务设置和维护注册列表的终结点。 此终结点仅由服务管理员使用，设备不使用它。

## <a name="device-provisioning-endpoint"></a>设备预配终结点

设备预配终结点是单一终结点，所有设备都使用它进行自动预配。 此 URL 对于所有预配服务实例都是相同 ，因而无需使用供应链方案中的新连接信息来刷新设备。 ID 范围可确保租户隔离。

## <a name="linked-iot-hubs"></a>链接的 IoT 中心

设备预配服务只能将设备预配到已链接到它的 IoT 中心。 将 IoT 中心链接到设备预配服务实例可以为 IoT 中心的设备注册表提供服务读/写权限；通过该链接，设备预配服务可以注册设备 ID 并在设备孪生中设置初始配置。 链接 IoT 中心可能位于任何 Azure 区域。 可将其他订阅中的中心链接到预配服务。

## <a name="allocation-policy"></a>分配策略

用于确定设备预配服务如何将设备分配给 IoT 中心的服务级别设置。 支持三种分配策略：

* **均匀加权分发**：链接的 IoT 中心等可能地获得预配到它们的设备。 默认设置。 如果只将设备预配到一个 IoT 中心，则可以保留此设置。

* **最低延迟**：将设备预配到具有最低延迟的 IoT 中心。 如果多个链接 IoT 中心提供相同的最低延迟，则预配服务将在这些中心上散列设备

* **通过注册列表进行静态配置**：注册列表中所需 IoT 中心的规范优先于服务级别分配策略。

## <a name="enrollment"></a>注册

注册是指可以通过自动预配注册的设备或设备组的记录。 注册记录包含有关设备或设备组的信息，包括：
- 设备使用的[证明机制](concepts-security.md#attestation-mechanism)
- 可选的初始所需配置
- 所需的 IoT 中心
- 所需的设备 ID

设备预配服务支持两种类型的注册：

### <a name="enrollment-group"></a>注册组

注册组是一组共享特定证明机制的设备。 注册组支持 x.509 和对称。 X.509 注册组中的所有设备都提供已由同一根或中间证书颁发机构（CA）签名的 x.509 证书。 对称密钥注册组中的每个设备都提供派生自组对称密钥的 SAS 令牌。 注册组名称和证书名称必须是小写的字母数字，并可包含连字符。

> [!TIP]
> 建议对共享所需初始配置的大量设备，或者全部转到同一租户的设备使用注册组。

### <a name="individual-enrollment"></a>单独注册

单独注册是用于可注册的单一设备的条目。 个人注册可使用 X.509 叶证书或 SAS 令牌（来自物理或虚拟 TPM）作为证明机制。 单独注册中的注册 ID 是小写的字母数字，并且可包含连字符。 单独注册可能会指定所需 IoT 中心设备 ID。

> [!TIP]
> 对于需要唯一初始配置的设备或仅能通过 TPM 证明使用 SAS 令牌进行身份验证的设备，建议为其使用个人注册。

## <a name="registration"></a>注册

注册是设备通过设备预配服务成功注册/预配到 IoT 中心的记录。 注册记录自动创建，可以删除，但不能更新。

## <a name="operations"></a>操作

操作是设备预配服务的计费单位。 成功完成到服务的一条指令即为一次操作。 操作包括设备注册和重新注册，还包括服务侧更改（例如添加注册列表条目和更新注册列表条目）。
