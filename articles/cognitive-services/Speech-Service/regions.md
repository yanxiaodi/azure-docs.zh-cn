---
title: 区域-语音服务
titleSuffix: Azure Cognitive Services
description: 语音服务区域的参考。
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: c0414277b4851891911908ba4f42e92abedc86e4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553189"
---
# <a name="speech-service-supported-regions"></a>语音服务支持的区域

通过语音服务，应用程序可将音频转换为文本、执行语音翻译以及将文本转换为语音。 多个区域中均提供该服务，这些区域为语音 SDK 和 REST API 使用唯一终结点。

请务必使用与订阅的区域匹配的终结点。

## <a name="speech-sdk"></a>语音 SDK

在[语音 SDK](speech-sdk.md) 中，区域指定为字符串（例如，在 C# 语音 SDK 中用作 `SpeechConfig.FromSubscription` 的参数）。

### <a name="speech-to-text-text-to-speech-and-translation"></a>语音到文本、文本到语音转换和翻译

语音 SDK 在这些区域提供**语音识别**、**文本到语音** **转换和翻译**:

  地区 | 语音 SDK 参数 | 语音自定义门户
 ------|-------|--------
 美国西部 | `westus` | https://westus.cris.ai
 美国西部 2 | `westus2` | https://westus2.cris.ai
 East US | `eastus` | https://eastus.cris.ai
 美国东部 2 | `eastus2` | https://eastus2.cris.ai
 美国中部 | `centralus` | https://centralus.cris.ai
 美国中北部 | `northcentralus` | https://northcentralus.cris.ai
 美国中南部 | `southcentralus` | https://southcentralus.cris.ai
 印度中部 | `centralindia` | https://centralindia.cris.ai
 东亚 | `eastasia` | https://eastasia.cris.ai
 东南亚 | `southeastasia` | https://southeastasia.cris.ai
 日本东部 | `japaneast` | https://japaneast.cris.ai
 韩国中部 | `koreacentral` | https://koreacentral.cris.ai
 澳大利亚东部 | `australiaeast` | https://australiaeast.cris.ai
 加拿大中部 | `canadacentral` | https://canadacentral.cris.ai
 北欧 | `northeurope` | https://northeurope.cris.ai
 西欧 | `westeurope` | https://westeurope.cris.ai
 英国南部 | `uksouth` | https://uksouth.cris.ai
 法国中部 | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>意向识别

通过语音 SDK 实现**意向识别**的可用区域如下：

 全球区域 | 地区 | 语音 SDK 参数
 ------|-------|--------
 亚洲 | 东亚 | `eastasia`
 亚洲 | 东南亚 | `southeastasia`
 澳大利亚 | 澳大利亚东部 | `australiaeast`
 欧洲 | 北欧 | `northeurope`
 欧洲 | 西欧 | `westeurope`
 北美 | East US | `eastus`
 北美 | 美国东部 2 | `eastus2`
 北美 | 美国中南部 | `southcentralus`
 北美 | 美国中西部 | `westcentralus`
 北美 | 美国西部 | `westus`
 北美 | 美国西部 2 | `westus2`
 南美 | 巴西南部 | `brazilsouth`

这是[语言理解服务 (LUIS)](/azure/cognitive-services/luis/luis-reference-regions) 支持的发布区域的子集。

### <a name="voice-first-virtual-assistants"></a>语音优先虚拟助手

[语音 SDK](speech-sdk.md)支持以下区域中的 "**语音优先" 虚拟助手**功能:

地区 | 语音 SDK 参数
-------|---------------------
美国西部 | `westus`
美国西部 2 | `westus2`
East US | `eastus`
美国东部 2 | `eastus2`
西欧 | `westeurope`
北欧 | `northeurope`
东南亚 | `southeastasia`

## <a name="rest-apis"></a>REST API

语音服务还为语音转文本和文本转语音请求公开 REST 终结点。

### <a name="speech-to-text"></a>语音转文本

有关语音到文本的参考文档, 请参阅[语音到文本 REST API](rest-speech-to-text.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>文本转语音

有关文本到语音的参考文档, 请参阅[文本到语音 REST API](rest-text-to-speech.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]