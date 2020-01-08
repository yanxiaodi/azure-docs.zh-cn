---
title: 什么是文本分析 API？ - 功能 -
titleSuffix: Azure Cognitive Services
description: 使用 Azure 认知服务中用于情绪分析、关键短语提取、语言检测和实体识别的文本分析 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 08/26/2019
ms.author: aahi
ms.openlocfilehash: 8c5df8461c74d48c0712ab1947e29813e7e1ea3f
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032676"
---
# <a name="what-is-the-text-analytics-api"></a>什么是文本分析 API？

文本分析 API 是一种基于云的服务，它对原始文本提供高级自然语言处理，并且包含四项主要功能：情绪分析、关键短语提取、语言检测和实体识别。

该 API 是 [Azure 认知服务](https://docs.microsoft.com/azure/cognitive-services/)的一部分，是云中机器学习和 AI 算法的集合，适用于开发项目。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

文本分析可能有不同的含义，但在认知服务中，文本分析 API 提供如下所述的四种分析。 可以将这些功能与 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/) 或适用于 [.NET](quickstarts/csharp.md)、[Python](quickstarts/python-sdk.md)、[Node.js](quickstarts/nodejs-sdk.md)、[Go](quickstarts/go-sdk.md) 或 [Ruby](quickstarts/ruby-sdk.md) 的客户端库配合使用。

## <a name="sentiment-analysis"></a>情绪分析
通过在原始文本中分析有关积极和消极情绪的线索，使用[情绪分析](how-tos/text-analytics-how-to-sentiment-analysis.md)确定客户如何看待你的品牌或主题。 此 API 针对每个文档返回介于 0 和 1 之间的情绪评分，1 是最积极的评分。<br /> 分析模型已使用 Microsoft 提供的大量文本正文和自然语言技术进行预先训练。 对于[选定的语言](text-analytics-supported-languages.md)，该 API 可以分析和评分提供的任何原始文本，并直接将结果返回给调用方应用程序。

## <a name="key-phrase-extraction"></a>关键短语提取
自动[提取关键短语](how-tos/text-analytics-how-to-keyword-extraction.md)，以快速识别要点。 例如，针对输入文本“The food was delicious and there were wonderful staff”，该 API 会返回谈话要点：“food”和“wonderful staff”。

## <a name="language-detection"></a>语言检测
可以[检测输入文本是用哪种语言编写的](how-tos/text-analytics-how-to-language-detection.md)，并以多种语言、变体、方言和一些区域/文化语言报告请求中提交的每个文档的单一语言代码。 语言代码与表示评分强度的评分相搭配。

## <a name="named-entity-recognition"></a>命名实体识别
[识别文本中的实体并将其分类](how-tos/text-analytics-how-to-entity-linking.md)为人员、地点、组织、日期/时间、数量、百分比、货币等。 已知实体也可以在 Web 上识别并链接到更多信息。

## <a name="use-containers"></a>使用容器

将标准化的 Docker 容器安装到靠近数据的位置以后，即可在本地[使用文本分析容器](how-tos/text-analytics-how-to-install-containers.md)提取关键短语、检测语言以及进行情绪分析。

## <a name="typical-workflow"></a>典型工作流

工作流非常简单：在代码中提交分析数据和处理输出。 分析器按原样使用，无需额外的配置或自定义。

1. 为文本分析[创建 Azure 资源](../cognitive-services-apis-create-account.md)。 然后，[获取生成的密钥](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)，以便对请求进行身份验证。

2. [规划请求](how-tos/text-analytics-how-to-call-api.md#json-schema)，其中包含原始非结构化文本形式的 JSON 数据。

3. 将此请求发布到注册期间建立的终结点，并追加所需的资源：情绪分析、关键短语提取、语言检测或实体识别。

4. 在本地流式处理或存储响应。 根据具体的请求，结果将是情绪评分、提取的关键短语集合或语言代码。

输出将会根据 ID 以单个 JSON 文档的形式返回，其中包含发布的每个文本文档的结果。 然后，可以分析、可视化结果，或将其分类成可行的见解。

数据不会存储在你的帐户中。 文本分析 API 执行的操作是无状态的，这意味着，将会处理所提供的文本，并立即返回结果。

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>适合多种编程经验水平的文本分析

即使编程经验并不丰富，也可以开始在进程中使用文本分析 API。 学习这些教程，了解如何根据自己的经验水平使用该 API 以不同方式分析文本。 

* 最低的编程要求：
    * [使用文本分析 API 和 MS Flow 识别 Yammer 组中的评论的情绪](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [集成 Power BI 和文本分析 API 以分析自定义反馈](tutorials/tutorial-power-bi-key-phrases.md)
* 建议的编程体验：
    * [使用 Azure Databricks 针对流数据执行情绪分析](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [生成 Flask 应用以翻译文本、分析情绪以及合成语音](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>支持的语言

为方便查找，本部分已转移到单独的文章。 有关此内容，请参阅[文本分析 API 支持的语言](text-analytics-supported-languages.md)。

<a name="data-limits"></a>

## <a name="data-limits"></a>数据限制

所有的文本分析 API 终结点都接受原始文本数据。 当前限制为每个文档最多包含 5,120 个字符；如果需要分析更大的文档，可将它们分解成较小的区块。

| 限制 | 值 |
|------------------------|---------------|
| 单个文档的最大大小 | 5,120 个字符，由 [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) 度量。 |
| 整个请求的最大大小 | 1 MB |
| 一个请求中的文档数上限 | 1,000 个文档 |

速率限制将因定价层而异。

| 层          | 每秒请求数 | 每分钟请求数 |
|---------------|---------------------|---------------------|
| S/多服务 | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

对每个文本分析功能的请求分别进行测量。 例如，可以同时向每个功能发送定价层的最大数量的请求。      

## <a name="unicode-encoding"></a>Unicode 编码

文本分析 API 使用 Unicode 编码来呈现文本和计算字符数。 可以 UTF-8 和 UTF-16 编码提交请求，这在字符计数方面没有可度量的差别。 Unicode 码位用作字符长度的启发因子，对文本分析数据限制的影响被视为等效。 如果你使用 [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) 获取字符计数，则使用的方法也是我们用来度量数据大小的方法。

## <a name="next-steps"></a>后续步骤

+ 为文本分析[创建 Azure 资源](../cognitive-services-apis-create-account.md)，以获取应用程序的密钥和终结点。

+ [快速入门](quickstarts/csharp.md)演练了以 C# 编写的 REST API 调用。 了解如何以少量的代码提交文本、选择分析，并查看结果。 如果你愿意，可以改为从 [Python 快速入门](quickstarts/python.md)着手。

+ 有关新版本和功能的信息，请参阅[文本分析 API 中的新增功能](whats-new.md)。

+ 更深入地探讨此[情绪分析教程](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services)（使用 Azure Databricks）。

+ 在[“外部和社区内容”页](text-analytics-resource-external-community.md)中查看一系列博客文章和更多的视频，了解如何结合其他工具和技术使用文本分析 API。
