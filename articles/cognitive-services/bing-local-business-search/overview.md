---
title: 什么是必应当地企业搜索 API？
titleSuffix: Azure Cognitive Services
description: 必应当地企业搜索 API 是一种 RESTful 服务，该服务使应用程序可以基于搜索查询找到有关当地地点和企业的信息。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: bdada613ef46881bbf051144efded541ac3d0974
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996694"
---
# <a name="what-is-bing-local-business-search"></a>什么是必应当地企业搜索？
必应当地企业搜索 API 是一种 RESTful 服务，该服务使应用程序可以基于搜索查询找到有关当地企业的信息。 例如，`q=<business-name> in Redmond, Washington` 或 `q=Italian restaurants near me`。 

## <a name="features"></a>功能
| Feature | 说明 |  
| -- | -- | 
| [查找当地企业和位置](quickstarts/local-quickstart.md) | 必应当地企业搜索 API 通过查询获取本地化的结果。 结果包括企业网站的 URL 和显示文本、电话号码以及地理位置，包括：GPS 坐标、城市、街道地址 |  
| [使用地理边界筛选当地结果](specify-geographic-search.md) | 添加坐标作为搜索参数，以将结果限制到由圆形区域或方框指定的特定地理区域。 | 
| [按类别筛选当地企业结果](local-categories.md) | 按类别搜索当地企业结果 此选项使用调用方的反向 IP 地址或 GPS 坐标来返回各种企业类别的本地化结果。|

## <a name="workflow"></a>工作流
可以通过任何编程语言调用必应当地企业搜索 API，只要该语言能够发出 HTTP 请求并分析 JSON 响应即可。 可以使用 REST API 访问此服务。
 
1. 创建可以访问必应搜索 API 的[认知服务 API 帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。   
2. URL 对 `q=""` 查询参数的搜索词进行编码。 例如 `q=nearby+restaurant` 或 `q=nearby%20restaurant`。 如果需要，还要设置分页。 
3. [向必应当地企业搜索 API 发送请求](quickstarts/local-quickstart.md) 
4. 分析 JSON 响应 

> [!NOTE]
> 当前，当地企业搜索仅支持 `en-US` 市场。 
> [!NOTE]
> 目前，当地企业搜索不支持自动建议。 

## <a name="next-steps"></a>后续步骤
- [查询和响应](local-search-query-response.md)
- [当地企业搜索快速入门](quickstarts/local-quickstart.md)
- [当地企业搜索 API 参考](local-search-reference.md)
- [使用和显示要求](use-display-requirements.md)
