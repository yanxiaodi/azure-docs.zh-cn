---
title: 使用 SendGrid 服务和 Azure Functions 在门打开时发送电子邮件 | Microsoft Docs
description: 监视磁传感器，检测门何时打开，并使用 Azure Functions 发送电子邮件通知。
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: a620b592a33f9de11de53d623d257f203da2157b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61370220"
---
# <a name="door-monitor"></a>门监视器          

MXChip IoT DevKit 包含内置的磁传感器。 在此项目中，请检测附近是否存在强磁场 - 在此示例中，该磁场来自一块小的、永久性磁铁。

## <a name="what-you-learn"></a>学习内容

此项目介绍：
- 如何使用 MXChip IoT DevKit 的磁传感器检测附近磁铁的移动。
- 如何使用 SendGrid 服务向电子邮件地址发送通知。

> [!NOTE]
> 要具体应用此项目，请执行以下任务：
> - 将一块磁铁装载到门的边缘。
> - 将 DevKit 装载到靠近磁铁的门框上。 打开或关闭此门会触发传感器，然后你就会收到有关此事件的电子邮件通知。

## <a name="what-you-need"></a>所需条件

完成[入门指南](iot-hub-arduino-iot-devkit-az3166-get-started.md)来实现以下目的：

* 将 DevKit 连接到 Wi-Fi
* 准备开发环境

一个有效的 Azure 订阅。 如果没有订阅，可以通过以下方法之一进行注册：

* 激活 [30 天免费试用版 Microsoft Azure 帐户](https://azure.microsoft.com/free/)。
* 声明你的 [Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)（如果你是 MSDN 或 Visual Studio 订阅者）。

## <a name="deploy-the-sendgrid-service-in-azure"></a>在 Azure 中部署 SendGrid 服务

[SendGrid](https://sendgrid.com/) 是基于云的电子邮件传递平台。 此服务将用于发送电子邮件通知。

> [!NOTE]
> 如果已部署 SendGrid 服务，可以直接转到[在 Azure 中部署 IoT 中心](#deploy-iot-hub-in-azure)。

### <a name="sendgrid-deployment"></a>SendGrid 部署

若要预配 Azure 服务，请使用“部署到 Azure”按钮。  可以通过此按钮将开源项目轻松快捷地部署到 Microsoft Azure。

单击下面的“部署到 Azure”按钮  。 

[![部署到 Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

如果尚未登录到 Azure 帐户，请立即登录。 

现在，你将看到 SendGrid 注册表单。

![SendGrid 部署](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

完成注册表单：

   * **资源组**：创建用于托管 SendGrid 服务的资源组，或使用现有资源组。 请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/manage-resource-groups-portal.md)。

   * **名称**：SendGrid 服务的名称。 选择一个不同于你的其他服务的唯一名称。

   * **密码**：此服务需要一个密码，该密码将不用于此项目中的任何项。

   * **电子邮件**：SendGrid 服务将向此电子邮件地址发送验证。

选中“固定到仪表板”选项，以便以后能够轻松查找此应用程序，然后单击“购买”以提交注册表单   。
 
### <a name="sendgrid-api-key-creation"></a>创建 SendGrid API 密钥

完成部署后，请单击此部署，然后单击“管理”按钮  。 此时将显示 SendGrid 帐户页，需要在其中验证电子邮件地址。

![SendGrid 的“管理”按钮](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

在 SendGrid 页上，单击“设置”   >   “API 密钥” >   “创建 API 密钥”。

![SendGrid 的第一个“创建 API”](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

在“创建 API 密钥”页上，输入 API 密钥名称，然后单击“创建和查看”    。

![SendGrid 的第二个“创建 API”](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

API 密钥仅显示一次。 请确保将其安全地复制和存储，因为下一步要用到它。

## <a name="deploy-iot-hub-in-azure"></a>在 Azure 中部署 IoT 中心

以下步骤将预配其他 Azure IoT 相关服务并为此项目部署 Azure Functions。

单击下面的“部署到 Azure”按钮  。 

[![部署到 Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

此时会显示注册表单。

![IoTHub 部署](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

填充注册表单上的字段。

   * **资源组**：创建用于托管 SendGrid 服务的资源组，或使用现有资源组。 请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/manage-resource-groups-portal.md)。

   * **IoT 中心名称**：IoT 中心的名称。 选择一个不同于你的其他服务的唯一名称。

   * **IoT 中心 SKU**：F1（一个订阅仅限一个）是免费的。 可在[定价页](https://azure.microsoft.com/pricing/details/iot-hub/)上查看定价详细信息。

   * **发件人电子邮件**：此字段的值应与设置 SendGrid 服务时使用的电子邮件地址相同。

选中“固定到仪表板”选项，以便以后能够轻松查找此应用程序，然后准备好继续下一步时单击“购买”   。
 
## <a name="build-and-upload-the-code"></a>生成并上传代码

接下来，加载 VS Code 中的示例代码并预配必要的 Azure 服务。

### <a name="start-vs-code"></a>启动 VS Code

- 确保 DevKit **未**连接到计算机。
- 启动 VS Code。
- 将 DevKit 连接到计算机。

> [!NOTE]
> 启动 VS Code 时，可能会收到一条错误消息，指出找不到 Arduino IDE 或相关板包。 如果收到此错误，请关闭 VS Code，再次启动 Arduino IDE，然后 VS Code 就会正确地找到 Arduino IDE 路径。

### <a name="open-arduino-examples-folder"></a>打开 Arduino 示例文件夹

展开左侧的“ARDUINO 示例”  部分，浏览到  “MXCHIP AZ3166 的示例 > AzureIoT”，然后选择“DoorMonitor”  。 此操作会打开一个新的 VS Code 窗口，其中包含一个项目文件夹。

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

还可以从命令面板打开示例应用。 使用 `Ctrl+Shift+P`（macOS: `Cmd+Shift+P`）打开命令面板，键入“Arduino”，然后找到并选择“Arduino:   Examples”。

### <a name="provision-azure-services"></a>预配 Azure 服务

在解决方案窗口中，运行云预配任务：
- 键入 `Ctrl+P`（macOS：`Cmd+P`）。
- 在提供的文本框中输入 `task cloud-provision`。

在 VS Code 终端中，交互式命令行指导你预配所需的 Azure 服务。 从提示的列表中选择此前在[在 Azure 中部署 IoT 中心](#deploy-iot-hub-in-azure)中预配的所有项目。

![云预配](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> 如果在尝试登录 Azure 时，页面停滞在“正在加载”状态，请参阅 [IoT DevKit 常见问题解答的“登录时页面停滞”部分](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure)来解决此问题。 

### <a name="build-and-upload-the-device-code"></a>生成并上传设备代码

接下来，上传设备代码。

#### <a name="windows"></a>Windows

1. 使用 `Ctrl+P` 运行 `task device-upload`。

2. 终端会提示进入配置模式。 为此，请长按按钮 A，然后按下重置按钮并松开。 屏幕会显示 DevKit 标识号和 *Configuration*（配置）一词。

#### <a name="macos"></a>macOS

1. 将 DevKit 置于配置模式：按住按钮 A，然后按下重置按钮并松开。 屏幕将显示“配置”。

2. 单击 `Cmd+P` 以运行 `task device-upload`。

#### <a name="verify-upload-and-run-the-sample-app"></a>验证、上传并运行示例应用

现已设置从[预配 Azure 服务](#provision-azure-services)步骤检索的连接字符串。 

然后，VS Code 开始验证 Arduino 草图并将其上传到 DevKit。

![设备上传](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit 将重新启动并开始运行代码。

> [!NOTE]
> 偶尔可能会收到“错误:AZ3166:未知程序包”错误消息。 如果未正确刷新板包索引，则会出现此错误。 若要解决此错误，请参阅 [IoT DevKit 常见问题解答的开发部分](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)。

## <a name="test-the-project"></a>测试项目

当 DevKit 所处环境中存在稳定的磁场时，此程序会首先初始化。

初始化以后，屏幕上会显示 `Door closed`。 磁场变化时，状态更改为 `Door opened`。 门状态一变化，你就会收到电子邮件通知。 （收到这些电子邮件可能需要长达五分钟的时间。）

![磁铁靠近传感器：门已关闭](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "磁铁靠近传感器：门已关闭")

![磁铁从传感器移开：门已打开](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "磁铁从传感器移开：门已打开")

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，请参阅 [IoT DevKit 常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)或通过以下渠道进行联系：

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>后续步骤

你已经了解了如何将 DevKit 设备连接到 Azure IoT 远程监视解决方案加速器并使用 SendGrid 服务来发送电子邮件。 下面是建议的后续步骤：

* [Azure IoT 远程监视解决方案加速器概述](https://docs.microsoft.com/azure/iot-suite/)
* [将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
