---
title: Azure IoT 中心的手动故障转移 | Microsoft Docs
description: 介绍如何为 Azure IoT 中心执行手动故障转移
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 308e452f33ded9be3b88ff370ed34326de54895c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877045"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>教程：为 IoT 中心执行手动故障转移

手动故障转移是 IoT 中心服务的一项功能，允许客户将其中心的操作从主要区域[故障转移](https://en.wikipedia.org/wiki/Failover)到相应的 Azure 异地配对区域。 在出现区域性灾难或者服务中断时间延长的情况下，可以进行手动故障转移。 也可通过执行计划内故障转移来测试灾难恢复功能，虽然我们建议使用测试性 IoT 中心而不是在生产环境中运行的 IoT 中心。 提供给客户的手动故障转移功能不另外收费。

将在本教程中执行以下任务：

> [!div class="checklist"]
> * 使用 Azure 门户创建 IoT 中心。 
> * 执行故障转移。 
> * 查看在辅助位置运行的中心。
> * 执行故障回复，让 IoT 中心的操作返回到主位置。 
> * 确认中心在正确的位置正确运行。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 单击“+ 创建资源”，选择“物联网”，然后选择“IoT 中心”。   

   ![显示正在创建 IoT 中心的屏幕截图](./media/tutorial-manual-failover/create-hub-01.png)

3. 选择“基本信息”  选项卡。在以下字段中进行填充。

    **订阅**：选择要使用的 Azure 订阅。

    **资源组**：单击“新建”，指定“ManlFailRG”作为资源组名称。  

    **区域**：选择离你近的区域。 本教程使用 `West US 2`。 只能在 Azure 异地配对区域之间执行故障转移。 与“美国西部 2”异地配对的区域是 WestCentralUS。
    
   **Iot 中心名称**：指定 IoT 中心的名称。 该中心名称必须在全局中独一无二。 

   ![显示用于创建 IoT 中心的“基本信息”窗格的屏幕截图](./media/tutorial-manual-failover/create-hub-02-basics.png)

   单击“查看 + 创建”  。 （它使用大小和规模的默认值。） 

4. 查看信息，然后单击“创建”以创建 IoT 中心。  

   ![显示用于创建 IoT 中心的最后步骤的屏幕截图](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>执行手动故障转移

请注意，一个 IoT 中心每天的限制是两次故障转移和两次故障回复。

1. 单击“资源组”，然后选择资源组“ManlFailRG”   。 在资源列表中单击你的中心。 

1. 在 IoT Hub 窗格上的“设置”  下，单击“故障转移”  。

   ![显示 IoT 中心属性窗格的屏幕截图](./media/tutorial-manual-failover/trigger-failover-01.png)

1. 在“手动故障转移”窗格上，可以看到**当前位置**和**故障转移位置**。 当前位置始终指示中心当前处于活动状态的位置。 故障转移位置是标准的 [Azure 异地配对区域](../best-practices-availability-paired-regions.md)，与当前位置配对。 不能更改位置值。 在本教程中，当前位置为 `West US 2`，故障转移位置为 `West Central US`。

   ![显示“手动故障转移”窗格的屏幕截图](./media/tutorial-manual-failover/trigger-failover-02.png)

1. 在“手动故障转移”窗格顶部单击“启动故障转移”。  

1. 在“确认”窗格中填充 IoT 中心的名称，确认是它需要故障转移。 然后，若要启动故障转移，请单击“故障转移”  。

   执行手动故障转移所需时间与中心的已注册设备数成正比。 例如，如果有 1 百万台设备，可能需要 15 分钟，但如果有 5 百万台设备，则可能需要 1 小时或更长的时间。

   ![显示“手动故障转移”窗格的屏幕截图](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   在手动故障转移进程正在运行时，会显示一个横幅，告诉你正在进行手动故障转移。 

   ![显示手动故障转移正在进行的屏幕截图](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   如果在关闭“IoT 中心”窗格后又在“资源组”窗格中通过单击来打开它，则会看到一个横幅，告诉你该中心正在进行手动故障转移。 

   ![显示 IoT 中心正在进行故障转移的屏幕截图](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   故障转移完成后，“手动故障转移”页上的当前区域和故障转移区域会互换，中心重新处于活动状态。 在本示例中，当前位置现在为 `WestCentralUS`，故障转移位置现在为 `West US 2`。 

   ![显示故障转移已完成的屏幕截图](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   “概述”页还显示一个横幅，指示故障转移已完成，并且 IoT 中心正在 `West Central US` 中运行。

   ![“概述”页中显示故障转移已完成的屏幕截图](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>执行故障回复 

执行手动故障转移以后，可以将中心的操作切换回原始的主要区域 -- 这称为故障回复。 如果刚执行故障转移，则需等待大约一小时，然后才能请求故障回复。 如果尝试在比这更短的时间内执行故障回复，则会显示错误消息。

故障回复的执行方式与手动故障转移一样。 下面是步骤： 

1. 若要执行故障回复，请返回到 Iot 中心的“Iot 中心”窗格。

2. 在 IoT Hub 窗格上的“设置”  下，单击“故障转移”  。 

3. 在“手动故障转移”窗格顶部单击“启动故障转移”。  

4. 在“确认”窗格中填充 IoT 中心的名称，确认是它需要故障回复。 然后，若要启动故障回复，请单击“确定”。 

   ![手动故障回复请求的屏幕截图](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   横幅会如“执行故障转移”部分所述一样显示。 故障回复完成之后，它会再次将 `West US 2` 显示为当前位置，将 `West Central US` 显示为故障转移位置，就像初始设置的一样。

## <a name="clean-up-resources"></a>清理资源 

若要删除为本教程创建的资源，请删除资源组。 此操作会一并删除组中包含的所有资源。 在本示例中，它会删除 IoT 中心和资源组本身。 

1. 单击“资源组”。  

2. 找到并选择资源组“ManlFailRG”。  单击可将其打开。 

3. 单击“删除资源组”。  系统提示时，请输入资源组的名称，然后单击“删除”进行确认  。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何执行以下任务，以便配置并执行手动故障转移以及请求故障回复：

> [!div class="checklist"]
> * 使用 Azure 门户创建 IoT 中心。 
> * 执行故障转移。 
> * 查看在辅助位置运行的中心。
> * 执行故障回复，让 IoT 中心的操作返回到主位置。 
> * 确认中心在正确的位置正确运行。

转到下一教程，了解如何管理 IoT 设备的状态。 

> [!div class="nextstepaction"]
> [管理 IoT 设备的状态](tutorial-device-twins.md)
