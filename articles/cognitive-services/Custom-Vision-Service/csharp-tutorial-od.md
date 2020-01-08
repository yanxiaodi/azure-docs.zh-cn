---
title: 快速入门：使用适用于 C# 的自定义视觉 SDK 创建对象检测项目
titleSuffix: Azure Cognitive Services
description: 使用 .NET SDK 和 C# 创建项目、添加标记、上传图像、训练项目以及检测对象。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: areddish
ms.openlocfilehash: 34b814e854a1576fcf55d14ddc5ac213d8f87070
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945167"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>快速入门：使用自定义视觉 .NET SDK 创建对象检测项目

本文提供信息和示例代码，以帮助你开始通过 C# 使用自定义视觉 SDK 来构建对象检测模型。 创建该项目后，可以添加标记的区域、上传图像、训练项目、获取项目的默认预测终结点 URL 并使用终结点以编程方式测试图像。 使用此示例作为构建自己的 .NET 应用程序的模板。 

## <a name="prerequisites"></a>先决条件

- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>获取自定义视觉 SDK 和示例代码

若要编写使用自定义视觉的 .NET 应用，需要自定义视觉 NuGet 包。 这些包将包含在要下载的示例项目中，但你可以在此处逐个访问它们。

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

克隆或下载[认知服务 .NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)项目。 导航到 **CustomVision/ObjectDetection** 文件夹，在 Visual Studio 中打开 _ObjectDetection.csproj_。

此 Visual Studio 项目会新建名为“我的新项目”的自定义视觉项目，该项目可以通过[自定义视觉网站](https://customvision.ai/)进行访问。  然后，该项目会上传图像来训练和测试对象检测模型。 在此项目中，会训练模型对图像中的叉子和剪刀进行检测。

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>了解代码

打开 _Program.cs_ 文件并检查代码。 在 **Main** 方法的相应定义中插入订阅密钥。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=18-27)]

Endpoint 参数应指向创建包含自定义视觉资源的 Azure 资源组的区域。 对于此示例，我们假定“美国中南部”区域，并使用：

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?range=14-14)]

### <a name="create-a-new-custom-vision-service-project"></a>创建新的自定义视觉服务项目

随后的代码创建对象检测项目。 创建的项目将显示在以前访问过的[自定义视觉网站](https://customvision.ai/)上。 请查看 [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) 方法，以在创建项目时指定其他选项（在[生成检测器](get-started-build-detector.md) Web 门户指南中进行了说明）。  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=29-35)]

### <a name="add-tags-to-the-project"></a>将标记添加到项目中

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=37-39)]

### <a name="upload-and-tag-images"></a>上传和标记图像

在对象检测项目中标记图像时，需要使用标准化坐标指定每个标记对象的区域。 以下代码将每个示例图像与其标记的区域相关联。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=41-84)]

然后，使用此关联映射上传每个示例图像及其区域坐标。 最多可以在单个批次中上传 64 个图像。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=86-104)]

现在，所有示例图像都已上传，每个图像都有一个标记（**叉子**或**剪刀**），并且有一个与该标记对应的关联的像素矩形。

### <a name="train-the-project"></a>定型项目

此代码将在项目中创建第一个训练迭代。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?range=106-117)]

### <a name="publish-the-current-iteration"></a>发布当前迭代

为发布的迭代起的名称可用于发送预测请求。 在发布迭代之前，迭代在预测终结点中不可用。

```csharp
// The iteration is now trained. Publish it to the prediction end point.
var publishedModelName = "treeClassModel";
var predictionResourceId = "<target prediction resource ID>";
trainingApi.PublishIteration(project.Id, iteration.Id, publishedModelName, predictionResourceId);
Console.WriteLine("Done!\n");
```

### <a name="create-a-prediction-endpoint"></a>创建预测终结点

```csharp
// Create a prediction endpoint, passing in the obtained prediction key
CustomVisionPredictionClient endpoint = new CustomVisionPredictionClient()
{
        ApiKey = predictionKey,
        Endpoint = SouthCentralUsEndpoint
};
```

### <a name="use-the-prediction-endpoint"></a>使用预测终结点

此部分的脚本用于加载测试图像、查询模型终结点，以及将预测数据输出到控制台。

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var imageFile = Path.Combine("Images", "test", "test_image.jpg");
using (var stream = File.OpenRead(imageFile))
{
        var result = endpoint.DetectImage(project.Id, publishedModelName, stream);

        // Loop over each prediction and write out the results
        foreach (var c in result.Predictions)
        {
                Console.WriteLine($"\t{c.TagName}: {c.Probability:P1} [ {c.BoundingBox.Left}, {c.BoundingBox.Top}, {c.BoundingBox.Width}, {c.BoundingBox.Height} ]");
        }
}
```

## <a name="run-the-application"></a>运行应用程序

应用程序运行时，会打开一个控制台窗口并写入以下输出：

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

然后，可以验证测试图像（在 **Images/Test/** 中找到）是否已正确标记，并验证检测区域是否正确。 此时，可以按任意键退出应用程序。

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>后续步骤

现在你已了解如何在代码中完成对象检测过程的每一步。 此示例执行单次训练迭代，但通常需要多次训练和测试模型，以使其更准确。 以下指南涉及图像分类，但其原理与对象检测类似。

> [!div class="nextstepaction"]
> [测试和重新训练模型](test-your-model.md)
