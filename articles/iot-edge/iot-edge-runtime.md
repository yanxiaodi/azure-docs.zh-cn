---
title: 了解运行时如何管理设备 - Azure IoT Edge |Microsoft Docs
description: 了解 Azure IoT Edge 运行时如何管理设备上的模块、安全性、通信和报告
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 677ff7ffab22eebdace67151d703ba83c2146602
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998607"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>了解 Azure IoT Edge 运行时及其体系结构

IoT Edge 运行时是将某个设备转换为 IoT Edge 设备的程序集合。 在 IoT Edge 运行时组件的共同作用下，IoT Edge 设备可以接收要在边缘上运行的代码并传递结果。 

IoT Edge 运行时在 IoT Edge 设备上执行以下功能：

* 在设备上安装和更新工作负荷。
* 维护设备上的 Azure IoT Edge 安全标准。
* 确保 [IoT Edge 模块](iot-edge-modules.md)始终处于运行状态。
* 将模块运行状况报告给云以进行远程监视。
* 促进下游叶设备与 IoT Edge 设备之间的通信。
* 促进 IoT Edge 设备上的模块间的通信。
* 促进 IoT Edge 设备和云之间的通信。

![运行时向 IoT 中心传达见解和模块运行状况](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 运行时的职责分为两类：通信和模块管理。 这两个角色由作为 IoT Edge 运行时一部分的两个组件执行。 IoT Edge 中心负责通信，而 IoT Edge 代理则负责部署和监视模块。 

IoT Edge 中心和 IoT Edge 代理都是模块，就像 IoT Edge 设备上运行的其他任何模块一样。 

## <a name="iot-edge-hub"></a>IoT Edge 中心

IoT Edge 中心是组成 Azure IoT Edge 运行时的两个模块之一。 它通过公开与 IoT 中心相同的协议终结点，充当 IoT 中心的本地代理。 这种一致性意味着客户端（无论是设备还是模块）可以连接到 IoT Edge 运行时，就像连接到 IoT 中心一样。 

>[!NOTE]
> IoT Edge 中心支持使用 MQTT 或 AMQP 进行连接的客户端。 它不支持使用 HTTP 的客户端。 

IoT Edge 中心不是在本地运行的完整版本的 IoT 中心。 有一些功能是 IoT Edge 中心以静默方式委托给 IoT 中心的。 例如，设备首次尝试连接时，IoT Edge 中心会将身份验证请求转发给 IoT 中心。 建立第一个连接之后，IoT Edge 中心会在本地缓存安全信息。 无需在云中进行身份验证即可允许该设备的后续连接。 

为减少 IoT Edge 解决方案使用的带宽，IoT Edge 中心优化了对云的实际连接数量。 IoT Edge 中心采用来自客户端（如模块或叶设备）的逻辑连接，并将它们组合为连接到云的单个物理连接。 此过程的详细信息对解决方案的其他部分透明。 即使客户端都通过相同连接进行发送，它们也会认为具有自己的云连接。 

![IoT Edge 中心是物理设备和 IoT 中心之间的网关](./media/iot-edge-runtime/Gateway.png)

IoT Edge 中心可以确定其是否连接到了 IoT 中心。 如果连接丢失，IoT Edge 中心将在本地保存消息或克隆更新。 一旦重新建立连接，将同步所有数据。 此临时缓存的位置由 IoT Edge 中心的模块孪生的属性决定。 只要设备具有存储容量，缓存的大小就没有限制并且会增加。 有关详细信息，请参阅[脱机功能](offline-capabilities.md)。

### <a name="module-communication"></a>模块通信

IoT Edge 中心促进模块间通信。 使用 IoT Edge 中心作为消息中转站可以保持模块之间相互独立。 模块只需指定它们接受消息的输入和写入消息的输出。 解决方案开发人员可以将这些输入和输出拼接在一起，以便于模块按特定于该解决方案的顺序处理数据。 

![IoT Edge 中心促进模块间通信](./media/iot-edge-runtime/module-endpoints.png)

为了将数据发送到 IoT Edge 中心，模块会调用 SendEventAsync 方法。 第一个参数指定要发送消息的输出。 下面的伪代码在 **output1** 上发送消息：

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

若要接收消息，请注册一个回叫，用于处理在特定输入上传入的消息。 下面的伪代码注册要用于处理在 **input1** 上接收到的所有消息的函数 messageProcessor：

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

有关 ModuleClient 类及其通信方法的更多信息，请参阅首选 SDK 语言的 API 参考：[C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)、 [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、 [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python)、 [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)[或 node.js。](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

解决方案开发者负责指定用于确定 IoT Edge 中心如何在模块间传递消息的规则。 传递规则在云中定义，并向下推送到其设备孪生中的 IoT Edge 中心。 使用 IoT 中心路由的同一语法定义在 Azure IoT Edge 中的模块之间的路由。 有关详细信息，请参阅[了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)。   

![模块之间的路由通过 IoT Edge 中心](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge 代理

IoT Edge 代理是构成 Azure IoT Edge 运行时的其他模块。 它负责实例化模块、确保它们继续运行以及报告返回到 IoT 中心的模块的状态。 就和其他任何模块一样，IoT Edge 代理使用其模块孪生来存储此配置数据。 

[IoT Edge 安全守护程序](iot-edge-security-manager.md)在设备启动时启动 IoT Edge 代理。 该代理从 IoT 中心检索其模块孪生并检查部署清单。 部署清单是一个 JSON 文件，它声明了需要启动的模块。 

部署清单中的每个项都包含有关模块的特定信息，并由 IoT Edge 代理用于控制模块的生命周期。 下面是一些更有趣的属性： 

* settings.image - IoT Edge 代理用来启动模块的容器映像。 如果该映像受密码保护，则必须为 IoT Edge 代理配置容器注册表的凭据。 可以使用部署清单远程配置容器注册表的凭据，也可以在 IoT Edge 设备本身上通过更新 IoT Edge 程序文件夹中的 `config.yaml` 文件进行配置。
* **settings.createOptions** - 在启动模块的容器时直接传递到 Moby 容器守护程序的一个字符串。 在此属性中添加选项可实现高级配置，如端口转发或将卷装载到模块的容器中。  
* 状态 - IoT Edge 代理放置模块的状态。 通常，此值设置为“正在运行”，因为大多数人都希望 IoT Edge 代理立即启动设备上的所有模块。 但是，可以将模块的初始状态指定为“已停止”，等待一定时间后告知 IoT Edge 代理启动模块。 IoT Edge 代理会向报告属性中的云报告每个模块的状态。 所需属性和报告的属性之间存在差异指示了设备运行状况不正常。 支持的状态为：
   * 正在下载
   * 正在运行
   * 不正常
   * 已失败
   * 已停止
* restartPolicy - IoT Edge 代理如何重启模块。 可能的值包括：
   * `never` – IoT Edge 代理永远不会重启模块。
   * `on-failure` - 如果模块崩溃，IoT Edge 代理会重启它。 如果该模块完全关闭，IoT Edge 代理不会重启它。
   * `on-unhealthy` - 如果模块崩溃或被视为不正常，IoT Edge 代理会重启它。
   * `always` - 如果模块崩溃、被视为不正常或者以任何方式关闭，IoT Edge 代理会重启它。 

IoT Edge 代理会将运行时响应发送到 IoT 中心。 下面是可能的响应的列表：
  * 200 - 正常
  * 400 - 部署配置格式不正确或无效。
  * 417 - 没有为设备设置部署配置。
  * 412 - 部署配置中的架构版本无效。
  * 406 - IoT Edge 设备脱机或不发送状态报告。
  * 500 - IoT Edge 运行时中出现了一个错误。

有关详细信息，请参阅[了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)。   

### <a name="security"></a>安全性

IoT Edge 代理在 IoT Edge 设备的安全性中起着关键作用。 例如，它会执行某些操作，如在启动之前验证模块的映像。 

有关 Azure IoT Edge 安全框架的详细信息，请阅读有关 [IoT Edge 安全管理器](iot-edge-security-manager.md)的内容。

## <a name="next-steps"></a>后续步骤

[了解 Azure IoT Edge 模块](iot-edge-modules.md)
