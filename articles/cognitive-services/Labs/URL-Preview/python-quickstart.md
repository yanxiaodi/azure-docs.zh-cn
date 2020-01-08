---
title: 快速入门：项目 URL 预览，Python
titlesuffix: Azure Cognitive Services
description: 通过 Python 快速开始使用项目 URL 预览的脚本示例。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: quickstart
ms.date: 03/29/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: e9b145b0786b2ab59552e5dfda6f0db63677443e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706884"
---
# <a name="quickstart-url-preview-with-python"></a>快速入门：通过 Python 使用 URL 预览

以下 Python 示例创建 SwiftKey 网站的 URL 预览： https://swiftkey.com/en 。

## <a name="prerequisites"></a>先决条件

获取[认知服务实验室](https://labs.cognitive.microsoft.com/en-us/project-answer-search)免费试用版的访问密钥

本示例使用 Python 3.6。

## <a name="code-scenario"></a>代码方案 

下面的代码创建一个 URL 预览。
它是通过以下步骤实现的：
1. 声明变量，以按主机和路径指定终结点。
2. 指定要预览的查询 URL，然后添加查询参数。  
3. 设置查询参数。
4. 定义搜索函数，用以创建请求并添加 Ocp-Apim-Subscription-Key 标头  。
5. 设置 Ocp-Apim-Subscription-Key 标头  。 
6. 建立连接，并发送请求。
7. 输出 JSON 结果。

本演示的完整代码如下：

```
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
```
## <a name="next-steps"></a>后续步骤
- [C# 快速入门](csharp.md)
- [Java 快速入门](java-quickstart.md)
- [JavaScript 快速入门](javascript.md)
- [Node URL 快速入门](node-quickstart.md)
