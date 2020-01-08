---
title: 使用适用于 Azure CLI 的 IoT 扩展进行 Azure IoT 设备管理 | Microsoft Docs
description: 使用适用于 Azure CLI 的 IoT 扩展工具进行 Azure IoT 中心设备管理，特点是使用直接方法并提供孪生所需的属性管理选项。
author: chrissie926
manager: ''
keywords: azure iot 设备管理, azure iot 中心设备管理, 设备管理 iot, iot 中心设备管理
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 93efd6e53470fb78bb6d823652437e7a37c33732
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640572"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>针对 Azure IoT 中心设备管理，使用适用于 Azure CLI 的 IoT 扩展

![端到端关系图](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)是一个新的开源 IoT 扩展, 它将添加到[Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)的功能中。 Azure CLI 包含用于与 Azure 资源管理器和管理终结点进行交互的命令。 例如，可使用 Azure CLI 创建 Azure VM 或 IoT 中心。 CLI 扩展使 Azure 服务能够扩展 Azure CLI，从而可访问其他特定于服务的功能。 IoT 扩展为 IoT 开发人员提供了对所有 IoT 中心、IoT Edge 和 IoT 中心设备预配服务功能的命令行访问。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 管理选项          | 任务  |
|----------------------------|-----------|
| 直接方法             | 让设备执行操作，如开始或停止发送消息或重新启动设备。                                        |
| 孪生所需属性    | 让设备进入特定状态，例如将 LED 设置为绿色，或将遥测发送间隔设置为 30 分钟。         |
| 孪生报告属性   | 获取报告的设备状态。 例如，设备报告 LED 现在正在闪烁。                                    |
| 孪生标记                  | 将设备特定的元数据存储在云中。 例如，存储在自动售货机的部署位置。                         |
| 设备孪生查询        | 查询所有设备孪生, 以使用任意条件检索这些孪生, 如确定可供使用的设备。 |

有关这些选项的差异和使用指导的更详细说明，请参阅[设备到云通信指南](iot-hub-devguide-d2c-guidance.md)和[云到设备通信指南](iot-hub-devguide-c2d-guidance.md)。

设备孪生是存储设备状态信息（元数据、配置和条件）的 JSON 文档。 IoT 中心为连接到它的每台设备保留一个设备孪生。 有关设备孪生的详细信息，请参阅[设备孪生入门](iot-hub-node-node-twin-getstarted.md)。

## <a name="what-you-learn"></a>学习内容

你将了解如何将 IoT 扩展用于适用于你的开发计算机上的各种管理选项的 Azure CLI。

## <a name="what-you-do"></a>准备工作

使用各种管理选项运行 Azure CLI 和适用于 Azure CLI 的 IoT 扩展。

## <a name="what-you-need"></a>所需条件

* 完成 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或其中一个设备教程；例如[将 Raspberry Pi 与 Node.js 配合使用](iot-hub-raspberry-pi-kit-node-get-started.md)。 这些项涵盖以下要求:

  - 一个有效的 Azure 订阅。
  - 已在订阅中创建一个 Azure IoT 中心。
  - 一个可向 Azure IoT 中心发送消息的客户端应用程序。

* 在学习本教程期间，确保设备与客户端应用程序均处于运行状态。

* [Python 2.7x 或 Python 3.x](https://www.python.org/downloads/)

<!-- I'm not sure we need all this info, so comment out this include for now. Robin 7.26.2019
[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)] -->

* Azure CLI。 如需进行安装，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 Azure CLI 版本必须至少是 2.0.24 或更高版本。 请使用 `az –version` 验证版本。

* 安装 IoT 扩展。 最简单的方法是运行 `az extension add --name azure-cli-iot-ext`。 [IoT 扩展自述文件](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md)介绍了该扩展的多种安装方法。

## <a name="sign-in-to-your-azure-account"></a>登录到 Azure 帐户

通过运行以下命令登录到 Azure 帐户：

```bash
az login
```

## <a name="direct-methods"></a>直接方法

```bash
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>设备孪生所需属性

通过运行以下命令将所需属性间隔设置为 3000：

```bash
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

可从设备读取此属性。

## <a name="device-twin-reported-properties"></a>设备孪生报告属性

通过运行以下命令获取报告的设备属性：

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

其中一个克隆的已报告属性是 $metadata。 $lastUpdated, 它显示设备应用程序更新其报告属性集的最后时间。

## <a name="device-twin-tags"></a>设备孪生标记

通过运行以下命令显示设备的标记和属性：

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

通过运行以下命令向设备添加字段角色 = 温度和湿度：

```bash
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>设备孪生查询

通过运行以下命令查询角色标记 =“温度和湿度”的设备：

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

通过运行以下命令查询除角色标记 =“温度和湿度”的设备以外的所有设备：

```bash
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>后续步骤

现在，已了解如何监视设备到云的消息，以及在 IoT 设备与 Azure IoT 中心之间发送云到设备的消息。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
