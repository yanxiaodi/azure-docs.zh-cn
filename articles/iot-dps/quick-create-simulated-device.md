---
title: 使用 C 将模拟的 TPM 设备预配到 Azure IoT 中心 | Microsoft Docs
description: 本快速入门使用单独注册。 在本快速入门中，请使用适用于 Azure IoT 中心设备预配服务的 C 设备 SDK 创建和预配模拟的 TPM 设备。
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ca6914967d855123c70bf746a9d68d2e045e76d9
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65908675"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>快速入门：使用 Azure IoT C SDK 预配模拟的 TPM 设备

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

本快速入门介绍如何在 Windows 开发计算机上创建和运行受信任平台模块 (TPM) 设备模拟器。 然后使用设备预配服务实例将此模拟设备连接到 IoT 中心。 我们将借助 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的示例代码在设备预配服务实例中注册设备，并模拟设备的启动序列。

如果不熟悉自动预配过程，请查看[自动预配的概念](concepts-auto-provisioning.md)。 另外，在继续学习本快速入门之前，请确保已完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。 

Azure IoT 设备预配服务支持两类注册：
- [注册组](concepts-service.md#enrollment-group)：用于注册多个相关设备。
- [单独注册](concepts-service.md#individual-enrollment)：用于注册单个设备。

本文将演示单个注册。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 启用了[“使用 C++ 的桌面开发”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作负荷的 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 或更高版本。
* 已安装最新版本的 [Git](https://git-scm.com/download/)。


<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>为 Azure IoT C SDK 准备开发环境

在本部分，我们将准备一个用于生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 和 [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) 设备模拟器示例的开发环境。

1. 下载 [CMake 生成系统](https://cmake.org/download/)。

    在进行 `CMake` 安装**之前**，必须在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负荷）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。

2. 打开命令提示符或 Git Bash shell。 执行以下命令克隆 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    应该预料到此操作需要几分钟才能完成。


3. 在 git 存储库的根目录中创建 `cmake` 子目录，并导航到该文件夹。 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>生成 SDK 并运行 TPM 设备模拟器

在本部分，我们将生成包含 TPM 设备模拟器示例代码的 Azure IoT C SDK。 此示例通过共享访问签名 (SAS) 令牌身份验证提供 TPM [证明机制](concepts-security.md#attestation-mechanism)。

1. 从 azure-iot-sdk-c git 存储库中创建的 `cmake` 子目录，运行以下命令以生成示例。 此生成命令还将生成模拟设备的 Visual Studio 解决方案。

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    如果 `cmake` 找不到 C++ 编译器，则可能会在运行以上命令时出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)窗口中运行该命令。 

    生成成功后，最后的几个输出行如下所示：

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. 导航到克隆的 git 存储库的根文件夹，并使用如下所示的路径运行 [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) 模拟器。 此模拟器通过端口 2321 和 2322 上的套接字进行侦听。 请勿关闭此命令窗口；本快速入门自始至终都需要让此模拟器保持运行。 

   如果在 *cmake* 文件夹中，则请运行以下命令：

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    模拟器未返回任何输出。 请让它继续模拟 TPM 设备。

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>从 TPM 设备读取加密密钥

在本部分，我们将生成并执行一个示例，以便从保持运行的、通过端口 2321 和 2322 侦听的 TPM 模拟器中读取认可密钥和注册 ID。 这些值用于将设备注册到设备预配服务实例。

1. 启动 Visual Studio 并打开名为 `azure_iot_sdks.sln` 的新解决方案文件。 此解决方案文件位于先前在 azure-iot-sdk-c git 存储库的根目录中创建的 `cmake` 文件夹中。

2. 在 Visual Studio 菜单中，选择“生成” > “生成解决方案”以生成解决方案中的所有项目。  

3. 在 Visual Studio 的“解决方案资源管理器”窗口中，导航到 **Provision\_Tools** 文件夹。  右键单击“tpm_device_provision”项目，  然后选择“设为启动项目”。  

4. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。   应用将读取并显示 **_注册 ID_** 和 **_认可密钥_** 。 复制这些值。 在下一部分，这些值将用于设备注册。 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>在门户中创建设备注册项

1. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开设备预配服务  。

2. 选择“管理注册”选项卡，然后单击顶部的“添加个人注册”按钮。   

3. 在“添加注册”中输入以下信息，然后单击“保存”按钮。  

    - **机制：** 选择“TPM”  作为标识证明*机制*。
    - **认可密钥：** 输入通过运行 tpm_device_provision 项目为 TPM 设备生成的“认可密钥”   。
    - **注册 ID：** 输入通过运行 tpm_device_provision 项目为 TPM 设备生成的“注册 ID”   。
    - **IoT Edge 设备：** 选择“禁用”。 
    - **IoT 中心设备 ID：** 输入 test-docs-device 作为设备的 ID  。

      ![在门户中输入设备注册信息](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      成功注册以后，设备的“注册 ID”会显示在“单个注册”选项卡下的列表中。   


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>模拟设备的首次启动顺序

在本部分，我们将示例代码配置为使用[高级消息队列协议 (AMQP)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) 向设备预配服务实例发送设备的启动序列。 此启动序列使得设备可被识别并分配到与设备预配服务实例链接的 IoT 中心。

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡，并复制“ID 范围”值。     

    ![从门户中提取设备预配服务终结点信息](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. 在 Visual Studio 的“解决方案资源管理器”窗口中，导航到 **Provision\_Samples** 文件夹。  展开名为 **prov\_dev\_client\_sample** 的示例项目。 展开“源文件”，打开 **prov\_dev\_client\_sample.c**。 

3. 在该文件的顶部附近，找到每个设备协议的 `#define` 语句，如下所示。 确保仅取消注释 `SAMPLE_AMQP`。

    目前，[TPM 个人注册不支持 MQTT 协议](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk)。

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. 找到 `id_scope` 常量，将值替换为前面复制的“ID 范围”值。  

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 在同一文件中找到 `main()` 函数的定义。 确保 `hsm_type` 变量设置为 `SECURE_DEVICE_TYPE_TPM` 而不是 `SECURE_DEVICE_TYPE_X509`，如下所示。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. 右键单击“prov\_dev\_client\_sample”项目，  然后选择“设为启动项目”。  

7. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。   在重新生成项目的提示中单击“是”，以便在运行项目之前重新生成项目。 

    以下输出示例显示预配设备客户端示例成功启动，然后连接到设备预配服务实例来获取 IoT 中心信息并注册：

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. 预配服务将模拟设备预配到 IoT 中心后，中心的“IoT 设备”中会显示设备 ID。  

    ![设备注册到 IoT 中心](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请勿清理在本快速入门中创建的资源。 如果不打算继续学习，请通过以下步骤删除通过本快速入门创建的所有资源。

1. 关闭计算机上的设备客户端示例输出窗口。
2. 关闭计算机上的 TPM 模拟器窗口。
3. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择设备预配服务  。 打开服务的“管理注册”，然后单击“个人注册”选项卡。   选择在本快速入门中注册的设备的“注册 ID”，然后单击顶部的“删除”按钮。   
4. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择 IoT 中心  。 打开中心的“IoT 设备”，选择在本快速入门中注册的设备的“设备 ID”，然后单击顶部的“删除”按钮。   

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已在计算机上创建 TPM 模拟设备，并已使用 IoT 中心设备预配服务将其预配到 IoT 中心。 若要了解如何以编程方式注册 TPM 设备，请继续阅读快速入门中关于 TPM 设备的编程注册内容。 

> [!div class="nextstepaction"]
> [Azure 快速入门 - 将 TPM 设备注册到 Azure IoT 中心设备预配服务](quick-enroll-device-tpm-java.md)

