---
title: 快速入门：项目答案答搜索、Node
description: 通过 Node 开始使用项目答案搜索。
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: ace83b9bdc8003e2e38d5b0a188582bc0cc2147c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707097"
---
# <a name="quickstart-project-answer-search-with-node"></a>快速入门：通过 Node 使用项目答案搜索

以下节点示例创建有关约塞米蒂国家公园信息的查询。

## <a name="prerequisites"></a>先决条件

获取[认知服务实验室](https://labs.cognitive.microsoft.com/en-us/project-answer-search)免费试用版的访问密钥

本示例使用节点 v8.9.4

## <a name="code-scenario"></a>代码方案 

使用以下代码获取解答。
该代码通过以下步骤实现：
1. 声明变量，以按主机和路径指定终结点。
2. 指定要预览的查询 URL，然后添加查询参数。  
3. 为响应创建处理程序函数。
4. 定义搜索函数，用以创建请求并添加 Ocp-Apim-Subscription-Key 标头  。
5. 运行“搜索”函数。 

本演示的完整代码如下：

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt= + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>后续步骤
- [C# 示例代码](c-sharp-quickstart.md)
- [Java 快速入门](java-quickstart.md)
- [Python 快速入门](python-quickstart.md)
