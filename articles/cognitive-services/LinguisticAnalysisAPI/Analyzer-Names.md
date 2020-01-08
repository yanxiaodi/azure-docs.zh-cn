---
title: 分析器命名结构 - 语言分析 API
titlesuffix: Azure Cognitive Services
description: 了解语言分析 API 分析器命名结构原理有助于提高灵活性和精确度。
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 03/23/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: c989f1115bc5a85bf09270c553ac1cb51bb4f170
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65954704"
---
# <a name="analyzer-names"></a>分析器名称

> [!IMPORTANT]
> 语言分析预览版已在 2018 年 8 月 9 日停止使用。 我们建议使用 [Azure 机器学习文本分析模块](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)进行文本处理和分析。

我们使用有点复杂的分析器命名结构，以实现分析器灵活性和理解名称含义的精确性。
分析器名称由四部分组成：ID、种类、规范和实现。
每个组成部分的作用定义如下。

## <a name="id"></a>ID
首先，分析器有唯一 ID，即 GUID。
这些 GUID 应该极少变化，但是唯一描述特定分析器的独一无二方式。

## <a name="kind"></a>种类
接下来，每个分析器都是一个种类  。
这非常宽泛地定义了返回的分析类型，应唯一定义用于表示此类分析的数据结构。
目前，有三种不同种类：
 - [令牌](Sentences-and-Tokens.md)
 - [POS 标记](Pos-Tagging.md)
 - [成分树](constituency-parsing.md)

## <a name="specification"></a>规格
然而，在特定种类中，各个专家可能会对应如何分析特定现象持有不同看法。
与编程语言不同，对于应如何完成此操作，没有准确清晰的定义。

例如，设想一下，我们已尝试在英语句子中查找令牌"它们未付。"
特别是，想想看字符串“didn't”（没有）。
一种可能解释是，应将它切分成两个词汇：“did”和“not”。
然后替代句子"它们不去了"将具有一组相同的令牌。
另一种可能解释是，应将它切分成词汇“did”和“n't”。
后一个词汇通常不被视为字词，但这种方法保留了表层字符串的更多信息，有时可能会很有用。
或许应将这种缩写形式视为一个字词。

无论选择哪种方法，都应确保一致性。
这正是规范  的作用所在：决定正确的表示形式应该是什么。

分析器输出只能与符合相同规范的数据进行相当比较。

## <a name="implementation"></a>实现

通常有多个模型试图获得相同结果，但它们的性能特征却不同。
一种模型可能会更快，但不太准确；另一种模型可能会做出不同的取舍。

分析器名称的实现  部分用于标识此类信息，方便用户能够选取最能满足自己需求的分析器。
