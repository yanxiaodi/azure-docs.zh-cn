---
title: IoT DevKit 到云 -- 将 IoT MXChip DevKit 连接到 Azure IoT 中心 | Microsoft Docs
description: 本教程介绍了如何将 IoT DevKit AZ3166 上的传感器的状态发送到 Azure IoT 远程监视解决方案加速器。
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: ae8dc263e08528c6e3b3bae8c779162c96d51f43
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61324242"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>将 MXChip IoT DevKit 连接到 Azure IoT 远程监视解决方案加速器

本教程介绍了如何在 DevKit 上运行示例应用，以便将传感器数据发送到 Azure IoT 远程监视解决方案加速器。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是具有多种外设和传感器的集成 Arduino 兼容板。 可以使用[适用于 Arduino 的 Visual Studio Code 扩展](https://aka.ms/arduino)针对其进行开发。 它附带了一个不断增长的[项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)，指导你构建物联网 (IoT) 解决方案的原型，以利用 Microsoft Azure 服务。

## <a name="what-you-need"></a>所需条件

完成[入门指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)来实现以下目的：

* 将 DevKit 连接到 Wi-Fi
* 准备开发环境

一个有效的 Azure 订阅。 如果没有订阅，可以通过以下两种方法之一进行注册：

* 激活 [30 天免费试用版 Microsoft Azure 帐户](https://azure.microsoft.com/free/)

* 声明你的 [Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)（如果你是 MSDN 或 Visual Studio 订阅者）

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>创建 Azure IoT 远程监视解决方案加速器

1. 转到 [Azure IoT 解决方案加速器站点](https://www.azureiotsolutions.com/)并单击“创建新的解决方案”  。

   ![选择 Azure IoT 解决方案加速器类型](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > 默认情况下，此示例将在创建一个 IoT 远程监视解决方案加速器后创建 S2 IoT 中心。 如果此 IoT 中心不用于大量设备，强烈建议你将其从 S2 降级到 S1，并且在不再需要 IoT 远程监视解决方案加速器时将其删除，以便也可以删除相关的 IoT 中心。 

2. 选择“远程监视”  。

3. 输入解决方案名称，选择订阅和区域，然后单击“创建解决方案”。  解决方案可能要花费一段时间才能完成预配。
  
   ![创建解决方案](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. 在预配完成后，单击“启动”。  在预配过程中会为解决方案创建一些模拟设备。 可以单击 **设备** 来了解它们。

   ![仪表板](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![控制台](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. 单击“添加设备”  。

6. 对于“自定义设备”，单击“新增”。  
  
   ![添加新设备](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. 单击“让我定义我自己的设备 ID”  ，输入 `AZ3166`，然后单击“创建”。 
  
   ![使用 ID 创建设备](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. 记下 **IoT 中心主机名**，然后单击“完成”。 

## <a name="open-the-remotemonitoring-sample"></a>打开 RemoteMonitoring 示例

1. 如果 DevKit 已连接，将其从计算机断开连接。

2. 启动 VS Code。

3. 将 DevKit 连接到计算机。 VS Code 将自动检测 DevKit 并打开以下页面：

   * DevKit 简介页面。
   * Arduino 示例：DevKit 入门练习示例。

4. 展开左侧的“ARDUINO 示例”  部分，浏览到  “MXCHIP AZ3166 的示例 > AzureIoT”，然后选择“RemoteMonitoring”  。 它将打开一个新的 VS Code 窗口，其中包含一个项目文件夹。

   > [!NOTE]
   > 如果无意中关闭了窗格，可以重新打开它。 使用 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 打开命令面板，键入“Arduino”，然后找到并选择“Arduino:   Examples”。

## <a name="provision-required-azure-services"></a>预配所需的 Azure 服务

在解决方案窗口中，通过在提供的文本框中输入 `task cloud-provision` 通过 `Ctrl+P` (macOS: `Cmd+P`) 来运行任务。

在 VS Code 终端中，交互式命令行指导你预配所需的 Azure 服务。

![预配 Azure 资源](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>生成并上传设备代码

1. 使用 `Ctrl+P` (macOS: `Cmd + P`) 并键入 **task config-device-connection**。

2. 终端会询问是否要使用它通过 `task cloud-provision` 步骤检索的连接字符串。 还可以通过单击“新建...”输入你自己的设备连接字符串

3. 终端会提示进入配置模式。 为此，请长按按钮 A，然后按下重置按钮并松开。 屏幕将显示 DevKit ID 和“配置”。

   ![输入连接字符串](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. 在 `task config-device-connection` 完成后，单击 `F1` 来加载 VS Code 命令并选择 `Arduino: Upload`。 VS Code 将开始验证并上传 Arduino 草图。
  
   ![验证并上传 Arduino 草图](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit 将重新启动并开始运行代码。

## <a name="test-the-project"></a>测试项目

在示例应用运行时，DevKit 会通过 WiFi 将传感器数据发送到 Azure IoT 远程监视解决方案加速器。 若要查看结果，请遵循以下步骤：

1. 转到 Azure IoT 远程监视解决方案加速器，然后单击“仪表板”。 

2. 在远程监视解决方案控制台上会看到 DevKit 传感器状态。

   ![Azure IoT 远程监视解决方案加速器中的传感器数据](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>更改设备 ID

如果希望在代码中将硬编码的 **AZ3166** 更改为自定义的设备 ID，请修改[远程监视示例](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23)中显示的代码行。

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，请参阅 [IoT 开发人员工具包常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或通过以下渠道联系我们：

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>后续步骤

了解如何将 DevKit 设备连接到 Azure IoT 远程监视解决方案加速器并将传感器数据可视化以后，建议接下来学习以下教程：

* [Azure IoT 解决方案加速器概述](https://docs.microsoft.com/azure/iot-suite/)

* [将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)

* [IoT 开发人员工具包](https://microsoft.github.io/azure-iot-developer-kit/) 
