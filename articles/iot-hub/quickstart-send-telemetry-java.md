---
title: 快速入门：使用 Java 将遥测数据发送到 Azure IoT 中心
description: 本快速入门将运行两个示例 Java 应用程序，从而向 IoT 中心发送模拟遥测数据，并读取 IoT 中心的遥测数据，在云中进行处理。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: a97081101df5199d3201a6ec47df4c2ac2747416
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309140"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>快速入门：将遥测数据发送到 Azure IoT 中心并使用 Java 应用程序读取它

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

本快速入门显示了如何将遥测数据发送到 Azure IoT 中心并使用 Java 应用程序读取它。 IoT 中心是一项 Azure 服务，用于将大量遥测数据从 IoT 设备引入云中进行存储或处理。 本快速入门会将模拟设备应用程序的遥测数据通过 IoT 中心发送到后端应用程序进行处理。

本快速入门使用两个预先编写的 Java 应用程序，一个用于发送遥测数据，一个用于读取中心的遥测数据。 运行这两个应用程序前，请先创建 IoT 中心并在中心注册设备。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本快速入门中运行的两个示例应用程序是使用 Java 编写的。 开发计算机上需要安装 Java SE 8。

可以从 [Java 对 Azure 和 Azure Stack 的长期支持](https://docs.microsoft.com/en-us/java/azure/jdk/?view=azure-java-stable)下载适用于多个平台的 Java SE 开发工具包 8。 请确保在“长期支持”  下选择“Java 8”  以获取 JDK 8 的下载。

可以使用以下命令验证开发计算机上 Java 的当前版本：

```cmd/sh
java -version
```

若要生成示例，需安装 Maven 3。 可从 [Apache Maven](https://maven.apache.org/download.cgi) 为多个平台下载 Maven。

可以使用以下命令验证开发计算机上 Maven 的当前版本：

```cmd/sh
mvn --version
```

运行以下命令将用于 Azure CLI 的 Microsoft Azure IoT 扩展添加到 Cloud Shell 实例。 IOT 扩展会将 IoT 中心、IoT Edge 和 IoT 设备预配服务 (DPS) 特定的命令添加到 Azure CLI。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

从 https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip 下载示例 Java 项目并提取 ZIP 存档。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，将使用 Azure Cloud Shell 来注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以创建设备标识。

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyJavaDevice**：所注册的设备的名称。 请按显示的方法使用 MyJavaDevice  。 如果为设备选择其他名称，则需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyJavaDevice
    ```

2. 在 Azure Cloud Shell 中运行以下命令，以获取刚注册设备的设备连接字符串：**YourIoTHubName  :将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyJavaDevice --output table
    ```

    记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

3. 还需要使用来自 IoT 中心的与事件中心兼容的终结点、与事件中心兼容的路径和服务主密钥，确保后端应用程序能连接到 IoT 中心并检索消息    。 以下命令可检索 IoT 中心的这些值：

     **YourIoTHubName：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name YourIoTHubName

    az iot hub show --query properties.eventHubEndpoints.events.path --name YourIoTHubName

    az iot hub policy show --name service --query primaryKey --hub-name YourIoTHubName
    ```

    记下这三个值，稍后会在快速入门中用到这些值。

## <a name="send-simulated-telemetry"></a>发送模拟遥测数据

模拟设备应用程序会连接到 IoT 中心上特定于设备的终结点，并发送模拟的温度和湿度遥测数据。

1. 在本地终端窗口中，导航到示例 Java 项目的根文件夹。 然后导航到 **iot-hub\Quickstarts\simulated-device** 文件夹。

2. 在所选文本编辑器中打开 src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java 文件  。

    将 `connString` 变量的值替换为之前记下的设备连接字符串。 然后将更改保存到 SimulatedDevice.java 文件  。

3. 在本地终端窗口中，运行以下命令以安装所需的库，并生成模拟设备应用程序：

    ```cmd/sh
    mvn clean package
    ```

4. 在本地终端窗口中，运行以下命令，以便运行模拟设备应用程序：

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    以下屏幕截图显示了模拟设备应用程序将遥测数据发送到 IoT 中心后的输出：

    ![运行模拟设备](media/quickstart-send-telemetry-java/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>从中心读取遥测数据

后端应用程序会连接到 IoT 中心上的服务端“事件”终结点  。 应用程序会接收模拟设备发送的设备到云的消息。 IoT 中心后端应用程序通常在云中运行，接收和处理设备到云的消息。

1. 在另一个本地终端窗口中，导航到示例 Java 项目的根文件夹。 然后导航到 iot-hub\Quickstarts\read-d2c-messages 文件夹  。

2. 在所选文本编辑器中打开 src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java 文件  。 更新以下变量并保存对文件所做的更改。

    | 变量 | 值 |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | 将变量的值替换为之前记下的与事件中心兼容的终结点。 |
    | `eventHubsCompatiblePath`     | 将变量的值替换为之前记下的与事件中心兼容的路径。 |
    | `iotHubSasKey`                | 将变量的值替换为之前记下的服务主密钥。 |

3. 在本地终端窗口中运行以下命令，以安装所需的库并生成后端应用程序：

    ```cmd/sh
    mvn clean package
    ```

4. 在本地终端窗口中，运行以下命令，以便运行终端应用程序：

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    以下屏幕截图显示了后端应用程序接收模拟设备发送到 IoT 中心的遥测数据后的输出：

    ![运行后端应用程序](media/quickstart-send-telemetry-java/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门设置了 IoT 中心、注册了设备、使用 Java 应用程序发送了模拟遥测数据到中心，并使用简单的后端应用程序读取中心的遥测数据。

若要了解如何从后端应用程序控制模拟设备，请继续阅读下一快速入门教程。

> [!div class="nextstepaction"]
> [快速入门：控制连接到 IoT 中心的设备](quickstart-control-device-java.md)
