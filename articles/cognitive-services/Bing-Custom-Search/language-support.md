---
title: 语言支持 - 必应自定义搜索 API
titleSuffix: Azure Cognitive Services
description: 必应自定义搜索 API 支持的语言和区域列表。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 85326ae9166f7ea15ec2f45c01755b8f9ef03aff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388565"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>必应自定义搜索 API 的语言和区域支持

必应自定义搜索 API 支持超过 36 个国家/地区，其中很多具有多种语言。

尽管是可选的，但请求应该指定 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) 查询参数，它确定你希望结果源自其中的市场。 对于可选查询参数列表，请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

可以使用 `cc` 查询参数指定国家/地区。 如果指定了国家/地区，还必须使用 `Accept-Language` 标头指定一个或多个语言代码。 支持的语言因国家/地区而异；“市场”表中提供了每个国家/地区适用的语言  。

`Accept-Language` 标头和 `setLang` 查询参数相互排斥，不可同时指定两者。 有关详细信息，请参阅 [Accept-language](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage)。

## <a name="countriesregions"></a>国家/地区

|国家/地区|代码|
|-------|----|
|阿根廷|AR|
|澳大利亚|AU|
|奥地利|AT|
|比利时|BE|
|巴西|BR|
|加拿大|CA|
|智利|CL|
|丹麦|DK|
|芬兰|FI|
|法国|FR|
|德国|DE|
|中国香港特别行政区|HK|
|印度|IN|
|印度尼西亚|ID|
|意大利|IT|
|日本|JP|
|韩国|KR|
|马来西亚|MY|
|墨西哥|MX|
|荷兰|NL|
|新西兰|NZ|
|挪威|否|
|中国|CN|
|波兰|PL|
|葡萄牙|PT|
|菲律宾|PH|
|俄罗斯|RU|
|沙特阿拉伯|SA|
|南非|ZA|
|西班牙|ES|
|瑞典|SE|
|瑞士|CH|
|中国台湾|TW|
|土耳其|TR|
|英国|GB|
|美国|美国|


## <a name="markets"></a>市场

|国家/地区|语言|市场代码|
|-------|--------|-----------|
|阿根廷|西班牙语|es-AR|
|澳大利亚|英语|en-AU|
|奥地利|德语|de-AT|
|比利时|荷兰语|nl-BE|
|比利时|法语|fr-BE|
|巴西|葡萄牙语|pt-BR|
|加拿大|英语|en-CA|
|加拿大|法语|fr-CA|
|智利|西班牙语|es-CL|
|丹麦|丹麦语|da-DK|
|芬兰|芬兰语|fi-FI|
|法国|法语|fr-FR|
|德国|德语|de-DE|
|中国香港特别行政区|繁体中文|zh-HK|
|印度|英语|en-IN|
|印度尼西亚|英语|en-ID|
|意大利|意大利语|it-IT|
|日本|日语|ja-JP|
|韩国|韩语|ko-KR|
|马来西亚|英语|en-MY|
|墨西哥|西班牙语|es-MX|
|荷兰|荷兰语|nl-NL|
|新西兰|英语|en-NZ|
|挪威|挪威语|no-NO|
|中国|中文|zh-CN|
|波兰|波兰语|pl-PL|
|葡萄牙|葡萄牙语|pt-PT|
|菲律宾|英语|en-PH|
|俄罗斯|俄语|ru-RU|
|沙特阿拉伯|阿拉伯语|ar-SA|
|南非|英语|en-ZA|
|西班牙|西班牙语|es-ES|
|瑞典|瑞典语|sv-SE|
|瑞士|法语|fr-CH|
|瑞士|德语|de-CH|
|中国台湾|繁体中文|zh-TW|
|土耳其|土耳其语|tr-TR|
|英国|英语|en-GB|
|美国|英语|en-US|
|美国|西班牙语|es-US|
