---
title: 快速入门：适用于 .NET 的语言理解 (LUIS) 创作客户端库
titleSuffix: Azure Cognitive Services
description: 适用于 .NET 的语言理解 (LUIS) 客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。  使用语言理解 (LUIS) 可在用户的自然语言对话文本中应用自定义的机器学习智能，以预测整体含义并提炼出相关的详细信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: d7668f49df1caca6b4261424b0d2c025a640d572
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258786"
---
# <a name="quickstart-language-understanding-luis-authoring-client-library-for-net"></a>快速入门：适用于 .NET 的语言理解 (LUIS) 创作客户端库

适用于 .NET 的语言理解 (LUIS) 创作客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。  使用语言理解 (LUIS) 可在用户的自然语言对话文本中应用自定义的机器学习智能，以预测整体含义并提炼出相关的详细信息。 

使用适用于 .NET 的语言理解 (LUIS) 创作客户端库可以：

* 创建应用
* 添加意向、实体和示例言语
* 添加短语列表等特征
* 训练和发布应用

[参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) | [创作包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) | [C# 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs)

## <a name="prerequisites"></a>先决条件

* 语言理解 (LUIS) 门户帐户 - [免费创建](https://www.luis.ai)
* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 的当前版本。

## <a name="setting-up"></a>设置

### <a name="get-your-language-understanding-luis-starter-key"></a>获取语言理解 (LUIS) 初学者密钥

获取[初学者密钥](luis-how-to-azure-subscription.md#starter-key)，并为该密钥[创建 环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)（名为 `COGNITIVESERVICE_AUTHORING_KEY`）。

### <a name="create-a-new-c-application"></a>新建 C# 应用程序

在首选编辑器或 IDE 中创建新的 .NET Core 应用程序。 

1. 在控制台窗口（例如 CMD、PowerShell 或 Bash）中，使用 dotnet `new` 命令创建名为 `language-understanding-quickstart` 的新控制台应用。 此命令将创建包含单个源文件的简单“Hello World”C# 项目：`Program.cs`。 

    ```console
    dotnet new console -n language-understanding-quickstart
    ```

1. 将目录更改为新创建的应用文件夹。 

1. 可使用以下代码生成应用程序：

    ```console
    dotnet build
    ```

    生成输出不应包含警告或错误。 
    
    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```


### <a name="install-the-sdk"></a>安装 SDK

在应用程序目录中，使用以下命令安装适用于 .NET 的语言理解 (LUIS) 创作客户端库：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.0.0
```

如果你使用的是 Visual Studio IDE，客户端库可用作可下载的 NuGet 包。


## <a name="object-model"></a>对象模型

语言理解 (LUIS) 创作客户端是对 Azure 进行身份验证的 [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) 对象，其中包含创作密钥。

创建客户端后，可以使用此客户端访问如下所述的功能：

* 应用 - [创建](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet)、[删除](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet)、[发布](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* 示例言语 - [按批添加](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet)、[按 ID 删除](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet) 
* 特征 - 管理[短语列表](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet) 
* 模型 - 管理[意向](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet)和实体
* 模式 - 管理[模式](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* 训练 - [训练](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet)应用和轮询[训练状态](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [版本](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - 使用克隆、导出和删除操作进行管理


## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 .NET 的语言理解 (LUIS) 创作客户端库来执行以下操作：

* [创建应用](#create-a-luis-app)
* [添加实体](#create-entities-for-the-app)
* [添加意向](#create-intent-for-the-app)
* [添加示例表述](#add-example-utterance-to-intent)
* [训练应用](#train-the-app)
* [发布应用](#publish-a-language-understanding-app)

## <a name="add-the-dependencies"></a>添加依赖项

在首选的编辑器或 IDE 中，从项目目录打开 **Program.cs** 文件。 将现有 `using` 代码替换为以下 `using` 指令：

[!code-csharp[Using statements](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Dependencies)]

## <a name="authenticate-the-client"></a>验证客户端

1. 创建一个变量，用于管理从环境变量提取的名为 `COGNITIVESERVICES_AUTHORING_KEY` 的创作密钥。 如果在启动应用程序后创建了环境变量，则需要关闭并重新加载运行它的编辑器、IDE 或 shell 以访问该变量。 稍后会创建这些方法。

1. 创建用于保存创作区域和终结点的变量。 创作密钥的区域取决于创作位置。 [三个创作区域](luis-reference-regions.md)如下：

    * 澳大利亚 - `australiaeast`
    * 欧洲 - `westeurope`
    * 美国和其他区域 - `westus`（默认）
    
    [!code-csharp[Authorization to resource key](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=Variables)]

1. 使用密钥创建 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) 对象，并在终结点中使用该对象创建一个 [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) 对象。

    [!code-csharp[Create LUIS client object](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>创建 LUIS 应用

1. 创建一个 LUIS 应用，用于包含保存意向、实体和示例言语的自然语言处理 (NLP) 模型。 

1. 创建 [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet)。 名称和语言区域性是必需的属性。 

1. 调用 [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) 方法。 响应为应用 ID。 
    
    [!code-csharp[Create a LUIS app](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>为应用创建意向
LUIS 应用模型中的主要对象是意向。 意向与用户言语意向的分组相符。  用户可以提问，或者做出表述，指出希望机器人（或其他客户端应用程序）提供特定的有针对性响应。  意向的示例包括预订航班、询问目的地城市的天气，以及询问客户服务的联系信息。   

使用唯一意向的名称创建 [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet)，然后将应用 ID、版本 ID 和 ModelCreateObject 传递给 [Model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) 方法。 响应为意向 ID。

[!code-csharp[Create intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddIntents)]

## <a name="create-entities-for-the-app"></a>为应用创建实体

尽管实体不是必需的，但在大多数应用中都可以看到实体。 实体从用户言语中提取信息，只有使用这些信息才能实现用户的意向。 有多种类型的[预生成](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet)实体和自定义实体，每种实体具有自身的数据转换对象 (DTO) 模型。  在应用中添加的常见预生成实体包括 [number](luis-reference-prebuilt-number.md)、[datetimeV2](luis-reference-prebuilt-datetimev2.md)、[geographyV2](luis-reference-prebuilt-geographyv2.md) 和 [ordinal](luis-reference-prebuilt-ordinal.md)。 

此 **AddEntities** 方法创建一个包含两个角色的 `Location` 简单实体、一个 `Class` 简单实体和一个 `Flight` 复合实体，并添加多个预生成实体。

必须知道，实体不会使用意向进行标记。 它们可以并且通常应用到多个意向。 只会为特定的单个意向标记示例用户言语。

实体的创建方法属于 [Model](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet) 类的一部分。 每个实体类型有自身的数据转换对象 (DTO) 模型，该模型通常在 [Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet) 命名空间中包含单词 `model`。 

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringAddEntities)]

## <a name="add-example-utterance-to-intent"></a>将示例言语添加到意向

为了确定言语的意向并提取实体，应用需要言语示例。 这些示例需要针对特定的单个意向，并且应该标记所有自定义实体。 无需标记预生成实体。 

通过创建 [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) 对象的列表来添加示例言语（每个示例言语对应于一个对象）。 每个示例应使用实体名称和实体值的名称/值对字典来标记所有实体。 实体值应与示例言语文本中显示的值完全相同。 

结合应用 ID、版本 ID 和示例列表调用 [Examples.BatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.batchasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ExamplesExtensions_BatchAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_IExamples_System_Guid_System_String_System_Collections_Generic_IList_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_Models_ExampleLabelObject__System_Threading_CancellationToken_)。 该调用将以结果列表做出响应。 需要检查每个示例的结果，以确保该示例已成功添加到模型中。 

[!code-csharp[Add example utterances to a specific intent](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringBatchAddUtterancesForIntent)]
    
**CreateUtterance** 和 **CreateLabel** 方法是帮助你创建对象的实用工具方法。

## <a name="train-the-app"></a>训练应用

创建模型后，需要为此模型版本训练 LUIS 应用。 训练后的模型可在[容器](luis-container-howto.md)中使用，或者将其[发布](luis-how-to-publish-app.md)到过渡槽或生产槽。 

[Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) 方法需要应用 ID 和版本 ID。 

极小的模型（如本快速入门中所示的模型）很快就能完成训练。 对于生产级应用程序，应用的训练应该包括轮询调用 [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) 方法以确定训练何时或者是否成功。 响应是一个 [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) 对象列表，其中分别列出了每个对象的状态。 所有对象必须成功，才能将训练视为完成。

[!code-csharp[Train the app's version](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringTrainVersion)]

## <a name="publish-a-language-understanding-app"></a>发布语言理解应用

使用 [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) 方法发布 LUIS 应用。 这会将当前已训练的版本发布到终结点上的指定槽。 客户端应用程序使用此终结点发送用户言语，以预测意向和提取实体。

[!code-csharp[Create entities](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/LUIS/LUIS.cs?name=AuthoringPublishVersionAndSlot)]

## <a name="run-the-application"></a>运行应用程序

从应用程序目录，使用 dotnet `run` 命令运行应用程序。

```console
dotnet run
```

## <a name="clean-up-resources"></a>清理资源

如果需要清理，可以删除 LUIS 应用。 可以使用 [DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet) 方法删除应用。 也可以从 [LUIS 门户](https://www.luis.ai)删除应用。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[使用 .Net SDK 查询预测终结点](sdk-csharp-quickstart-query-prediction-endpoint.md)

* [什么是语言理解 (LUIS) API？](what-is-luis.md)
* [新增功能](whats-new.md)
* [意向](luis-concept-intent.md)、[实体](luis-concept-entity-types.md)、[示例言语](luis-concept-utterance.md)和[预生成实体](luis-reference-prebuilt-entities.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/LUIS/LUIS.cs) 上找到此示例的源代码。
