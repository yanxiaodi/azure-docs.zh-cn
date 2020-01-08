---
title: Azure IoT Central 中的体系结构概念 | Microsoft Docs
description: 本文介绍与 Azure IoT Central 的体系结构相关的重要概念
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 43357bdeb444fed20f29107d10dc31a61857fccf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877502"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central 体系结构

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

本文概述 Microsoft Azure IoT Central 体系结构。

![顶层体系结构](media/concepts-architecture/architecture.png)

## <a name="devices"></a>设备

设备与 Azure IoT Central 应用程序交换数据。 设备可以：

- 发送度量，例如遥测。
- 与应用程序同步设置。

在 Azure IoT Central 中，设备可以与应用程序交换的数据在设备模板中指定。 有关设备模板的详细信息，请参阅[元数据管理](#metadata-management)。

若要详细了解设备如何连接到 Azure IoT Central 应用程序，请参阅[设备连接](concepts-connectivity.md)。

## <a name="cloud-gateway"></a>云网关

Azure IoT Central 使用 Azure IoT 中心作为启用设备连接的云网关。 IoT 中心允许：

- 在云中进行大规模数据引入。
- 设备管理。
- 安全的设备连接。

若要详细了解 IoT 中心，请参阅 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/)。

若要详细了解 Azure IoT Central 中的设备连接，请参阅[设备连接](concepts-connectivity.md)。

## <a name="data-stores"></a>数据存储

Azure IoT Central 在云中存储应用程序数据。 存储的应用程序数据包括：

- 设备模板。
- 设备标识。
- 设备元数据。
- 用户和角色数据。

Azure IoT Central 将时序存储用于从设备发送的度量数据。 设备提供的时序数据供分析服务使用。

## <a name="analytics"></a>分析

分析服务负责生成应用程序显示的自定义报告数据。 操作员可以[自定义在应用程序中显示的分析](howto-create-analytics.md)。 分析服务在 [Azure 时序见解](https://azure.microsoft.com/services/time-series-insights/)基础上构建，可以处理从设备发送的度量数据。

## <a name="rules-and-actions"></a>规则和操作

[规则和操作](howto-create-telemetry-rules.md)可以紧密地配合使用，以便自动完成应用程序中的任务。 开发者可以根据设备遥测（例如温度超过定义的阈值）来定义规则。 Azure IoT Central 使用流处理器来确定何时满足规则条件。 规则条件在得到满足的情况下，会触发开发者定义的操作。 例如，可以通过某个操作向工程师发送通知电子邮件，告知对方设备中的温度过高。

## <a name="metadata-management"></a>元数据管理

在 Azure IoT Central 应用程序中，设备模板用于定义设备类型的行为和功能。 例如，致冷器设备模板指定致冷器发送给应用程序的遥测。

![模板体系结构](media/concepts-architecture/template_architecture.png)

在设备模板中：

- **度量**指定设备发送给应用程序的遥测。
- **设置**指定操作员可以设置的配置。
- **属性**指定操作员可以设置的元数据。
- **规则**根据从设备发送的数据自动设置应用程序中的行为。
- **仪表板**是可以在应用程序中自定义的设备视图。

一个应用程序可以有一个或多个基于每个设备模板的模拟设备和真实设备。

## <a name="data-export"></a>数据导出

在 Azure IoT Central 应用程序中, 可以将[数据连续导出](howto-export-data-event-hubs-service-bus.md)到自己的 Azure 事件中心和 Azure 服务总线实例。 还可以定期将数据导出到 Azure Blob 存储帐户。 IoT Central 可以导出度量、设备和设备模板。

## <a name="batch-device-updates"></a>批处理设备更新

在 Azure IoT Central 应用程序中, 可以[创建和运行作业](howto-run-a-job.md)来管理连接设备。 通过这些作业, 可以对设备属性或设置进行大容量更新或运行命令。 例如, 可以创建一个作业来提高多个 refrigerated 自动售货机机的风扇速度。

## <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)

[管理员可以使用预定义的角色定义适用于 Azure IoT Central 应用程序的访问规则](howto-administer.md)。 管理员可以通过为用户分配角色来决定用户可以访问的应用程序的具体区域。

## <a name="security"></a>安全性

Azure IoT Central 中的安全功能包括：

- 对传输中的和静止时的数据加密。
- 通过 Azure Active Directory 或 Microsoft 帐户提供身份验证。 支持双重身份验证。
- 完全的租户隔离。
- 设备级别安全性。

## <a name="ui-shell"></a>UI Shell

UI Shell 是一个现代的基于 HTML5 浏览器的应用程序，响应速度快。
管理员可以通过应用自定义主题和修改帮助链接来自定义应用程序的 UI, 以指向您自己的自定义帮助资源。 若要了解有关 UI 自定义的详细信息, 请参阅[自定义 Azure IOT CENTRAL UI](howto-customize-ui.md)一文。

操作员可以创建个性化的应用程序仪表板。 您可以有多个仪表板, 用于显示不同的数据并在它们之间切换。

## <a name="next-steps"></a>后续步骤

现在, 你已了解 Azure IoT Central 的体系结构, 接下来要介绍 Azure IoT Central 中的[设备连接](concepts-connectivity.md)。