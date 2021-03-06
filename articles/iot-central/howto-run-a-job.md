---
title: 在 Azure IoT Central 应用程序中创建并运行作业 | Microsoft Docs
description: Azure IoT Central 作业允许批量设备管理功能，例如更新设备属性、设置或执行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 6c18a244ceae2ccd9a536abeb6bc2d85760bb0a6
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875911"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中创建并运行作业

可以使用 Microsoft Azure IoT Central 通过作业大规模地管理连接的设备。 作业允许你对设备属性、设置和命令进行批量更新。 本文介绍如何在自己的应用程序中开始使用作业。

## <a name="create-and-run-a-job"></a>创建并运行作业

此部分介绍如何创建并运行作业。 其中介绍了如何提高多个 refrigerated 自动售货机机的风扇速度。

1. 从导航窗格中，导航到“作业”。

1. 选择 " **+ 新建**" 以创建新作业。

    ![新建作业](./media/howto-run-a-job/createnewjob.png)

1. 输入名称和描述以标识要创建的作业。

1. 选择要将作业应用到的设备集。 选择设备后, 会看到右侧会填充设备集中的设备。 如果选择损坏的设备集, 则不会显示任何设备, 并且你会看到一条消息, 指出你的设备设置已断开。

1. 接下来, 选择要定义的作业类型 (设置、属性或命令)。 选择 **+** 所选作业类型旁边的 "添加", 并添加操作。

    ![配置作业](./media/howto-run-a-job/configurejob.png)

1. 在右侧, 选择要在其上运行作业的设备。 通过选中顶部的复选框, 将在整个设备集中选择所有设备。 选中 "**名称**" 旁边的复选框, 即可选择当前页面上的所有设备。

1. 选择设备后, 选择 "**运行**" 或 "**保存**"。 作业现在会显示在 "主**作业**" 页上。 在此视图中, 可以看到当前正在运行的作业和任何以前运行的作业的历史记录。 正在运行的作业始终显示在列表的顶部。 你保存的作业可以随时打开以继续编辑或运行。

    ![查看作业](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > 你可以查看以前运行的作业的历史记录最多30天。

1. 若要获取作业的概述, 请从列表中选择要查看的作业。 此概述包含作业详细信息、设备和设备状态值。 在此概述中, 你还可以选择 "**下载作业详细信息**" 以下载作业详细信息的 .csv 文件, 包括设备及其状态值。 此信息可用于故障排除。

    ![查看设备状态](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>停止正在运行的作业

若要停止正在运行的作业, 请选择它, 然后在面板上选择 "**停止**"。 作业状态将更改, 以反映作业已停止。

   ![停止作业](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>运行已停止的作业

若要运行当前已停止的作业, 请选择已停止的作业。 选择面板上的 "**运行**"。 作业状态将更改, 以反映作业现在正在运行。

   ![已恢复的作业](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>复制作业

若要复制已创建的现有作业, 请在 "主作业" 页中选择该作业, 然后选择 "**复制**"。 此时会打开一个新的作业配置副本供你编辑。 您可以保存或运行新作业。 如果对所选设备集进行了任何更改, 则这些更改将反映在此复制的作业中供你编辑。

   ![复制作业](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>查看作业状态

在创建作业后, "**状态**" 列将更新作业的最新状态消息。 下表列出了可能的状态值:

| 状态消息       | 状态的含义                                          |
| -------------------- | ------------------------------------------------------- |
| 已完成            | 此作业已在所有设备上执行。              |
| 已失败               | 此作业失败且未在设备上完全执行。  |
| 待处理              | 此作业尚未开始在设备上执行。         |
| 正在运行              | 此作业当前正在设备上执行。             |
| 已停止              | 此作业已由用户手动停止。           |

状态消息后跟作业中设备的概述。 下表列出了可能的设备状态值:

| 状态消息       | 状态的含义                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 已成功            | 成功执行该作业的设备数。       |
| 已失败               | 作业无法在其上执行的设备的数量。       |

### <a name="view-the-device-status"></a>查看设备状态

若要查看作业和所有受影响的设备的状态, 请选择该作业。 若要下载包含作业详细信息的 .csv 文件, 包括设备列表及其状态值, 请选择 "**下载作业详细信息**"。 在每个设备名称旁边, 你会看到以下状态消息之一:

| 状态消息       | 状态的含义                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| 已完成            | 作业已在此设备上执行。                                     |
| 已失败               | 作业在此设备上执行失败。 错误消息显示了详细信息。  |
| 待处理              | 作业尚未在此设备上执行。                                   |

> [!NOTE]
> 如果已删除设备, 则无法选择该设备, 并将其显示为 "已删除", 并显示设备 ID。

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何在 Azure IoT Central 应用程序中创建作业, 以下是一些后续步骤:

- [使用设备集](howto-use-device-sets.md)
- [管理设备](howto-manage-devices.md)
- [对设备模板进行版本控制](howto-version-device-template.md)
