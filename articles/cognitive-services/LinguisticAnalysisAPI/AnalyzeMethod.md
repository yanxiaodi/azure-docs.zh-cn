---
title: 分析方法 - 语言分析 API
titlesuffix: Azure Cognitive Services
description: 如何使用语言分析 API 中的 Analyze 方法分析某些自然语言输入。
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 02c41e2510fd77f4bb65143faf62737f0985d2b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61401179"
---
# <a name="analyze-method"></a>分析方法

> [!IMPORTANT]
> 语言分析预览版已在 2018 年 8 月 9 日停止使用。 我们建议使用 [Azure 机器学习文本分析模块](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)进行文本处理和分析。

“Analyze”  REST API 用于分析给定的自然语言输入。
这可能涉及仅查找[句子和令牌](Sentences-and-Tokens.md)内的输入，查找[一部分的语音标记](POS-tagging.md)，或查找[角色群树](Constituency-Parsing.md)。
可通过选取相关分析器指定想要的结果。
要列举所有可用分析器，请查看 **[分析器](AnalyzersMethod.md)** 。

请注意，需要指定输入字符串的语言。

REST 终结点： 
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>请求参数

名称 | Type | 需要 | 描述
-----|-------|----------|------------
**语言**    | 字符串 | 是 | 将用于分析的两个字母的 ISO 语言编码。 例如，English 为“en”。
**analyzerIds** | 字符串列表 | 是 | 要应用的分析器的 GUID 列表。 有关详细信息，请参阅分析器文档。
**text**        | 字符串 | 是 | 要分析的原始输入。 这可能是一个短字符串，如单词或短语、完整的句子或完整的段落或语篇。

## <a name="response-json"></a>响应 (JSON)

一组分析输出，其中每个输出对应请求中指定的每个属性。

结果如下：

Name | 类型 | 描述
-----|------|--------------
analyzerId | 字符串 | 指定分析器的 GUID
结果 | 对象 | 分析器结果

请注意，结果的类型取决于输入分析器类型。

### <a name="tokens-response-json"></a>令牌响应 (JSON)

Name | 类型 | 描述
-----|------|-------------
结果 | 句子对象列表 | 文本内确定的句子边界 |
result[x].Offset | int | 每个句子的起始字符偏移量 |
result[x].Len | int | 每个句子的字符长度 |
result[x].Tokens | 令牌对象列表 | 句子内确定的令牌边界 |
result[x].Tokens[y].Offset | int | 令牌的起始字符偏移量 |
result[x].Tokens[y].Len | int | 令牌的字符长度 |
result[x].Tokens[y].RawToken | 字符串 | 规范化前，令牌内的字符 |
result[x].Tokens[y].NormalizedToken | 字符串 | 字符的规范化形式，可在[分析树](Constituency-Parsing.md)中安全使用；例如，左括号字符“(”变为“-LRB-” |

示例输入：`This is a test. Hello.'
示例 JSON 响应：
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>POS 标记响应 (JSON)

结果为字符串列表的列表。
每个句子有一个 POS 标记列表，每个标记有一个 POS 标记。
要查找与每个 POS 标记对应的令牌，还需要请求标记化对象。

### <a name="constituency-tree-response-json"></a>结构树响应 (JSON)

结果为字符串列表，在输入中找到的每个句子都有一个解析树。
解析树采用圆括号形式表示。

## <a name="example"></a>示例

`POST /analyze`

请求正文：JSON 有效负载
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?"
}
```

响应：JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2",
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```
