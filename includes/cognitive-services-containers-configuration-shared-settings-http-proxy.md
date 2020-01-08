---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 84cd8ed79281b005407b5a857398b5669635c072
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320491"
---
如果需要配置 HTTP 代理以发出出站请求，请使用以下两个参数：

| 名称 | 数据类型 | 描述 |
|--|--|--|
|HTTP_PROXY|string|要使用的代理，例如 `http://proxy:8888`<br>`<proxy-url>`|
|HTTP_PROXY_CREDS|string|对代理进行身份验证所需的凭据，例如 username:password。|
|`<proxy-user>`|string|代理的用户。|
|`<proxy-password>`|string|与代理的 `<proxy-user>` 关联的密码。|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTP_PROXY=<proxy-url> \
HTTP_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
