---
title: include 文件
description: include 文件
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173188"
---
### <a name="create-a-nodejs-application"></a>创建 Node.js 应用程序

如果创建中继时已禁用“需要客户端授权”选项，可使用任何浏览器向混合连接 URL 发送请求。 若要访问受保护的终结点，需在 `ServiceBusAuthorization` 标头中创建并传递令牌，如下所示。

首先，创建一个名为 `sender.js` 的新 JavaScript 文件。

### <a name="add-the-relay-npm-package"></a>添加中继 NPM 包

从项目文件夹中的 Node 命令提示符运行 `npm install hyco-https`。 此包还会导入常规 `https` 包。 对于客户端，主要的区别在于该包会提供用于构造中继 URI 和令牌的函数。

### <a name="write-some-code-to-send-messages"></a>编写一些代码来发送消息

1. 在 `sender.js` 文件的顶部，添加以下`constants`。
   
    ```js
    const https = require('hyco-https');
    ```

2. 将以下常量添加到 `sender.js` 文件，用于保存混合连接详细信息。 将括号中的占位符替换为在创建混合连接时获得的值。
   
   1. `const ns` - 中继命名空间。 请务必使用完全限定的命名空间名称，例如 `{namespace}.servicebus.windows.net`。
   2. `const path` - 混合连接的名称。
   3. `const keyrule` - SAS 密钥的名称。
   4. `const key` - SAS 密钥值。

3. 将以下代码添加到 `sender.js` 文件。 可以发现，该代码与 Node.js HTTPS 客户端的常规用法并无太大不同，只是前者添加了授权标头。
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    sender.js 文件的内容应如下所示：
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

