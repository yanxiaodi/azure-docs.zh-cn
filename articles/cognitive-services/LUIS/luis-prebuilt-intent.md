---
title: 为语言理解预生成意向
titleSuffix: Azure Cognitive Services
description: LUIS 提供了一组预生成意向，用以快速添加常用的对话式用户方案。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 4b14d9f39ef5bb0806cfe0720dbaa2fe95b32421
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932613"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>为常用意向添加预生成意向 

LUIS 通过预生成域提供了一组预生成意向，用以快速添加常用的意向和话语。 这是一种快速简单地向对话式客户端应用添加功能的方法，不需要为这些功能设计模型。 

## <a name="add-a-prebuilt-intent"></a>添加预生成意向

1. 在“我的应用”页面上，选择你的应用。 这将打开你的应用并显示该应用的“生成”部分。 

1. 在“意向”页面上，从意向列表上方的工具栏中选择“添加预生成意向”。 

1. 从弹出对话框中选择“Utilities.Cancel”意向。 

    ![添加预生成意向](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. 选择“完成”按钮。

## <a name="train-and-test"></a>训练和测试

1. 添加意向后，通过在右上角的工具栏中选择“训练”来训练应用。 

1. 通过在右侧的工具栏中选择“测试”来测试新意向。 

1. 在文本框中，输入用于取消的话语：

    |测试话语|预测分数|
    |--|:--|
    |我想取消我的航班。|0.67|
    |取消购买。|0.52|
    |取消会议。|0.56|

    ![测试预生成意向](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [预生成实体](./luis-prebuilt-entities.md)
