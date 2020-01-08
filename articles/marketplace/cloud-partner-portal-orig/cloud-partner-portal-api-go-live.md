---
title: 进入正常运行状态 |Azure Marketplace
description: 上线 API 启动产品/服务上线一览过程。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: ac56f86bad132f3e00a4b5c2507d65c0722c628c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935494"
---
<a name="go-live"></a>上线
=======

此 API 启动将应用投入生产的过程。 这通常是一项长期操作。 此调用使用[发布](./cloud-partner-portal-api-publish-offer.md) API 操作返回的通知电子邮件列表。

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>URI 参数
--------------

|  **名称**      |   **说明**                                                           | **数据类型** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | 要检索的产品/服务的发布者标识符，例如 `contoso`       |  String       |
| offerId        | 要检索的产品/服务的产品/服务标识符                                   |  String       |
| api-version    | API 最新版本                                                   |  Date         |
|  |  |  |


<a name="header"></a>Header
------

|  **名称**       |     **ReplTest1**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| 授权   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>正文示例
------------

### <a name="response"></a>响应

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>响应标头

|  **名称**             |      **ReplTest1**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  要查询的 URL，用于确定操作的当前状态            |
|  |  |


### <a name="response-status-codes"></a>响应状态代码

| **代码** |  **说明**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` - 已成功接受请求。 响应包含用于跟踪操作状态的位置。 |
|  400     | `Bad/Malformed request` - 响应正文中的其他错误信息。 |
|  404     |  `Not found` - 指定的实体不存在。                                       |
|  |  |
