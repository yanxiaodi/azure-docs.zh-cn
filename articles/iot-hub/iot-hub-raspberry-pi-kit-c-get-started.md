---
title: 使用 C 将 Raspberry Pi 连接到 Azure IoT 中心 | Microsoft Docs
description: 了解如何设置 Raspberry Pi 并将其连接到 Azure IoT 中心，使其能够将数据发送到 Azure 云平台
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 94ac75c4165b11e343ce5c31480a511ebf978a36
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67838781"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>将 Raspberry Pi 连接到 Azure IoT 中心 (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教程中，首先学习有关使用运行 Raspbian 的 Raspberry Pi 的基础知识。 然后将学习如何使用 [Azure IoT 中心](about-iot-hub.md)将设备无缝连接到云。 有关 Windows 10 IoT Core 的示例，请访问 [Windows 开发人员中心](https://www.windowsondevices.com/)。

还没有工具包？ 请尝试 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)。 或在[此处](https://azure.microsoft.com/develop/iot/starter-kits)购买新工具包。

## <a name="what-you-do"></a>准备工作

* 创建 IoT 中心。

* 在 IoT 中心内为 Pi 注册设备。

* 设置 Raspberry Pi。

* 在 Pi 上运行示例应用程序，以将传感器数据发送到 IoT 中心。

将 Raspberry Pi 连接到所创建的 IoT 中心。 然后，在 Pi 上运行示例应用程序，以从 BME280 传感器收集温度和湿度数据。 最后，将传感器数据发送到 IoT 中心。

## <a name="what-you-learn"></a>学习内容

* 如何创建 Azure IoT 中心以及如何获取新的设备连接字符串。

* 如何通过 BME280 传感器连接 Pi。

* 如何通过在 Pi 上运行示例应用程序来收集传感器数据。

* 如何将传感器数据发送到 IoT 中心。

## <a name="what-you-need"></a>所需条件

![所需条件](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Raspberry Pi 2 或 Raspberry Pi 3 电路板。

* 一个有效的 Azure 订阅。 如果没有 Azure 帐户，只需几分钟时间就能[创建一个免费的 Azure 试用帐户](https://azure.microsoft.com/free/)。

* 监视器、USB 键盘和连接到 Pi 的鼠标。

* 运行 Windows 或 Linux 的 Mac 或 PC。

* Internet 连接。

* 16 GB 或更大内存的 microSD 卡。

* USB-SD 适配器或 microSD 卡，用于将操作系统映像刻录到 microSD 卡中。

* 带有 6 英尺微型 USB 电缆的 5 伏 2 安电源。

以下项是可选项：

* 已装配的 Adafruit BME280 温度、压力和湿度传感器。

* 试验板。

* 6 根 F/M 跳线。

* 散射的 10 毫米 LED 灯。

> [!NOTE]
> 上述项为可选项，因为代码示例支持模拟的传感器数据。
>

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>设置 Raspberry Pi

现在设置 Raspberry Pi。

### <a name="install-the-raspbian-operating-system-for-pi"></a>为 Pi 安装 Raspbian 操作系统

准备用于安装 Raspbian 映像的 microSD 卡。

1. 下载 Raspbian。

   1. [下载 Raspbian Stretch with Desktop](https://www.raspberrypi.org/downloads/raspbian/)（.zip 文件）。

   2. 将 Raspbian 映像提取到计算机上的一个文件夹中。

2. 将 Raspbian 安装到 microSD 卡。

   1. [下载并安装 Etcher SD 卡刻录机实用工具](https://etcher.io/)。

   2. 运行 Etcher 并选择你在步骤 1 中提取的 Raspbian 映像。

   3. 选择 microSD 卡驱动器。 注意，Etcher 可能已选择了正确的驱动器。

   4. 单击“刷机”，将 Raspbian 安装到 microSD 卡。

   5. 在安装完成后，从计算机中移除 microSD 卡。 可以安全地直接移除 microSD 卡，因为在完成时 Etcher 会自动弹出或卸载 microSD 卡。

   6. 将 microSD 卡插入到 Pi 中。

### <a name="enable-ssh-and-spi"></a>启用 SSH 和 SPI

1. 将 Pi 连接到监视器、键盘和鼠标，启动 Pi，然后通过将 `pi` 用作用户名并将 `raspberry` 用作密码来登录 Raspbian。
 
2. 依次单击 Raspberry 图标 >“首选项”   > “Raspberry Pi 配置”  。

   ![Raspbian 首选项菜单](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. 在“接口”  选项卡上，将“SPI”  和“SSH”  设置为“启用”  ，并单击“确定”  。 如果没有物理传感器，但希望使用模拟传感器数据，可选择执行此步骤。

   ![在 Raspberry Pi 上启用 SPI 和 SSH](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> 若要启用 SSH 和 SPI，可在 [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) 和 [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md) 中找到更多参考文档。
>

### <a name="connect-the-sensor-to-pi"></a>将传感器连接到 Pi

使用试验板和跳线，将 LED 灯和 BME280 连接到 Pi，如下所示。 如果没有该传感器，请[跳过此部分](#connect-pi-to-the-network)。

![Raspberry Pi 和传感器连接](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

BME280 传感器可收集温度和湿度数据。 如果设备和云之间有通信，LED 将闪烁。

对于传感器引脚，请使用以下接线：

| 启动（传感器和 LED 灯）     | 结束（开发板）            | 线缆颜色   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD（引脚 5G）         | GPIO 4（引脚 7）         | 白线   |
| LED GND（引脚 6G）         | GND（引脚 6）            | 黑线   |
| VDD（引脚 18F）            | 3.3 伏 PWR（引脚 17）      | 白线   |
| GND（引脚 20F）            | GND（引脚 20）           | 黑线   |
| SCK（引脚 21F）            | SPI0 SCLK（引脚 23）     | 橙色电缆  |
| SDO（引脚 22F）            | SPI0 MISO（引脚 21）     | 黄色电缆  |
| SDI（引脚 23F）            | SPI0 MOSI（引脚 19）     | 绿色电缆   |
| CS（引脚 24F）             | SPI0 CS（引脚 24）       | 蓝线    |

单击查看 [Raspberry Pi 2 和 3 引脚映射](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi)以供参考。

成功将 BME280 连接到 Raspberry Pi 后，它应如下图所示。

![连接在一起的 Pi 和 BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>将 Pi 连接到网络

使用 USB 微电缆和电源开启 Pi。 使用以太网电缆将 Pi 连接到有线网络，或者按照 [Raspberry Pi Foundation 中的说明](https://www.raspberrypi.org/learning/software-guide/wifi/)将 Pi 连接到无线网络。 将 Pi 成功连接到网络后，需要记下 [Pi 的 IP 地址](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)。

![已连接到有线网络](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>在 Pi 上运行示例应用程序

### <a name="sign-into-your-raspberry-pi"></a>登录到 Raspberry Pi

1. 使用主计算机的以下任一 SSH 客户端连接到 Raspberry Pi。
   
   **Windows 用户**
   1. 下载并安装 [PuTTY](https://www.putty.org/) for Windows。 
   1. 将 Pi 的 IP 地址复制到主机名（或 IP 地址）部分，并选择 SSH 作为连接类型。
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac 和 Ubuntu 用户**

   使用 Ubuntu 或 macOS 上的内置 SSH 客户端。 可能需要运行 `ssh pi@<ip address of pi>`，以通过 SSH 连接 Pi。
   > [!NOTE]
   > 默认用户名是 `pi`，密码是 `raspberry`。


### <a name="configure-the-sample-application"></a>配置示例应用程序

1. 通过运行以下命令，克隆示例应用程序：

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. 运行安装脚本：

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > 如果没有物理 BME280，可使用“--simulated-data”作为命令行参数来模拟温度和湿度数据  。 `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>生成并运行示例应用程序

1. 通过运行以下命令，生成示例应用程序：

   ```bash
   cmake . && make
   ```
   
   ![生成输出](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. 通过运行以下命令，生成示例应用程序：

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > 确保将设备连接字符串复制并粘贴到单引号中。
   >

应看到以下输出，该输出显示传感器数据和发送到 IoT 中心的消息。

![输出 - 从 Raspberry Pi 发送到 IoT 中心的传感器数据](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>读取 IoT 中心收到的消息

若要监视 IoT 中心从设备收到的消息，一种方法是使用适用于 Visual Studio Code 的 Azure IoT Tools。 若要了解详细信息，请参阅[使用适用于 Visual Studio Code 的 Azure IoT Tools 在设备和 IoT 中心之间发送和接收消息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。

若要了解如何通过更多方式来处理设备发送的数据，请转到下一部分。

## <a name="next-steps"></a>后续步骤

此时已运行示例应用程序，收集传感器数据并将其发送到 IoT 中心。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
