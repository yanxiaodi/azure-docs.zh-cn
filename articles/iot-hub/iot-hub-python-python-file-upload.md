---
title: 使用 Python 将设备上的文件上传到 Azure IoT 中心 | Microsoft Docs
description: 如何使用适用于 Python 的 Azure IoT 设备 SDK 将文件上的设备上传到云端。 上传的文件存储在 Azure 存储 Blob 容器中。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 6dfbcc7a3e76842546326742d801c913451855f3
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001127"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>使用 IoT 中心将文件从设备上传到云 (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本文演示如何使用 [IoT 中心的文件上传功能](iot-hub-devguide-file-upload.md)将文件上传到 [Azure Blob 存储](../storage/index.yml)。 本教程介绍如何：

* 安全地提供存储容器用于文件上传。

* 使用 Python 客户端通过 IoT 中心上传文件。

[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-python.md)快速入门演示了 IoT 中心基本的设备到云的消息传送功能。 但是，在某些情况下，无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 需要从设备上传文件时，仍可以使用 IoT 中心的安全性和可靠性。

> [!NOTE]
> IoT 中心 Python SDK 目前仅支持上传基于字符的文件，如 .txt 文件。

在本教程最后，会运行下述 Python 控制台应用：

* FileUpload.py，该应用使用 Python 设备 SDK 将文件上传到存储中。

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> 本指南使用已弃用的 V1 Python SDK，因为尚未在新的 V2 SDK 中实现文件上传功能。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>从设备应用上传文件

本部分中操作将会创建可将文件上传到 IoT 中心的设备应用。

1. 在命令提示符处，运行以下命令来安装 **azure-iothub-device-client** 包：

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. 使用文本编辑器创建将上传到 blob 存储的测试文件。

    > [!NOTE]
    > IoT 中心 Python SDK 目前仅支持上传基于字符的文件，如 .txt 文件。

3. 使用文本编辑器，在工作文件夹中创建一个 FileUpload.py 文件。

4. 在 FileUpload.py 文件的开头添加以下 `import` 语句和变量。 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. 在文件中，将 `[Device Connection String]` 替换为 IoT 中心设备的连接字符串。 将 `[Full path to file]` 替换为创建的测试文件的路径，或是设备上要上传的任何文件的路径。 将 `[File name for storage]` 替换为要在文件上传到 blob 存储之后向它提供的名称。 

6. 针对 upload_blob 函数创建一个回调：

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. 添加以下代码用于连接客户端和上传文件。 此外还包含 `main` 例程：

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. 保存并关闭 UploadFile.py 文件。

## <a name="run-the-application"></a>运行应用程序

现即可运行应用程序。

1. 在工作文件夹的命令提示符处，运行以下命令：

    ```cmd/sh
    python FileUpload.py
    ```

2. 以下屏幕截图显示来自 FileUpload 应用的输出：

    ![simulated-device 应用的输出](./media/iot-hub-python-python-file-upload/1.png)

3. 可以使用门户查看所配置的存储容器中上传的文件：

    ![上传的文件](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 IoT 中心的文件上传功能来简化从设备进行的文件上传。 可以使用以下文章继续探索 IoT 中心功能和方案：

* [以编程方式创建 IoT 中心](iot-hub-rm-template-powershell.md)

* [C SDK 简介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)
