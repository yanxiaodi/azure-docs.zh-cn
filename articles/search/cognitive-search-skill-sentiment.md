---
title: 情绪认知搜索技能 - Azure 搜索
description: 从 Azure 搜索扩充管道中的文本提取积极-消极情绪分数。
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 8610506b7d62542e9eb801b06dd49a5e130fa7fa
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265745"
---
#   <a name="sentiment-cognitive-skill"></a>情绪认知技能

情绪技能可沿着一个积极-消极连续统评估非结构化文本，对于每个记录，会返回一个介于 0 和 1 之间的数字评分。 评分接近 1 代表积极的情绪，评分接近 0 代表消极的情绪。 此技能使用认知服务中的[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)提供的机器学习模型。

> [!NOTE]
> 通过增大处理频率、添加更多文档或添加更多 AI 算法来扩大范围时，需要[附加可计费的认知服务资源](cognitive-search-attach-cognitive-services.md)。 调用认知服务中的 API，以及在 Azure 搜索中的文档破解阶段提取图像时，会产生费用。 提取文档中的文本不会产生费用。
>
> 内置技能执行按现有[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)计费。 图像提取定价如 [Azure 搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)所述。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>数据限制
记录的最大大小应为5000个字符[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)。 如果需要在将数据发送到情绪分析器之前拆分数据，请使用[文本拆分技能](cognitive-search-skill-textsplit.md)。


## <a name="skill-parameters"></a>技能参数

参数区分大小写。

| 参数名称 |                      |
|----------------|----------------------|
| defaultLanguageCode | （可选）要应用到未显式指定语言的文档的语言代码。 <br/> 请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>技能输入 

| 输入名称 | 说明 |
|--------------------|-------------|
| text | 要分析的文本。|
| languageCode  |  （可选）表示记录的语言的字符串。 如果未指定此属性，则默认值为“en”。 <br/>请参阅[支持的语言的完整列表](../cognitive-services/text-analytics/text-analytics-supported-languages.md)。|

## <a name="skill-outputs"></a>技能输出

| 输出名称 | 说明 |
|--------------------|-------------|
| score | 介于 0 和 1 之间的值，表示所分析的文本的情绪。 值接近 0 代表消极情绪，接近 0.5 代表中性情绪，接近 1 代表积极的情绪。|


##  <a name="sample-definition"></a>示例定义

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
        }
    ]
}
```

##  <a name="sample-input"></a>示例输入

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>示例输出

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>说明
如果为空，则不为这些记录返回情绪分数。

## <a name="error-cases"></a>错误案例
如果某种语言不受支持，则生成错误，并且不会返回情绪分数。

## <a name="see-also"></a>另请参阅

+ [预定义技能](cognitive-search-predefined-skills.md)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
