---
title: 通过客户端库开始使用必应语音识别 API | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: 使用 Microsoft 认知服务中的必应语音客户端库开发将语音转换为文本的应用程序。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8ea0c9e7104b29456749c7f1af2a671b32a8fde2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966846"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>必应语音服务客户端库入门

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

除了通过 REST API 直接发出 HTTP 请求外，必应语音服务还为开发人员提供采用不同语言的语音客户端库。 语音客户端库：

- 支持更高级的语音识别功能，例如实时的中间结果，长时间的音频流（长达 10 分钟）和连续识别。
- 采用首选语言提供简单和常用的 API。
- 隐藏低级别通信详细信息。

目前，提供以下必应语音客户端库：

- [C# 桌面库](GetStartedCSharpDesktop.md)
- [C# 服务库](GetStartedCSharpServiceLibrary.md)
- [JavaScript 库](GetStartedJSWebsockets.md)
- [适用于 Android 的 Java 库](GetStartedJavaAndroid.md)
- [适用于 iOS 的 Objective-C 库](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>其他资源

- [示例](../samples.md)页提供使用语音客户端库的完整示例。
- 如果需要尚不支持的客户端库，可以创建自己的 SDK。 在平台上实现[语音 WebSocket 协议](../API-Reference-REST/websocketprotocol.md)并使用所选择的语言。

## <a name="license"></a>许可证

所有认知服务 SDK 和示例均获得 MIT 许可证的许可。 有关详细信息，请参阅[许可证](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md)。
