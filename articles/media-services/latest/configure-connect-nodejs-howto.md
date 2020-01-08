---
title: 连接到 Azure 媒体服务 v3 API-node.js
description: 了解如何通过 node.js 连接到媒体服务 v3 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 2ab620ec456610029d699952bb9625abfe4c21dc
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307887"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>连接到媒体服务 v3 API-node.js

本文介绍如何使用服务主体登录方法连接到 Azure 媒体服务 v3 node.js SDK。

## <a name="prerequisites"></a>先决条件

- 安装 [Node.js](https://nodejs.org/en/download/)。
- [创建媒体服务帐户](create-account-cli-how-to.md)。 请确保记住资源组名称和媒体服务帐户名。

> [!IMPORTANT]
> 查看[命名约定](media-services-apis-overview.md#naming-conventions)。

## <a name="create-packagejson"></a>创建包。 json

1. 使用偏好的编辑器创建包文件。
1. 打开文件并粘贴以下代码：

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

应指定以下包：

|package|描述|
|---|---|
|`azure-arm-mediaservices`|Azure 媒体服务 SDK。 <br/>若要确保使用的是最新的 Azure 媒体服务包，请选中 " [NPM 安装 windowsazure.mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)"。|
|`azure-storage`|存储 SDK。 将文件上传到资产时使用。|
|`ms-rest-azure`| 用于登录。|

您可以运行以下命令，确保使用的是最新的包：

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>连接到 node.js 客户端

1. 使用偏好的编辑器创建一个 .js 文件。
1. 打开该文件并粘贴以下代码。
1. 将 "终结点配置" 部分的值设置为从[访问 api](access-api-cli-how-to.md)获取的值。

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>运行你的应用程序

打开命令提示符。 浏览到示例的目录，然后执行以下命令：

```
npm install 
node index.js
```

## <a name="see-also"></a>请参阅

- [媒体服务概念](concepts-overview.md)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>后续步骤

浏览媒体服务 [Node.js 参考](https://aka.ms/ams-v3-nodejs-ref)文档并查看[示例](https://github.com/Azure-Samples/media-services-v3-node-tutorials)，了解如何将媒体服务 API 与 Node.js 配合使用。

