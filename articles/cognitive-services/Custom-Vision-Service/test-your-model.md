---
title: 测试和重新训练模型 - 自定义影像服务
titleSuffix: Azure Cognitive Services
description: 了解如何测试图像，然后将其用于重新训练模型。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 3f78f0b992581a44b030387f1bd0e37664df4cfd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560915"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>使用自定义影像服务测试和重新训练模型

训练模型后，可使用存储于本地的图像或联机图像快速进行测试。 该测试使用最近训练的迭代。

## <a name="test-your-model"></a>测试模型

1. 在[自定义影像服务网页](https://customvision.ai)选择项目。 选择顶部菜单栏右侧的“快速测试”。 随即打开一个标有“快速测试”的窗口。

    ![“快速测试”按钮位于窗口右上角。](./media/test-your-model/quick-test-button.png)

2. 在“快速测试”窗口中，单击“提交图像”字段，并输入要用于测试的图像的 URL。 如果要使用存储于本地的图像，请单击“浏览本地文件”按钮并选择本地图像文件。

    ![“提交图像”页的图像](./media/test-your-model/submit-image.png)

所选图像显示在页面中间。 然后，结果以表格形式显示在图像下方，其中包含两列，分别为“标记”和“置信度”。 查看结果后，可关闭“快速测试”窗口。

现可将此测试图像添加到模型，然后重新训练模型。

## <a name="use-the-predicted-image-for-training"></a>使用预测图像进行定型

要使用之前提交的图像进行训练，请执行以下步骤：

1. 若要查看提交给分类器的图像，请打开[自定义影像服务网页](https://customvision.ai)，然后选择“预测”选项卡。

    ![“预测”选项卡的图像](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > 默认视图显示当前迭代的图像。 可使用“迭代”下拉字段查看先前迭代期间提交的图像。

2. 将鼠标悬停在图像上可查看分类器预测的标记。

    > [!TIP]
    > 图像存在先后顺序，对分类器最有用的图像位于最上方。 若要选择不同排序依据，请使用“排序”部分。

    若要将图像添加到定型数据，请依次选择该图像和标记，然后选择“保存并关闭”。 图像将从“预测”中删除并添加到定型图像中。 可选择“定型图像”选项卡查看该图像。

    ![“标记”页面的图像](./media/test-your-model/tag-image.png)

3. 使用“定型”按钮重新定型分类器。

## <a name="next-steps"></a>后续步骤

[改进分类器](getting-started-improving-your-classifier.md)
