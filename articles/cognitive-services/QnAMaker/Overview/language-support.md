---
title: 语言支持 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 知识库的语言影响 QnA Maker 从源自动提取问题和解答的能力，以及 QnA Maker 响应用户查询时提供的结果的相关性。 区域性列表（QnA Maker 支持的知识库的自然语言）。 请勿将多种语言混合在同一个知识库中。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec09e5cd053774902083c6dd042c1ed4b293d20
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066666"
---
# <a name="language-support-for-qna-maker"></a>QnA Maker 的语言支持

知识库的语言影响 QnA Maker 从[源](../Concepts/data-sources-supported.md)自动提取问题和解答的能力，以及 QnA Maker 响应用户查询时提供的结果的相关性。

## <a name="auto-extraction"></a>自动提取
QnA Maker 支持提取任何语言页面中的问题/解答，但对于以下语言，提取有效性更高，因为 QnA Maker 使用关键字来标识问题。

|支持的语言| 区域设置|
|-----|----|
|英语|en-*|
|法语|fr-*|
|意大利语|it-*|
|德语|de-*|
|西班牙语|es-*|

## <a name="primary-language-detection"></a>主语言检测

将第一个文档或 URL 添加到第一个知识库时，将为 QnA Maker 资源以及在该资源上创建的所有知识库设置用于检测的主要语言。 不能更改此语言。 

如果用户计划支持多种语言，则他们需要为每种语言提供新的 QnA Maker 资源。 了解如何[创建基于语言的 QnA Maker 知识库](../how-to/language-knowledge-base.md)。  

通过以下步骤验证主要语言：

1. 登录到 [Azure 门户](https://portal.azure.com)。  
1. 查找并选择作为 QnA Maker 资源的一部分创建的 Azure 搜索资源。 Azure 搜索资源名称将以与 QnA Maker 资源相同的名称开始，并将具有类型**搜索服务**。 请记住，只有一个 QnA Maker 资源可链接到一个 Azure 搜索资源。
1. 从搜索资源的 "**概述**" 页中，选择 "**索引**"。 
1. 选择 testkb 索引。
1. 选择 "**字段**" 选项卡。 
1. 查看**问题**和**答案**字段的**分析器**列。 


## <a name="query-matching-and-relevance"></a>查询匹配和相关性
QnA Maker 依赖于 Azure 搜索中的[语言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)来提供结果。 针对 En-* 语言提供了特殊的重新排名功能以实现更好的相关性。

虽然 Azure 搜索功能对于支持的语言而言都相同，但 QnA Maker 还有基于 Azure 搜索结果的额外排名器。 在此 ranker 模型中，我们使用了一些特殊的语义和 en 中基于 word 的功能，这些功能尚不适用于其他语言。 我们并不提供这些功能，因为它们是 QnA Maker ranker 的内部工作的一部分。 

QnA Maker 在创建过程中[自动检测知识库的语言](#primary-language-detection)，并相应地设置分析器。 可以采用以下语言创建知识库。 

|支持的语言|
|-----|
|阿拉伯语|
|亚美尼亚语|
孟加拉语|
|巴斯克语|
|保加利亚语|
|加泰罗尼亚语|
|中文_简体|
|中文_繁体|
|克罗地亚语|
|捷克语|
|丹麦语|
|荷兰语|
|英语|
|爱沙尼亚语|
|芬兰语|
|法语|
|加利西亚语|
|德语|
|希腊语|
|古吉拉特语|
|希伯来语|
|印地语|
|匈牙利语|
|冰岛语|
|印度尼西亚语|
|爱尔兰语|
|意大利语|
|日语|
|卡纳达语|
|韩语|
|拉脱维亚语|
|立陶宛语|
|马拉雅拉姆语|
|马来语|
|挪威语|
|波兰语|
|葡萄牙语|
|旁遮普语|
|罗马尼亚语|
|俄语|
|塞尔维亚语_西里尔文|
|塞尔维亚语_拉丁语|
|斯洛伐克语|
|斯洛文尼亚语|
|西班牙语|
|瑞典语|
|泰米尔语|
|泰卢固语|
|泰语|
|土耳其语|
|乌克兰语|
|乌尔都语|
|越南语|
