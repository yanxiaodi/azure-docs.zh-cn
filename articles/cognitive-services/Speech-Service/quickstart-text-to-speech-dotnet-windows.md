---
title: 快速入门：合成语音，.NET Framework (Windows) - 语音服务
titleSuffix: Azure Cognitive Services
description: 按照本指南使用适用于 Windows 的 .NET framework 和语音 SDK 创建文本转语音控制台应用程序。 完成后，你可以从文本合成语音，并实时听到扬声器上的语音。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 65db847c5df1a05671e3b1c0ac4541f00506c8fd
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854698"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>快速入门：使用适用于 .NET Framework 的语音 SDK (Windows) 合成语音

针对[语音识别](quickstart-csharp-dotnet-windows.md)和[语音翻译](quickstart-translate-speech-dotnetframework-windows.md)也提供了快速入门。

按照本指南使用适用于 Windows 的 .NET framework 和语音 SDK 创建文本转语音控制台应用程序。 完成后，你可以从文本合成语音，并实时听到扬声器上的语音。

为了快速演示（无需自己构建 Visual Studio 项目，如下所示）：

从 GitHub 获取最新[认知服务语音 SDK 示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk)。

## <a name="prerequisites"></a>先决条件

若要完成本项目，需要：

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* 语音服务的订阅密钥。 [免费获得一个](get-started.md)。
* 可用的扬声器（或耳机）。

## <a name="create-a-visual-studio-project"></a>创建 Visual Studio 项目

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>添加示例代码

1. 打开 `Program.cs` 并使用此示例替换自动生成的代码：

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. 找到字符串 `YourSubscriptionKey` 并将其替换为你的语音服务订阅密钥。

1. 找到字符串 `YourServiceRegion` 并将其替换为与订阅关联的[区域](regions.md)。 例如，如果使用的是免费试用版，区域是 `westus`。

1. 保存对项目的更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

1. 从菜单栏中，选择“构建” > “构建解决方案”   。 现在，编译代码时应不会提示错误。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“生成解决方案”选项](media/sdk/qs-csharp-dotnet-windows-08-build.png "成功生成")

1. 在菜单栏中，选择“调试” > “开始调试”，或按 F5 以启动应用程序    。

    ![Visual Studio 应用程序的屏幕截图，其中突出显示了“启动调试”选项](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "启动应用进入调试")

1. 此时将显示控制台窗口，提示你键入一些文本。 键入几个单词或一个句子。 你键入的文本将传输到语音服务，并合成为语音，在你的扬声器上播放。

    ![成功识别后的控制台输出的屏幕截图](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "成功识别后的控制台输出")

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 C# 示例](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>另请参阅

- [自定义语音字体](how-to-customize-voice-font.md)
- [录制语音示例](record-custom-voice-samples.md)
