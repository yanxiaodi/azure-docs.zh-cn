---
title: 使用 IoT 中心 C SDK 针对受限制设备进行 Azure IoT 中心开发 | Microsoft Docs
description: 开发人员指南：指导如何使用 Azure IoT SDK 针对受限制设备进行开发。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.openlocfilehash: d69fe6b845d3af04e42ee91daa9359dcb9a88fc5
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880966"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>使用 Azure IoT C SDK 针对受限制设备进行开发

Azure IoT 中心 C SDK 使用 ANSI C (C99) 编写，因而非常适合用于运行各种平台，磁盘和内存占用都很小。 建议 RAM 至少为 64 KB，但具体内存占用取决于使用的协议、打开的连接数以及目标平台。
> [!NOTE]
> * Azure IoT C SDK 会定期发布资源消耗信息, 以帮助进行开发。  请访问我们的[GitHub 存储库](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)并查看最新的基准。
>

C SDK 可通过 apt-get、NuGet 和 MBED 以程序包的形式提供。 若要以受限制设备为目标，则可能需要为目标平台在本地构建 SDK。 本文档演示如何使用 [cmake](https://cmake.org/) 删除特定功能，从而缩小 C SDK 占用的空间。 此外，本文档讨论使用受限制设备的最佳编程模型。

## <a name="building-the-c-sdk-for-constrained-devices"></a>针对受限制设备构建 C SDK

针对受限制设备构建 C SDK。

### <a name="prerequisites"></a>先决条件

请按照本 [C SDK 设置指南](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)准备用于构建 C SDK 的开发环境。 在开始执行使用 cmake 进行构建的步骤前，可调用 cmake 标记来删除未使用的功能。

### <a name="remove-additional-protocol-libraries"></a>删除其他协议库

C SDK 目前支持五个协议:MQTT、MQTT over websocket、AMQPs、AMQP over WebSocket 和 HTTPS。 大多数方案需要在客户端上运行一到两个协议，因此可从 SDK 中删除未使用的协议库。 有关为方案选择适当通信协议的其他信息，请参阅[选择 IoT 中心通信协议](iot-hub-devguide-protocols.md)。 例如，MQTT 是一种轻量级协议，通常更适合用于受限制设备。

可以使用以下 cmake 命令删除 AMQP 和 HTTP 库：

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>删除 SDK 日志记录功能

C SDK 提供各种日志记录功能来帮助进行调试。 可以使用以下 cmake 命令删除生产设备的日志记录功能：

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>删除上传到 blob 的功能

可以使用 SDK 的内置功能将大文件上传到 Azure 存储。 Azure IoT 中心充当关联 Azure 存储帐户的调度程序。 可以使用此功能发送媒体文件、大型遥测批次和日志。 可以在[使用 IoT 中心上传文件](iot-hub-devguide-file-upload.md)中获取详细信息。 如果应用程序不需要此功能，则可以使用以下 cmake 命令删除此功能：

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>在 Linux 环境中运行 strip

如果二进制文件在 Linux 系统上运行，则可以在编译后利用 [strip 命令](https://en.wikipedia.org/wiki/Strip_(Unix))缩小最终应用程序的大小。

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>适用于受限制设备的编程模型

接下来，查看适用于受限制设备的编程模型。

### <a name="avoid-using-the-serializer"></a>避免使用序列化程序

C SDK 具有可选的 [C SDK 序列化程序](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)，它允许使用声明式映射表来定义方法和设备孪生属性。 序列化程序旨在简化开发，但也会增加开销，因而对于受限制设备而言不是最优选择。 在这种情况下，请考虑使用原始客户端 API，并使用轻量级分析程序（例如 [parson](https://github.com/kgabis/parson)）分析 JSON。

### <a name="use-the-lower-layer-_ll_"></a>使用较低层 (_LL_)

C SDK 支持两种编程模型。 其中一组拥有具有 _LL_ 中缀的 API，该中缀代表较低层。 这组 API 权重更轻且不会启动工作线程，这意味着用户必须手动控制调度。 例如，对于设备客户端，可在此[头文件](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h)中找到 _LL_ API。 

另一组没有 _LL_ 索引的 API 称为便捷层，工作线程会在其中自动启动。 例如，设备客户端的便捷层 API 可以在此 [IoT 设备客户端头文件](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h)中找到。 对于每个额外的线程会占用大量系统资源的受限制设备，请考虑使用 _LL_ API。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure IoT C SDK 体系结构，请参阅：
-   [Azure IoT C SDK 源代码](https://github.com/Azure/azure-iot-sdk-c/)
-   [适用于 C 语言的 Azure IoT 设备 SDK 简介](iot-hub-device-sdk-c-intro.md)
