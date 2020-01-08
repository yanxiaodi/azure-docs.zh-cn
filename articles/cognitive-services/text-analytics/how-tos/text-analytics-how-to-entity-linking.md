---
title: 结合使用实体识别和文本分析 API
titleSuffix: Azure Cognitive Services
description: 了解如何使用文本分析 REST API 识别实体。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 642b21624ce3ffc993d5f29a413845044d703fd7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984268"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>如何在文本分析中使用命名实体识别

[命名实体识别 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) 需要使用非结构化文本，并对每个 JSON 文档返回已消除歧义的实体列表，其中包含网上（维基百科和必应）详细信息的链接。

## <a name="entity-linking-and-named-entity-recognition"></a>实体链接和命名实体识别

文本分析的 `entities` 终结点支持命名实体识别 (NER) 和实体链接。

### <a name="entity-linking"></a>实体链接
实体链接是一种对文本中找到的实体的身份进行识别和消歧的功能（例如，确定“Mars”是用作行星还是用作罗马战神）。 此过程需要用到已识别实体链接到的知识库 — Wikipedia 用作 `entities` 终结点文本分析的知识库。

### <a name="named-entity-recognition-ner"></a>命名实体识别 (NER)
命名实体识别 (NER) 是指识别文本中不同实体，并将它们分入预定义类别的能力。 下面列出了受支持的实体类型。

在文本分析[版本 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) 中，实体链接和命名实体识别 (NER) 均可用于几种语言。 有关详细信息，请参阅[语言支持](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition)一文。

### <a name="language-support"></a>语言支持

使用各种语言的实体链接需要使用每种语言的相应知识库。 对于文本分析中的实体链接，这意味着 `entities` 终结点支持的每种语言都将链接到该语言的相应 Wikipedia 语料库。 由于语料库的大小因语言而异，因此，实体链接功能的召回率应该也有所不同。

## <a name="supported-types-for-named-entity-recognition"></a>命名实体识别支持的类型

| 类型  | SubType | 示例 |
|:-----------   |:------------- |:---------|
| 个人        | 暂无\*         | “Jeff”、“Bill Gates”     |
| Location      | 暂无\*         | “Redmond, Washington”、“Paris”  |
| 组织  | 暂无\*         | “Microsoft”   |
| 数量      | 数量        | “6”、“six”     |
| 数量      | 百分比    | “50%”、“fifty percent”|
| 数量      | 序号       | “2nd”、“second”     |
| 数量      | 期限           | “90 day old”、“30 years old”    |
| 数量      | 货币      | “$10.99”     |
| 数量      | 维度     | “10 miles”、“40 cm”     |
| 数量      | 温度   | “32 degrees”    |
| DateTime      | 暂无\*         | “6:30PM February 4, 2012”      |
| DateTime      | Date          | “May 2nd, 2017”、“05/02/2017”   |
| DateTime      | Time          | “8am”、“8:00”  |
| DateTime      | 日期范围     | “May 2nd to May 5th”    |
| DateTime      | 时间范围     | “6pm to 7pm”     |
| DateTime      | Duration      | “1 minute and 45 seconds”   |
| DateTime      | 设置           | “every Tuesday”     |
| URL           | 暂无\*         | "https:\//www.bing.com"    |
| Email         | 暂无\*         | "support@contoso.com" |

\*一些实体可能会省略 `SubType`，具体视输入和已提取的实体而定。  列出的所有受支持的实体类型仅适用于英文、简体中文、法文、德文和西班牙文。



## <a name="preparation"></a>准备工作

必须拥有以下格式的 JSON 文档：ID、文本、语言

有关当前支持的语言，请参阅[此列表](../text-analytics-supported-languages.md)。

每个文档的大小必须少于 5,120 个字符，每个集合最多可包含 1,000 个项目 (ID)。 集合在请求正文中提交。 以下示例例举了可能提交至实体链接端的内容。

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>步骤 1：构造请求

有关请求定义的详细信息，请参阅[如何调用文本分析 API](text-analytics-how-to-call-api.md)。 为方便起见，特重申以下几点：

+ 创建 POST 请求。 查看此请求的 API 文档：[实体 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ 使用 Azure 上的文本分析资源或实例化的[文本分析容器](text-analytics-how-to-install-containers.md)设置 HTTP 终结点，以便提取关键短语。 必须包括`/text/analytics/v2.1/entities`。 例如：`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`。

+ 设置请求标头以包括文本分析操作[的访问密钥](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在请求正文中，提供为此分析准备的 JSON 文档集合

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或打开[文档](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)中的“API 测试控制台”来构造请求并将其 POST 到该服务。

## <a name="step-2-post-the-request"></a>步骤 2：发布请求

在收到请求时执行分析。 有关每分钟和每秒可以发送的请求的大小和数量的信息，请参阅概述中的[数据限制](../overview.md#data-limits)部分。

记住，该服务是无状态服务。 帐户中未存储任何数据。 结果会立即在响应中返回。

## <a name="step-3-view-results"></a>步骤 3：查看结果

所有 POST 请求都将返回 JSON 格式的响应，其中包含 ID 和检测到的属性。

系统会立即返回输出。 你可以将结果流式传输到接受 JSON 的应用程序，或者将输出保存到本地系统上的文件中，然后将其导入到允许对数据进行排序、搜索和操作的应用程序。

下面展示了实体链接的输出示例：

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>总结

在本文中，你已了解使用认知服务中的文本分析进行实体链接的概念和工作流。 综上所述：

+ [实体 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) 适用于选定语言。
+ 请求正文中的 JSON 文档包括 ID、文本和语言代码。
+ POST 请求的目标是 `/entities` 终结点，方法是使用对订阅有效的个性化[访问密钥和终结点](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
+ 响应输出由链接实体（包括每个文档 ID 的置信度分数、偏移量和 Web 链接）组成，可用于任何应用程序

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [文本分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [文本分析概述](../overview.md)
* [常见问题解答 (FAQ)](../text-analytics-resource-faq.md)</br>
* [文本分析产品页](//go.microsoft.com/fwlink/?LinkID=759712)
