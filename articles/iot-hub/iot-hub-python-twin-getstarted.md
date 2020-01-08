---
title: Azure IoT 中心设备孪生入门 (Python) | Microsoft Docs
description: 如何使用 Azure IoT 中心设备孪生添加标记，并使用 IoT 中心查询。 使用用于 Python 的 Azure IoT SDK 来实现一个模拟设备应用，并实现可添加标记和运行 IoT 中心查询的一个服务应用。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: 565330528638bb6c8e0458a9761e2cf9fa4e3d2a
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001486"
---
# <a name="get-started-with-device-twins-python"></a>设备孪生入门 (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教程结束时，会获得两个 Python 控制台应用：

* **AddTagsAndQuery.py**，这是一个 Python 后端应用，用于添加标记并查询设备孪生。

* **ReportConnectivity.py**，这是一个 .Python 应用，用于模拟使用早先创建的设备标识连接到 IoT 中心的设备，并报告其连接状况。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>创建服务应用

在本部分中，将创建一个 Python 控制台应用，用于将位置元数据添加到与 **{DEVICE ID}** 关联的设备克隆中。 然后，该应用将选择位于 Redmond 的设备来查询存储在 IoT 中心的设备孪生，然后查询报告移动电话网络连接的设备孪生。

1. 在工作目录中，打开命令提示符，并安装**用于 Python 的 Azure IoT 中心服务 SDK**。

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > 适用于 azure iothub 的 pip 包目前仅适用于 Windows 操作系统。 对于 Linux/Mac 操作系统，请参阅[准备适用于 Python 的开发环境](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)一文中特定于 Linux 和 Mac 操作系统的部分。
   >

2. 使用文本编辑器，新建一个 **AddTagsAndQuery.py** 文件。

3. 添加以下代码，从服务 SDK 导入所需模块：

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. 添加以下代码。 将`[IoTHub Connection String]`替换为在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 iot 中心连接字符串。 将`[Device Id]`替换为在[IoT 中心注册新设备](#register-a-new-device-in-the-iot-hub)中注册的设备 ID。
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. 将以下代码添加到 **AddTagsAndQuery.py** 文件：

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    **Registry** 对象公开从该服务与设备孪生交互所需的所有方法。 此代码将首先初始化 **Registry** 对象，然后更新 **deviceId** 的设备孪生，最后运行两个查询。 第一个查询仅选择位于 **Redmond43** 工厂的设备的设备孪生，第二个查询将查询细化为仅选择还要通过移动电话网络连接的设备。

6. 在 **AddTagsAndQuery.py** 的末尾添加以下代码来实现 **iothub_service_sample_run** 函数：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. 使用以下方法运行应用程序：

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    在查询位于 **Redmond43** 的所有设备的查询结果中，应该会看到一个设备，而在将结果限制为使用蜂窝网络的设备的查询结果中没有任何设备。

    ![第一个显示 Redmond 中所有设备的查询](./media/iot-hub-python-twin-getstarted/service-1.png)

在下一部分中，创建的设备应用将报告连接信息，并更改上一部分中查询的结果。

## <a name="create-the-device-app"></a>创建设备应用

在本部分中，将创建一个 Python 控制台应用，用于连接到你的中心作为 **{设备 ID}** ，然后更新其设备克隆的报告属性，以包含使用蜂窝网络连接的信息。

1. 在工作目录中的命令提示符下，安装**适用于 Python 的 Azure IoT 中心设备 SDK**：

    ```cmd/sh
    pip install azure-iot-device
    ```

2. 使用文本编辑器，新建一个 **ReportConnectivity.py** 文件。

3. 添加以下代码，从设备 SDK 导入所需模块：

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. 添加以下代码。 将占位符值替换为在[IoT 中心注册新设备](#register-a-new-device-in-the-iot-hub)中复制的设备连接字符串。 `[IoTHub Device Connection String]`

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. 将以下代码添加到 **ReportConnectivity.py** 文件以实现设备孪生功能：

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```

    **Client** 对象公开从该设备与设备孪生交互所需的所有方法。 上面的代码首先初始化 **Client** 对象，然后检索你的设备的设备孪生，并使用连接信息更新其报告属性。

6. 在 **ReportConnectivity.py** 的末尾添加以下代码来实现 **iothub_client_sample_run** 函数：

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. 运行设备应用：

    ```cmd/sh
    python ReportConnectivity.py
    ```

    应当会看到关于设备孪生已更新的确认。

    ![更新孪生](./media/iot-hub-python-twin-getstarted/device-1.png)

8. 既然设备报告其连接的信息，该信息应显示在两个查询中。 回过头来再次运行查询：

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    这一次，两个查询结果中应当都会显示你的 **{Device ID}** 。

    ![第二个查询](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，并在 IoT 中心的标识注册表中创建了设备标识。 已从后端应用以标记形式添加了设备元数据，并编写了模拟的设备应用，用于报告设备孪生中的设备连接信息。 你还学习了如何使用注册表查询此信息。

使用下列资源了解如何执行以下操作：

* 通过 [IoT 中心入门](quickstart-send-telemetry-python.md)教程学习如何从设备发送遥测数据。

* 使用[所需属性配置设备](tutorial-device-twins.md)教程，使用设备克隆的所需属性配置设备。

* 通过[使用直接方法](quickstart-control-device-python.md)教程学习如何以交互方式控制设备（例如从用户控制的应用打开风扇）。
