---
title: 翻译自定义 - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 通过 Microsoft Translator Hub 使用首选的术语和样式构建自己的机器翻译系统。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be9c06d45da4d83c26f82343c9cb7b19ba19b4ae
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257625"
---
# <a name="customize-your-text-translations"></a>自定义文本翻译

Microsoft 自定义翻译是 Microsoft Translator 服务的一项功能，它允许用户在使用文本翻译 API（仅限版本 3）翻译文本时自定义 Microsoft Translator 的高级神经机器翻译。

与[认知服务语音](https://docs.microsoft.com/azure/cognitive-services/speech-service/)一起使用时，该功能还可用于自定义语音翻译。

## <a name="custom-translator"></a>自定义翻译

使用自定义翻译，可以构建神经翻译系统，以了解自己的业务和行业中使用的术语。 然后，自定义的翻译系统将集成到现有的应用程序、工作流和网站中。

### <a name="how-does-it-work"></a>工作原理

使用之前翻译的文档（leaflets、网页、文档等）构建一个翻译系统，该系统可反映与标准翻译系统更好的特定于域的术语和样式。 用户可以上传 TMX、XLIFF、TXT、DOCX 和 XLSX 文档。  

该系统还接受在文档级别并行但在句子级别尚未对齐的数据。 如果用户可以访问相同内容的多种语言版（但每种语言的内容都位于单独的文档中），自定义翻译将能够跨文档自动匹配句子。  该系统还可以使用一种或两种语言的单语数据来补充并行训练数据以改进翻译。

然后，通过使用类别参数对 Microsoft 文本翻译 API 进行常规调用，即可使用自定义的系统。

考虑到训练数据的适当类型和数量，通过使用自定义翻译，期望在翻译质量上获得 5 到 10 分或甚至更多 BLEU 分数的情况并不罕见。

有关基于可用数据的各种自定义级别的更多详细信息，请参阅[自定义翻译用户指南](https://aka.ms/CustomTranslatorDocs)。


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> 旧版 Microsoft Translator 中心将在 2019 5 月17日停用。 [查看重要的迁移信息和日期](https://www.microsoft.com/translator/business/hub/)。  

## <a name="custom-translator-versus-hub"></a>自定义翻译与 Hub

|   | **Hub** | **自定义翻译**|
|:-----|:----:|:----:|
|自定义功能状态   | 正式版  | 正式版 |
| 文本 API 版本  | 仅 V2   | 仅 V3 |
| SMT 自定义 | 是   | 否 |
| NMT 自定义 | 否    | 是 |
| 新的统一语音服务自定义 | 否    | 是 |
| [无跟踪](https://www.aka.ms/notrace) | 是  | 是 |

## <a name="collaborative-translations-framework"></a>协作翻译框架

> [!NOTE]
> 自 2018 年 2 月 1 日起，AddTranslation() 和 AddTranslationArray() 不再可用于文本翻译 API V2.0。 这些方法将失败，不会写入任何内容。 文本翻译 API V3.0 不支持这些方法。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用自定义翻译设置自定义语言系统](https://aka.ms/CustomTranslatorDocs)
