---
title: 使用文本审查 API 来审查文本 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 通过使用联机控制台中的文本审查 API 体验文本审查。
services: cognitive-services
author: sanjeev3
ms.author: sajagtap
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 49047c21f86530aac6ed3e0138a643fd08c35af8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561176"
---
# <a name="moderate-text-from-the-api-console"></a>从 API 控制台审查文本

在 Azure 内容审查器中使用[文本审查 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) 扫描文本内容以查找亵渎语言，并将其与自定义和共享列表进行比较。

## <a name="get-your-api-key"></a>获取 API 密钥

可以在联机控制台中体验 API 之前，需要提供订阅密钥。 订阅密钥位于“设置”选项卡上的“Ocp-Apim-Subscription-Key”框中。 有关详细信息，请参阅[概述](overview.md)。

## <a name="navigate-to-the-api-reference"></a>导航到 API 参考

转到[文本审查 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)。 

  “文本 - 屏幕”页随即打开。

## <a name="open-the-api-console"></a>打开 API 控制台

若要打开 API 测试控制台，选择最贴切地描述你所在位置的区域。 

  ![“文本 - 屏幕”页区域选择](images/test-drive-region.png)

  文本 - 屏幕 API 控制台随即打开。

## <a name="select-the-inputs"></a>选择输入

### <a name="parameters"></a>Parameters

在文本屏幕中选择要使用的查询参数。 在本例中，使用语言的默认值。 也可以将其留空，因为作为其执行的一部分，该操作将自动检测可能的语言。

> [!NOTE]
> 有关 language 参数，分配 `eng` 或留空以查看计算机辅助的分类响应（预览功能）。 **此功能仅支持英语**。
>
> 对于不雅用语检测，请使用本文所列的支持语言的 [ISO 639-3 代码](http://www-01.sil.org/iso639-3/codes.asp)或留空。

对于“自动更正”、“PII”和“分类（预览）”，请选择“true”。 将“ListId”字段保留空白。

  ![文本 - 屏幕控制台查询参数](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>内容类型

有关 **Content-Type**，选择你想要显示的内容类型。 在本例中，使用默认的 text/plain 内容类型。 在“Ocp-Apim-Subscription-Key”框中，输入订阅密钥。

### <a name="sample-text-to-scan"></a>要扫描的示例文本

在“请求正文”框中，输入一些文本。 以下示例显示文本中的有意拼写错误。

> [!NOTE]
> 以下示例文本中的无效社会安全号码是有意而为。 目的是传达示例输入和输出格式。

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>分析响应

下面的响应显示来自 API 的各种见解。 它包含潜在不雅内容、个人数据、分类（预览）和自动更正版本。

> [!NOTE]
> 计算机辅助的“分类”功能处于预览状态，仅支持英语。

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1 Microsoft Way, Redmond, WA 98052",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

有关 JSON 响应中各部分的详细说明，请参阅[文本审查](text-moderation-api.md)概念指南。

## <a name="next-steps"></a>后续步骤

在代码中使用 REST API，或者从[文本审查 .NET 快速入门](text-moderation-quickstart-dotnet.md)开始，与你的应用程序集成。
