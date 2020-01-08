---
title: 词性标记 - 语言分析 API
description: 了解语言分析 API 中的词性标记如何标识文本中每个字词的词类或词性。
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 09/27/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 0269397b0f8da66d2bafecfb427ba705fdfff001
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394483"
---
# <a name="part-of-speech-tagging"></a>词性标记

> [!IMPORTANT]
> 语言分析预览版已在 2018 年 8 月 9 日停止使用。 我们建议使用 [Azure 机器学习文本分析模块](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)进行文本处理和分析。

## <a name="background-and-motivation"></a>背景和动机

将文本拆分成句子和词汇后，分析的下一步就是标识每个字词的词类或词性。
词类包括诸如“名词”  （一般表示人、地点、事物、想法等）和“谓词”  （一般表示动作、状态变化等）之类。对于某些字词，词性是明确的（例如，“泥潭”  确实只是名词），但对于其他许多字词，词性就很难判断。
“Table”  可以是坐下的位置（或 2D 数字布局），也可以说“table a discussion”（留到以后再讨论）。

## <a name="list-of-part-of-speech-tags"></a>词性标记列表

| 标记 | 描述 | 示例字词 |
|-----|-------------|---------------|
| $ | 美元 | $ |
| \`\` | 左引号 | \` \`\` |
| '' | 右引号 | ' '' |
| ( | 左括号 | ( [ { |
| ) | 右括号 | ) ] } |
| ， | 逗号 | ， |
| -- | 破折号 | -- |
| . | 句子终止符 | . ! ? |
| : | 冒号或省略号 | : ; ... |
| CC | 并列连词 | and but or yet|
| CD | 基数 | nine 20 1980 '96 |
| DT | 限定词 |a the an all both neither|
| EX | 存在 | there |
| FW | 外来词 | enfant terrible hoi polloi je ne sais quoi |
| IN | 介词或从属连词| in inside if upon whether |
| JJ | 形容词或基数 | ninth pretty execrable multimodal |
| JJR | 形容词的比较级 | better faster cheaper |
| JJS | 形容词的最高级 | best fastest cheapest |
| LS | 列表项标记 | (a) (b) 1 2 A B A. B. |
| MD | 情态助动词 | can may shall will could might should ought |
| NN | 普通名词、单数名词或集合名词 | potato money shoe |
| NNP | 单数专有名词 | Kennedy Roosevelt Chicago Weehauken |
| NNPS | 复数专有名词 | Springfields Bushes |
| NNS | 复数普通名词 | pieces mice fields |
| PDT | 前置限定词 | all both half many quite such sure this |
| POS | 所有格标记 | ' 's |
| PRP | 人称代词 | she he it I we they you |
| PRP $ | 物主代词 | hers his its my our their your |
| RB | 副词 | clinically only |
| RBR | 副词的比较级 | further gloomier grander graver greater grimmer harder harsher healthier heavier higher however larger later leaner lengthier less-perfectly lesser lonelier longer louder lower more... |
| RBS | 副词的最高级 | best biggest bluntest earliest farthest first furthest hardest heartiest highest largest least less most nearest second tightest worst |
| RP | 小品词 | on off up out about |
| SYM | 符号 | % & |
| TO | “to”作为介词或不定式标记 | 至 |
| UH | 感叹词 | uh hooray howdy hello |
| VB | 谓词的基本形式 | give assign fly |
| VBD | 谓词的过去时 | gave assigned flew |
| VBG | 谓词的现在分词或动名词 | giving assigning flying |
| VBN | 谓词的过去分词 | given assigned flown |
| VBP | 谓词的现在时（非第三人称单数） | give assign fly |
| VBZ | 谓词的现在时（第三人称单数） | gives assigns flies |
| WDT | WH-限定词 | that what which |
| WP | WH-代词 | who whom |
| WP$ | WH-物主代词 | whose |
| WRB | WH-副词 | how however whenever where |

## <a name="specification"></a>规格

对于词汇切分，本文以[宾州树库](https://catalog.ldc.upenn.edu/LDC99T42)中的规范为准。
