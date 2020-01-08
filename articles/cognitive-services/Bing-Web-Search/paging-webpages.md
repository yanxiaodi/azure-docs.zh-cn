---
title: 如何分页列出搜索结果 - 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何分页列出必应 Web 搜索 API 中的搜索结果。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: a038dc2706c7cb128751630f8997851409886290
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66384813"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>如何分页列出必应 Web 搜索 API 中的结果

如果调用 Web 搜索 API，必应会返回结果列表。 此列表是与查询相关的所有结果的一部分。 若要获取可访问结果的总估计数，请访问答案对象的 [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) 字段。  

下面的示例展示了 Web 答案对象包含的 `totalEstimatedMatches` 字段。  

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

若要翻页浏览可用网页，请使用 [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#count) 和 [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#offset) 查询参数。  

`count` 参数指定要在响应中返回的结果数。 最多可以请求在响应中获取 50 个结果。 默认值为 10。 提供的实际结果数可能小于请求获取的结果数。

`offset` 参数指定要跳过的结果数。 `offset` 从零开始，应小于 (`totalEstimatedMatches` - `count`)。  

若要每页显示 15 个网页，可以将 `count` 设置为 15，并将 `offset` 设置为 0，以获取第一页结果。 对于每个后续网页请求，让 `offset` 递增 15（例如，15、30）。  

下面的示例展示了如何从偏移 45 处开始请求获取 15 个网页。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

如果默认 `count` 值适用于实现，只需指定 `offset` 查询参数即可。  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Web 搜索 API 返回的结果包括网页，也可能包括图像、视频和新闻。 对搜索结果进行分页是分页 [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) 答案，而不是分页图像或新闻等其他答案。 例如，如果将 `count` 设置为 50，就会返回 50 个网页结果，但响应中可能还有其他答案的结果。 例如，响应可能包括 15 张图像和 4 篇新闻文章。 结果也可能在第一页（而不是第二页）上显示新闻，反之亦然。   

如果指定 `responseFilter` 查询参数，但未在筛选器列表中添加“网页”，请勿使用 `count` 和 `offset` 参数。 

> [!NOTE]
> `TotalEstimatedAnswers` 字段是可以为当前查询检索的搜索结果总数的估计值。  设置 `count` 和 `offset` 参数时，`TotalEstimatedAnswers` 数值可能会更改。 

## <a name="next-steps"></a>后续步骤

* [必应 Web 搜索 API 是什么](overview.md)？
