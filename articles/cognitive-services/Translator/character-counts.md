---
title: 字符计数 - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 文本翻译 API 如何计算字符数。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e3a16d9272e75f9a94f5381c1681c036d177e0f6
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595991"
---
# <a name="how-the-translator-text-api-counts-characters"></a>文本翻译 API 如何计算字符数

文本翻译 API 将输入文本的每个 Unicode 码位计为一个字符。 文本到某种语言的每个翻译都计为单独的翻译，即使在单个 API 调用中发出翻译为多种语言的请求时也是如此。 响应的长度无关紧要。

计数对象为：

* 在请求正文中传递到文本翻译 API 的文本
   * `Text`（如果使用 Translate、Transliterate 和 Dictionary Lookup 方法）
   * `Text` 和 `Translation`（如果使用 Dictionary Examples 方法）
* 所有标记：请求正文文本字段内的 HTML、XML 标记等。 用于生成请求的 JSON 表示法（例如，“Text:”）不计入。
* 单个字母
* 标点
* 空格、制表符、标记和任何类型的空格字符
* Unicode 中定义的每个码位
* 重复的翻译（即使之前已翻译相同的文本）

对于基于表意文字（例如中文汉字和日文汉字）的脚本，文本翻译 API 仍会对 Unicode 码位的数量计数，每个表意文字计为一个字符。 例外:Unicode 代理项计为两个字符。

请求、单词、字节或句子的数量在字符计数中不相关。

对 Detect 和 BreakSentence 方法的调用不计入字符消耗。 但是，我们希望 Detect 和 BreakSentence 方法的调用次数与其他计数函数的使用次数成合理的比例。 如果发出的 Detect 或 BreakSentence 调用的数量是其他计数方法数量的 100 倍，Microsoft 保留限制使用 Detect 和 BreakSentence 方法的权利。


有关字符计数的详细信息，请参阅 [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx)。
