---
title: 在 Microsoft Flow 中使用 Azure IoT Central 连接器生成工作流 | Microsoft Docs
description: 使用 Microsoft Flow 中的 IoT Central 连接器来触发工作流, 并创建、获取、更新、删除设备, 并在工作流中运行命令。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: a972ab52f64a37ac6876194202324b4380460817
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050570"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>在 Microsoft Flow 中使用 IoT Central 连接器生成工作流

本主题适用于构建者和管理员。

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

使用 Microsoft Flow 跨企业用户依赖的多个应用程序和服务自动完成工作流。 如果在 Microsoft Flow 中使用 IoT Central 连接器，则当规则在 IoT Central 中触发时，就可以触发工作流。 在 IoT Central 或任何其他应用程序触发的工作流中, 可以使用 IoT Central 连接器中的操作来执行以下操作:
- 创建设备
- 获取设备信息
- 更新设备的属性和设置
- 在设备上运行命令
- 删除设备

请检查[这些 Microsoft Flow 模板](https://aka.ms/iotcentralflowtemplates)，此类模板可以将 IoT Central 连接到其他服务，例如移动通知和 Microsoft Teams。

## <a name="prerequisites"></a>先决条件

- 即用即付应用程序
- 要使用 Microsoft Flow 的 Microsoft 个人或工作或学校帐户 ([详细了解 Microsoft Flow 计划](https://aka.ms/microsoftflowplans))
- 使用 Azure IoT Central 连接器的工作或学校帐户

## <a name="trigger-a-workflow"></a>触发工作流

本部分说明如何在 IoT Central 中触发规则时, 在 Flow 移动应用中触发移动通知。 使用嵌入的 Microsoft Flow 设计器, 可以在 IoT Central 应用程序中生成整个工作流。

1. 首先[在 IoT Central 中创建规则](howto-create-telemetry-rules.md)。 保存规则条件后, 选择 "Microsoft Flow"**操作**作为新操作。 将打开一个对话框窗口, 用于配置工作流。 登录到的 IoT Central 用户帐户将用于登录 Microsoft Flow。

    ![新建 Microsoft Flow 操作](media/howto-add-microsoft-flow/createflowaction.png)

1. 你将看到你具有访问权限的工作流列表, 并附加到此 IoT Central 规则。 单击 "**浏览模板**" 或 "**新建 > 从模板创建**", 并可以从任何可用的模板中进行选择。 

    ![可用 Microsoft Flow 模板](media/howto-add-microsoft-flow/flowtemplates1.png)

1. 系统将提示你登录到所选模板中的连接器。 

    > [!NOTE]
    > 若要使用 Azure IoT Central 连接器, 必须使用 Azure Active Directory 帐户 (工作或学校帐户) 登录。 Azure IoT Central 连接器不支持abc@outlook.com个人abc@live.com帐户, 如或。

    登录到连接器后, 你将在设计器中安装以生成工作流。 工作流有一个 IoT Central 触发器，其中已填充应用程序和规则。

1. 您可以通过自定义传递给操作的信息并添加新操作来自定义工作流。 在此示例中, 操作为 "**通知-向我发送移动通知**"。 可以包括 IoT Central 规则提供的动态内容，将设备名称和时间戳等重要信息传递给通知。

    > [!NOTE]
    > 选择 "动态内容" 窗口中的 "**查看更多**文本", 以获取触发规则的度量值和属性值。

    ![Flow 编辑操作，此时动态窗格处于打开状态](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. 完成操作编辑后, 请选择 "**保存**"。 此时会定向到工作流的概览页。 在这里可以看到运行历史记录，并可将它与其他同事共享。

    > [!NOTE]
    > 如果希望 IoT Central 应用中的其他用户编辑此规则，则必须在 Microsoft Flow 中将其与这些用户共享。 在工作流中将这些用户的 Microsoft Flow 帐户作为所有者添加。

1. 如果你返回 IoT Central 的应用, 则在 "操作" 区域中会显示此规则具有 Microsoft Flow 操作。

还可以直接从 Microsoft Flow 使用 IoT Central 连接器生成工作流。 然后, 可以选择要连接到哪个 IoT Central 应用。

## <a name="create-a-device-in-a-workflow"></a>在工作流中创建设备

此部分介绍如何在移动设备上使用 Microsoft Flow 移动应用通过按钮在 IoT Central 中创建新设备。 可以在 Flow 中使用此操作将 Dynamics 之类的 ERP 系统与 IoT Central 集成，方法是创建一个新设备（如果在其他位置添加了设备）。

1. 一开始在 Microsoft Flow 中创建空白工作流。

1. 搜索作为触发器的“适用于移动设备的流按钮”。

1. 在此触发器中，添加名为“设备名称”的文本输入。 将说明文本更改为“输入新设备的设备名称”。

1. 添加新操作。 搜索“Azure IoT Central - 创建设备”操作。

1. 在下拉列表中选取应用程序，然后选择用于创建设备的设备模板。 此时会看到操作展开，显示设备的所有属性和设置。

1. 选择“设备名称”字段。 在动态内容窗格中，选择“设备名称”。 此值从用户通过移动应用输入的输入传递, 是 IoT Central 中新设备的名称。 在此示例中，唯一必填字段为设备名称，以红色星号指示。 另一设备模板可能有多个必填字段，需填充这些字段才能创建新设备。

    ![Flow 的创建设备操作动态窗格](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. （可选）根据需要在其他字段中填充内容，以便创建新设备。

1. 最后，保存工作流。

1. 在 Microsoft Flow 移动应用中试用工作流。 转到应用中的“按钮”选项卡。 此时会看到“按钮 -> 创建新设备”工作流。 输入新设备的名称，观察其在 IoT Central 中显示！

    ![Flow 的创建设备移动屏幕截图](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>在工作流中更新设备

此部分介绍如何在移动设备上使用 Microsoft Flow 移动应用通过按钮在 IoT Central 中更新设备设置和属性。

1. 一开始在 Microsoft Flow 中创建空白工作流。

1. 搜索作为触发器的“适用于移动设备的流按钮”。

1. 在此触发器中，请添加一个输入，例如一个与要更改的设置或属性对应的“位置”文本输入。 更改说明文本。

1. 添加新操作。 搜索“Azure IoT Central - 更新设备”操作。

1. 从下拉菜单中选取应用程序。 现在需要 ID，该 ID 属于要更新的现有设备。 

    > [!NOTE] 
    > **您必须使用**要更新的设备的 "设备详细信息" 页上 URL 中的 ID。 设备资源管理器的设备列表中找到的设备 ID 不是 Microsoft Flow 中使用的设备 ID。

    ![URL 中的 IoT Central ID](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. 可以更新设备名称。 若要更新设备的属性和设置，必须在“设备模板”下拉列表中选择要更新的设备对应的设备模板。 此时操作磁贴会展开，显示可更新的所有属性和设置。

    ![Flow 的更新设备工作流](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. 选择要更新的每个属性和设置。 在动态内容窗格的触发器中，选择相应的输入。 在此示例中，“位置”值会向下传播，以便更新设备的“位置”属性。

1. 最后，保存工作流。

1. 在 Microsoft Flow 移动应用中试用工作流。 转到应用中的“按钮”选项卡。 此时会看到“按钮 -> 更新设备”工作流。 输入输入内容，此时会看到设备在 IoT Central 中进行了更新！

## <a name="get-device-information-in-a-workflow"></a>获取工作流中的设备信息

可以使用**Azure IoT Central 获取设备**操作, 通过其 ID 获取设备信息。 
> [!NOTE] 
> **您必须使用**要更新的设备的 "设备详细信息" 页上 URL 中的 ID。 设备资源管理器的设备列表中找到的设备 ID 不是 Microsoft Flow 中使用的设备 ID。

你可以获取信息, 例如设备名称、设备模板名称、属性值以及要传递给工作流中的后续操作的设置值。 下面是一个示例工作流, 该工作流将客户名称属性值从设备传递到 Microsoft 团队。

   ![流获取设备工作流](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>在工作流中的设备上运行命令
你可以使用**Azure IoT Central 运行命令**操作在其 ID 指定的设备上运行命令。 

> [!NOTE] 
> **您必须使用**要更新的设备的 "设备详细信息" 页上 URL 中的 ID。 设备资源管理器的设备列表中找到的设备 ID 不是 Microsoft Flow 中使用的设备 ID。
    
你可以选择要运行的命令, 并通过此操作传递命令的参数。 下面是一个示例工作流, 它通过 Microsoft Flow 移动应用中的按钮运行设备重新启动命令。

   ![流获取设备工作流](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>在工作流中删除设备

可以使用 " **Azure IoT Central-删除设备**" 操作, 按设备 ID 删除设备。 
> [!NOTE] 
> **您必须使用**要更新的设备的 "设备详细信息" 页上 URL 中的 ID。 设备资源管理器的设备列表中找到的设备 ID 不是 Microsoft Flow 中使用的设备 ID。

下面是一个示例工作流，此工作流在 Microsoft Flow 移动应用中通过按钮来删除设备。

   ![Flow 的删除设备工作流](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>疑难解答

如果无法创建到 Azure IoT Central 连接器的连接，请参考以下提示。

1. Microsoft 个人帐户（例如 @hotmail.com、@live.com、@outlook.com 域）目前不受支持。 必须使用 Azure Active Directory (AD) 工作或学校帐户。

2. 必须至少已登录到 IoT Central 应用程序一次，才能在 Microsoft Flow 中使用 IoT Central 连接器。 否则此应用程序将无法在“应用程序”下拉菜单中显示。

3. 如果使用 Azure AD 帐户时收到错误, 请尝试以管理员身份打开 Windows PowerShell 并运行以下 commandlet。

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何使用 Microsoft Flow 来构建工作流, 接下来的建议是[管理设备](howto-manage-devices.md)。

