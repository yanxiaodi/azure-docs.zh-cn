---
title: 句子配对和对齐 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 在执行训练过程中，会将并行文档中的句子配对或对齐。 自定义翻译每次学习一个句子的翻译，并通过读取另一个句子来获取此句子的翻译。 然后，它将这两个句子中的单词和短语相互对齐。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e9bc5c876da6bd2be1b22b389b819e51330b2e50
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595455"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>并行文档中的句子配对和对齐

在训练过程中，会将并行文档中的句子配对或对齐。 自定义翻译会报告它能够配对为每个数据集中“已对齐句子”的句子数。

## <a name="pairing-and-alignment-process"></a>配对和对齐过程

自定义翻译每次学习一个句子的翻译。 它从源中读取某个句子，然后从目标中读取此句子的翻译。 然后，它将这两个句子中的单词和短语相互对齐。 此过程可让自定义翻译将一个句子中的单词和短语映射到此句子的翻译中的对应单词和短语。 对齐的目的是尽量确保基于相互翻译的句子训练系统。

## <a name="pre-aligned-documents"></a>预先对齐的文档

如果你知道自己可以提供并行文档，则可以通过提供预先对齐的文本文件来替代句子对齐。 可将两个文档中的所有句子提取到该文本文件，按每行一个句子的方式进行组织，然后使用 `.align` 扩展上传。 `.align` 扩展告知自定义翻译应该跳过句子对齐。

为获得最佳结果，请尽量确保在文件中每行放置一个句子。 不要在句子中插入换行符，否则会导致对齐结果不佳。

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>建议的最小提取句子和对齐句子数目

下表显示了每个数据集中所需的最小提取句子和对齐句子数目，以帮助你成功完成训练。 建议的最小提取句子数目之所以远远多过建议的对齐句子数目，是因为考虑到了句子对齐不一定能够成功对齐所有提取的句子这一事实。

| 数据集   | 建议的最小提取句子计数 | 建议的最小对齐句子计数 | 最大对齐句子计数 |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| 培训   | 10,000                                     | 2,000                                    | 没有上限                 |
| 优化     | 2,000                                      | 500                                      | 2,500                          |
| 正在测试    | 2,000                                      | 500                                      | 2,500                          |
| 字典 | 0                                          | 0                                        | 没有上限                 |

## <a name="next-steps"></a>后续步骤

- 了解如何在自定义翻译中使用[字典](what-is-dictionary.md)。
