---
title: 使用 Azure 媒体服务分析视频和音频文件 | Microsoft Docs
description: 使用 Azure 媒体服务时，可以使用 AudioAnalyzerPreset 和 VideoAnalyzerPreset 分析音频和视频内容。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/21/2019
ms.author: juliako
ms.openlocfilehash: bc4be8eaafe805e5d9a985b005efe80bc4af1d21
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2019
ms.locfileid: "71177997"
---
# <a name="analyzing-video-and-audio-files"></a>分析视频和音频文件

通过 Azure 媒体服务 v3，你可以通过媒体服务 v3 分析器预设（本文中所述）使用视频索引器从视频和音频文件中提取见解。 如果需要更详细的见解，请直接使用视频索引器。 如需了解何时要使用视频索引器与媒体服务分析器预设，请查看[比较文档](../video-indexer/compare-video-indexer-with-media-services-presets.md)。

若要使用媒体服务 v3 预设分析内容，请创建**转换**，然后提交使用以下某个预设的**作业**：[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) 或 **AudioAnalyzerPreset**。 以下文章演示了如何使用 **VideoAnalyzerPreset**：[教程：使用 Azure 媒体服务分析视频](analyze-videos-tutorial-with-api.md)。

> [!NOTE]
> 使用视频或音频分析器预设时，使用 Azure 门户将帐户设置为具有 10 个 S3 媒体预留单位。 有关详细信息，请参阅[缩放媒体处理](media-reserved-units-cli-how-to.md)。

## <a name="built-in-presets"></a>内置预设

媒体服务当前支持以下内置分析器预设：  

|**预设名称**|**方案**|**详细信息**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|分析音频|该预设应用一组基于 AI 的预定义分析操作，其中包括语音听录。 目前，该预设支持处理包含单个音轨的内容且音轨应仅包含一种语言的语音。 可以使用 BCP-47 格式“language tag-region”为输入中的音频有效负载指定语言。 支持的语言包括英语（en-US 和 en-GB）、西班牙语（es-ES 和 es-MX）、法语 (fr-FR)、意大利语 (it-IT)、日语 (ja-JP)、葡萄牙语 (pt-BR)、中文 (zh-CN)、德语 (de-DE)、阿拉伯语 (ar-EG)、俄语 (ru-RU)、印地语 (hi-IN) 和韩语 (ko-KR)。<br/><br/> 如果未指定语言或将语言设置为 null，自动语言检测将选择检测到的第一种语言并使用所选语言处理整个文件。 自动语言检测功能目前支持英语、中文、法语、德语、意大利语、日语、西班牙语、俄语和葡萄牙语。 它目前不支持在检测到第一种语言后在各语言之间进行动态切换。 自动语言检测功能最适用于包含清晰可辨的语音的录音。 如果自动语言检测未能找到语言，脚本将回退到英语。|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|分析音频和视频|从音频和视频中提取见解（丰富的元数据），并输出 JSON 格式的文件。 可以指定在处理视频文件时是否只想提取音频见解。 有关详细信息，请参阅[分析视频](analyze-videos-tutorial-with-api.md)。|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|检测视频中的所有表面|描述在分析视频时要使用的设置，以便检测所有表面。|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

使用此预设，可以从音频或视频文件中提取多个音频见解。 输出包括一个 JSON 文件（包含所有见解）和该音频脚本的 VTT 文件。 此预设接受 [BCP47](https://tools.ietf.org/html/bcp47) 字符串格式的属性，该属性用于指定输入文件的语言。 音频见解包括：

* 音频听录 - 带有时间戳的口语脚本。 支持多种语言
* 发言人索引 - 发言人及其口语的映射
* 语音情绪分析 - 对音频听录进行情绪分析后的输出
* 关键字 - 从音频听录提取的关键字。

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

使用此预设，可以从视频中提取多个音频和视频见解。 输出包括一个 JSON 文件（包含所有见解）、该视频脚本的 VTT 文件以及视频缩略图集合。 此预设还接受 [BCP47](https://tools.ietf.org/html/bcp47) 字符串格式的属性，该属性表示视频的语言。 视频见解包括上述所有音频见解，此外还包含以下项：

* 面部跟踪 - 视频中出现人脸的时间段。 每张人脸都有一个面部 ID 和对应的视频缩略图集合
* 视觉文本 - 通过光学字符识别检测出的文本。 该文本具有时间标记，还用于提取包括音频脚本在内的关键字
* 关键帧 - 从视频中提取的关键帧集合
* 视觉内容审核 - 被标记为成人或不雅性质的视频部分
* 注释 - 基于预定义对象模型对视频进行注释的结果

##  <a name="insightsjson-elements"></a>insights.json 元素

输出包括一个 JSON 文件 (insights.json)，其中包含了从音频或视频得出的所有见解。 该 json 文件可能包含以下元素：

### <a name="transcript"></a>脚本

|姓名|描述|
|---|---|
|ID|行 ID。|
|text|脚本本身。|
|language|脚本语言。 旨在支持每行语言不同的脚本。|
|instances|出现该行的时间范围列表。 如果实例是脚本，则只有 1 个实例。|

例如：

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|姓名|描述|
|---|---|
|ID|OCR 行 ID。|
|text|OCR 文本。|
|confidence|识别置信度。|
|language|OCR 语言。|
|instances|出现此 OCR 的时间范围列表（同一 OCR 可重复多次出现）。|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>人脸

|姓名|描述|
|---|---|
|ID|人脸 ID。|
|name|人脸姓名。 可以为“Unknown #0”、公认的名人或客户训练的人。|
|confidence|人脸识别置信度。|
|description|名人的说明。 |
|thumbnailId|该人脸的缩略图 ID。|
|knownPersonId|如果是已知人员，则为此人的内部 ID。|
|referenceId|如果是必应名人，则为此人的必应 ID。|
|referenceType|当前仅限必应。|
|标题|如果是名人，则为此人的头衔（例如“Microsoft 的 CEO”）。|
|imageUrl|如果是名人，则为此人的图像 URL。|
|实例|在给定时间范围内出现该人脸的实例。 每个实例还具有一个 thumbnailsId。 |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>截图

|姓名|描述|
|---|---|
|ID|截图 ID。|
|keyFrames|截图内的关键帧列表（每个关键帧都有一个 ID 和实例时间范围列表）。 关键帧实例具有一个 thumbnailId 字段，该字段包含关键帧的缩略图 ID。|
|实例|此截图的时间范围列表（截图仅有 1 个实例）。|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statistics

|姓名|描述|
|---|---|
|CorrespondenceCount|视频中对应关系的数目。|
|WordCount|每个发言人的单词数。|
|SpeakerNumberOfFragments|发言人在视频中的片段数量。|
|SpeakerLongestMonolog|发言人的最长独白。 如果发言人在独白中有沉默，则会将沉默期包含在内。 删除独白开头和结尾的沉默期。| 
|SpeakerTalkToListenRatio|计算方式为将发言人的独白时间（开头和结尾之间没有沉默期）除以视频总时间。 时间将四舍五入为三位小数。|


### <a name="sentiments"></a>情绪

情绪依据其 sentimentType 字段得出（积极/中立/消极）。 例如：0-0.1、0.1-0.2。

|姓名|描述|
|---|---|
|ID|情绪 ID。|
|averageScore |该情绪类型的所有实例的所有分数的均值 - 积极/中立/消极|
|instances|出现此情绪的时间范围列表。|
|sentimentType |类型可以是“Positive”、“Neutral”或“Negative”。|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>标签

|姓名|描述|
|---|---|
|ID|标签 ID。|
|name|标签名称（例如“计算机”、“电视”）。|
|language|标签名称语言（转换后）。 BCP-47|
|instances|出现此标签的时间范围列表（一个标签可重复多次出现）。 每个实例都有置信度字段。 |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>关键字

|姓名|描述|
|---|---|
|ID|关键字 ID。|
|text|关键字文本。|
|confidence|关键字的识别置信度。|
|language|关键字语言（转换后）。|
|instances|出现此关键字的时间范围列表（一个关键字可重复多次出现）。|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 块包含视频索引器找到的、可能具有成人内容的时间范围。 如果 visualContentModeration 为空，则表示未识别到成人内容。

被确定包含成人或不雅内容的视频可能仅可供私人观看。 用户可以选择请求人工审查内容，在这种情况下，IsAdult 属性将包含人工审查的结果。

|姓名|描述|
|---|---|
|ID|视觉内容审核 ID。|
|adultScore|成人内容评分（由内容审核员提供）。|
|racyScore|不雅内容评分（由内容审核员提供）。|
|实例|显示此视觉内容审核的时间范围列表。|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>后续步骤

[教程：使用 Azure 媒体服务分析视频](analyze-videos-tutorial-with-api.md)
