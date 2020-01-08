---
title: 如何向 Azure Maps 提供数据反馈 |Microsoft Docs
description: 使用 Azure Maps 反馈工具提供数据反馈。
author: walsehgal
ms.author: v-musehg
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 076f98cb240014bcc88a395902203413e31fe0f1
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642118"
---
# <a name="provide-data-feedback-to-azure-maps"></a>向 Azure Maps 提供数据反馈

从2018年5月起, Azure Maps 通常是可用的, 它提供了新的映射数据、易于使用的 REST Api 和强大的 Sdk, 以便在各种业务用例上支持我们的企业客户。 现实世界每秒都在发生变化, 为我们的客户提供事实数字表示形式至关重要。 计划打开或关闭设施的客户需要确保正确更新我们的地图, 使其能够在适当的设施有效地计划交付、维护或客户服务。 我们创建了 Azure Maps 数据反馈工具来使客户能够提供直接的数据反馈。 客户的数据反馈直接发送到我们的数据提供商及其地图编辑器, 他们可以快速评估并将反馈纳入我们的地图产品。  

[Azure Maps 数据反馈工具](https://feedback.azuremaps.com)为我们的客户提供了一种简单的方法来提供地图数据反馈, 尤其是在相关业务点和居住地址上。 本文介绍如何使用 Azure Maps 反馈工具提供不同类型的反馈。

## <a name="add-a-business-place-or-a-residential-address"></a>添加办公地点或住宅地址 

你可能希望为地图上缺少的兴趣点或住宅地址提供反馈。 可以通过两种方式来执行此操作, 请打开 Azure 映射数据反馈工具, 搜索缺少位置的坐标, 然后单击 "添加位置"

  ![搜索缺少的位置](./media/how-to-use-feedback-tool/search-poi.png)

或者, 您可以与地图进行交互, 并单击该位置以在坐标处删除 pin, 然后单击 "添加位置"。 

  ![添加 pin](./media/how-to-use-feedback-tool/add-poi.png)

单击时, 会将您定向到一个窗体, 以便为该位置提供相应的详细信息。

  ![添加位置](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>修复业务场所或住宅地址 

使用反馈工具, 还可以搜索和查找业务地点或地址, 并提供反馈来修复地址或 pin 位置 (如果不正确)。 若要提供反馈来修复地址, 请使用搜索栏搜索企业位置或住宅地址。 在结果列表中单击你感兴趣的位置, 并单击 "修复此位置"。

  ![要修复的搜索位置](./media/how-to-use-feedback-tool/fix-place.png)

若要提供反馈来修复地址, 请填写 "修复位置" 窗体, 然后单击 "提交" 按钮。

  ![修复窗体](./media/how-to-use-feedback-tool/fix-form.png)

如果位置的 pin 位置错误, 请选中 "修复位置" 窗体上的复选框, 该复选框显示 "pin 位置不正确", 然后将 pin 移到正确的位置, 然后单击 "提交" 按钮。

  ![移动 pin 位置](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>添加注释 

除了允许搜索位置之外, 还可以使用反馈工具为与位置相关的详细信息添加自由格式的文本注释。 若要为该位置添加注释搜索, 或者单击 "位置", 然后单击 "添加评论", 请编写注释, 然后单击 "提交"。 

  ![添加注释](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>跟踪状态 

还可以通过选中 "我想跟踪状态" 框并在发出请求时提供电子邮件, 来跟踪请求的状态。 你将在电子邮件中收到跟踪链接, 该链接为你的请求提供最新状态。 

  ![反馈状态](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>后续步骤

若要发布与 Azure Maps 相关的任何技术问题, 请访问:

* [Azure Maps Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure Maps 反馈论坛](https://feedback.azure.com/forums/909172-azure-maps)