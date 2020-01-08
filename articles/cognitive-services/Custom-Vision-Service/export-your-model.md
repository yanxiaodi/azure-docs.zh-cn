---
title: 将模型导出到移动设备 - 自定义视觉服务
titleSuffix: Azure Cognitive Services
description: 了解如何导出模型以用于创建移动应用程序。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: anroth
ms.openlocfilehash: 554a392a7f815a6e646927f137b1e6c2856099bd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561086"
---
# <a name="export-your-model-for-use-with-mobile-devices"></a>导出模型以用于移动设备

自定义影像服务支持导出分类器以脱机运行。 可以将导出的分类器嵌入应用程序，并在设备上本地运行，进行实时分类。

自定义影像服务支持导出以下内容：

* 用于 Android 的 Tensorflow。
* 用于 iOS11 的 CoreML。
* 用于 Windows ML 的 ONNX。
* Windows 或 Linux 容器。 容器包括 Tensorflow 模型和服务代码，可用于使用自定义影像服务 API。 

> [!IMPORTANT]
> 自定义影像服务仅导出压缩域。 已针对移动设备上实时分类的约束对压缩域生成的模型进行了优化。 面对相同数量的定型数据时，用压缩域生成的分类器比用标准域生成的分类器的准确度略低。
>
> 有关如何改进分类器的信息，请参阅[改进分类器](getting-started-improving-your-classifier.md)文档。

## <a name="convert-to-a-compact-domain"></a>转换为压缩域

> [!NOTE]
> 本部分中的步骤仅适用于现有分类器未设置为压缩域的情况。

若要转换现有分类器的域，请采用以下步骤：

1. 在[自定义影像页面](https://customvision.ai)，选择“主页”图标以查看项目列表。 也可以访问 [https://customvision.ai/projects](https://customvision.ai/projects) 查看项目。

    ![“主页”图标和项目列表的图像](./media/export-your-model/projects-list.png)

2. 选择一个项目，然后选择页面右上角的齿轮图标。

    ![齿轮图标的图像](./media/export-your-model/gear-icon.png)

3. 在“域”部分，选择一个压缩域。 选择“保存更改”来保存更改。

    ![域选项的图像](./media/export-your-model/domains.png)

4. 从页面顶部选择“定型”以使用新域进行重新定型。

## <a name="export-your-model"></a>导出模型

若要在重新定型后导出模型，请采用以下步骤：

1. 转到“性能”选项卡并选择“导出”。 

    ![导出图标的图像](./media/export-your-model/export.png)

    > [!TIP]
    > 如果“导出”项不可用，则表示所选迭代未使用压缩域。 使用此页面中的“迭代”部分，选择使用压缩域的迭代，然后选择“导出”。

2. 选择导出格式，然后选择“导出”以下载该模型。

## <a name="next-steps"></a>后续步骤

通过浏览以下文章或示例之一将导出的模型集成到应用程序中:

* [将 Tensorflow 模型与 Python 配合使用](export-model-python.md)
* [将 ONNX 模型用于 Windows 机器学习](custom-vision-onnx-windows-ml.md)
* 请参阅[iOS 应用程序中的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)示例, 以便使用 Swift 实现实时映像分类。
* 请参阅 android[应用程序中的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)示例, 了解 android 上的实时图像分类。
* 请参阅在 Xamarin iOS 应用中使用 Xamarin 进行实时映像分类的[CoreML 模型](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)示例。
