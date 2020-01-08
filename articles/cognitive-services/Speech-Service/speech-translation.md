---
title: 语音翻译和语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务使你能够将语音的端到端、实时、多语言翻译添加到你的应用程序、工具和设备。 相同 API 可以用于语音到语音和语音到文本的转换。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: cfcefd0b18831163324519b61dbea305f90f44bc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552645"
---
# <a name="what-is-speech-translation"></a>什么是语音翻译？

通过 Azure 语音服务的语音翻译, 可实现音频流的实时多语言语音到语音和语音到文本转换。 使用 Speech SDK, 你的应用程序、工具和设备可以访问提供的音频的源转录和翻译输出。 检测到语音时, 会返回过渡脚本和翻译结果, 并且总决赛结果可以转换为合成语音。

Microsoft 的翻译引擎由两种不同的方法提供支持: 统计机转换 (SMT) 和神经机器翻译 (NMT)。 SMT 使用高级统计分析来估算几个词的上下文中可能的最佳翻译。 使用 NMT, 神经网络可通过使用句子的完整上下文翻译字词来提供更准确、自然的翻译。

如今, Microsoft 使用 NMT 转换为最常用的语言。 NMT 支持所有[可用于语音到语音转换的语言](language-support.md#speech-translation)。 语音到文本转换可能会使用 SMT 或 NMT，具体取决于语言对。 如果 NMT 支持目标语言, 则完全翻译为 NMT。 当 NMT 不支持目标语言时, 转换将使用英语作为这两种语言之间的 "透视" 来混合 NMT 和 SMT。

## <a name="core-features"></a>核心功能

以下是通过语音 SDK 和 REST Api 提供的功能:

| 使用案例 | SDK | REST |
|----------|-----|------|
| 识别结果的语音到文本转换。 | 是 | 否 |
| 语音到语音转换。 | 是 | 否 |
| 过渡识别和转换结果。 | 是 | 否 |

## <a name="get-started-with-speech-translation"></a>语音翻译入门

我们提供了快速入门, 旨在让你在不到10分钟的时间内运行代码。 此表包含按语言组织的语音翻译快速入门列表。

| 快速入门 | 平台 | API 参考 |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Browse](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows、Linux、macOS | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>示例代码

GitHub 上提供了语音 SDK 的示例代码。 这些示例涵盖了常见方案, 如从文件或流中读取音频、连续和单步识别/翻译以及使用自定义模型。

* [语音到文本和翻译示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>迁移指南

如果你的应用程序、工具或产品正在使用[语音翻译 API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview), 我们已创建了可帮助你迁移到语音服务的指南。

* [从语音翻译 API 迁移到语音服务](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>参考文档

* [语音 SDK](speech-sdk-reference.md)
* [语音设备 SDK](speech-devices-sdk.md)
* [REST API：语音转文本](rest-speech-to-text.md)
* [REST API：文本转语音](rest-text-to-speech.md)
* [REST API：批量听录和自定义](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>后续步骤

* [免费获取语音服务订阅密钥](get-started.md)
* [获取语音 SDK](speech-sdk.md)
