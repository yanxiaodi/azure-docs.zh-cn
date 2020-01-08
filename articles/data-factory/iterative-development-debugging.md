---
title: 在 Azure 数据工厂中进行迭代开发和调试 | Microsoft Docs
description: 了解如何在 Azure 门户中以迭代方式开发和调试数据工厂管道。
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: djpmsft
ms.author: daperlov
manager: craigg
ms.openlocfilehash: a1c4bf55fbb8242293fc0e30bbe16a6884b901cb
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178623"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>使用 Azure 数据工厂进行迭代开发和调试

Azure 数据工厂允许以迭代方式开发和调试数据工厂管道。

有关此功能的 8 分钟简介和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>迭代的调试功能
使用管道画布中的**调试**功能创建管道并执行测试运行，不需要编写一行代码。

![管道画布上的调试功能](media/iterative-development-debugging/iterative-development-image1.png)

在管道画布的“输出”窗口中查看测试运行的结果。

![管道画布“输出”窗口](media/iterative-development-debugging/iterative-development-image2.png)

在测试运行成功后，向管道中添加更多活动并继续以迭代方式进行调试。 还可以在测试运行正在执行时将其**取消**。

![取消测试运行](media/iterative-development-debugging/iterative-development-image3.png)

执行测试运行时，在选择“调试”之前，不需要将更改发布到数据工厂。 当希望确保更改在你更新数据工厂工作流之前按预期工作时，此功能很有帮助。

> [!IMPORTANT]
> 选择”调试”会实际运行管道。 因此，例如，如果管道包含复制活动，则测试运行会将数据从源复制到目标。 因此，在调试时，建议在复制活动和其他活动中使用测试文件夹。 在调试管道后，切换到要在正常操作中使用的实际文件夹。

## <a name="visualizing-debug-runs"></a>可视化调试运行

可以在一个位置可视化数据工厂正在进行的所有调试运行。 选择页面右上角的“查看调试运行”。 在使用主管道启动子管道的调试运行并且希望用单个视图查看所有活动的调试运行的情况下，此功能非常有用。

![选择查看活动调试运行图标](media/iterative-development-debugging/view-debug-runs-image1.png)

![活动调试运行的示例列表](media/iterative-development-debugging/view-debug-runs-image2.png)

如果有活动的数据流调试会话，则这些会话将显示在活动调试窗口的底部。 您可以选择一个活动数据流会话并停止各自的群集。

![活动数据流调试运行的示例列表](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>监视调试运行

通过**调试**功能启动的测试运行不会显示在“监视器”选项卡上的列表中。在“监视器”选项卡上，只能查看通过“立即触发”、“计划”或“翻转窗口”触发器触发的运行。可以在管道画布的“输出”窗口中查看通过**调试**功能启动的最后一个测试运行。

## <a name="setting-breakpoints-for-debugging"></a>设置用于调试的断点

数据工厂还允许一直调试到管道画布中的某个特定活动。 只需要在活动上放置要测试到的一个断点，然后选择“调试”即可。 数据工厂会确保测试仅运行到管道画布上的断点活动。 如果不想测试整个管道，只想测试该管道内的一部分活动，则此“调试至”功能非常有用。

![管道画布上的断点](media/iterative-development-debugging/iterative-development-image4.png)

若要设置断点，请选择管道画布上的元素。 “调试至”选项在元素的右上角显示为空心的红色圆圈。

![在所选元素上设置断点之前](media/iterative-development-debugging/iterative-development-image5.png)

选择“调试至”选项后，它将变为实心的红色圆圈，以指示已启用断点。

![在所选元素上设置断点之后](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>后续步骤
[在 Azure 数据工厂中进行持续集成和部署](continuous-integration-deployment.md)
