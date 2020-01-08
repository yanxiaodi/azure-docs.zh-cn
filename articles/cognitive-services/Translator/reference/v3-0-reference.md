---
title: 文本翻译 API V3.0 参考
titleSuffix: Azure Cognitive Services
description: 文本翻译 API v3.0 参考文档。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: cb5a3b8572cebfd6c0731a9e572e966fda280be6
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772790"
---
# <a name="translator-text-api-v30"></a>文本翻译 API v3.0

## <a name="whats-new"></a>新增功能

文本翻译 API 版本 3 提供了基于 JSON 的新型 Web API。 它通过将现有功能合并到更少的操作中来提高可用性和性能，并提供新功能。

 * 音译可将一种语言的文本从一个脚本转换为另一个脚本。
 * 在一个请求中翻译成多种语言。
 * 在一个请求中进行语言检测、翻译和音译。
 * 字典可查找术语的替代翻译，可查找反向翻译以及显示上下文中使用的术语的示例。
 * 更详细的语言检测结果。

## <a name="base-urls"></a>基 URL

Microsoft Translator 位于多个数据中心位置之外。 目前它们位于 10 个 [Azure 地理区域](https://azure.microsoft.com/global-infrastructure/regions)：

* **美洲：** 美国东部、美国中南部、美国西部和美国西部2 
* **亚太区：** 韩国南部、日本东部、东南亚和澳大利亚东部
* **欧洲：** 欧洲北部和欧洲西部

在大多数情况下，对 Microsoft 文本翻译 API 的请求由距离请求的来源位置最近的数据中心处理。 如果数据中心出现故障，请求可能会路由到 Azure 地理区域之外。

若要强制由特定 Azure 地理区域处理请求，请将 API 请求中的全球终结点更改为所需的区域终结点：

|描述|Azure 地理区域|基 URL|
|:--|:--|:--|
|Azure|全局（非区域）|   api.cognitive.microsofttranslator.com|
|Azure|美国|   api-nam.cognitive.microsofttranslator.com|
|Azure|欧洲|  api-eur.cognitive.microsofttranslator.com|
|Azure|亚太区|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>身份验证

订阅 Microsoft 认知服务中的文本翻译 API 或[认知服务多服务](https://azure.microsoft.com/pricing/details/cognitive-services/)，并使用你的订阅密钥（在 Azure 门户中提供）进行身份验证。 

有三个标头可用于对你的订阅进行身份验证。 下表介绍了每个标头的使用方式：

|标头|说明|
|:----|:----|
|Ocp-Apim-Subscription-Key|如果要传递密钥，请与认知服务订阅一起使用。<br/>该值是文本翻译 API 订阅的 Azure 密钥。|
|授权|如果要传递身份验证令牌，请与认知服务订阅一起使用。<br/>该值是持有者令牌：`Bearer <token>`。|
|Ocp-Apim-Subscription-Region|*如果要传递多服务机密密钥，请将用于认知服务多服务订阅。*<br/>值是多服务订阅的区域。 如果不使用多服务订阅，此值是可选的。|

###  <a name="secret-key"></a>密钥
第一个选项是使用 `Ocp-Apim-Subscription-Key` 标头进行身份验证。 只需将 `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` 标头添加到你的请求。

### <a name="authorization-token"></a>授权令牌
或者，可以交换访问令牌的密钥。 此令牌作为 `Authorization` 标头包含在每个请求中。 若要获取授权令牌，请向以下 URL 发出 `POST` 请求：

| 环境     | 身份验证服务 URL                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

以下是根据给定密钥获取令牌的示例请求：

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

成功的请求会在响应正文中将编码的访问令牌作为纯文本返回。 有效的令牌在授权中作为持有者令牌传递给翻译服务。

```
Authorization: Bearer <Base64-access_token>
```

身份验证令牌的有效期为 10 分钟。 在对翻译 API 进行多次调用时，应重新使用该令牌。 但是，如果程序在很长一段时间内向翻译 API 发出请求，则程序必须定期（例如每 8 分钟）请求一个新的访问令牌。

### <a name="multi-service-subscription"></a>多服务订阅

最后一个身份验证选项是使用认知服务的多服务订阅。 这样便可以使用一个密钥对多个服务的请求进行身份验证。 

使用多服务密钥时，必须在请求中包括两个身份验证标头。 第一个标头可传递密钥，第二个标头可指定与你的订阅关联的区域。 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

区域对于多服务文本 API 订阅是必需的。 你选择的区域是在使用多服务订阅密钥时可用于文本翻译的唯一区域，并且必须是通过 Azure 门户注册多服务订阅时所选的同一区域。

可用区域包括`australiaeast` `brazilsouth` 、、`centralus` 、`eastus`、、 、`centraluseuap` 、、`japanwest`、、、、 `eastasia` `canadacentral` `centralindia` `eastus2` `francecentral` `japaneast` `koreacentral`、 、`northcentralus` 、、`southeastasia`、、 、、`southafricanorth`、和。 `westcentralus` `southcentralus` `uksouth` `northeurope` `westeurope` `westus` `westus2`

如果使用参数 `Subscription-Key` 传递查询字符串中的密钥，则必须使用查询参数 `Subscription-Region` 指定区域。

如果使用持有者令牌，则必须从区域终结点中获取令牌：`https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`。


## <a name="errors"></a>错误

标准错误响应是具有名为 `error` 的名称/值对的 JSON 对象。 该值也是具有以下属性的 JSON 对象：

  * `code`：服务器定义的错误代码。

  * `message`：一个提供人类可读的错误表示形式的字符串。

例如，拥有免费试用版订阅的客户会在免费配额用尽后收到以下错误：

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
错误代码是一个 6 位数字，包括 3 位数的 HTTP 状态代码，后接用于进一步将错误分类的 3 位数。 常见错误代码包括：

| 代码 | 描述 |
|:----|:-----|
| 400000| 某个请求输入无效。|
| 400001| “scope”参数无效。|
| 400002| “category”参数无效。|
| 400003| 语言说明符缺失或无效。|
| 400004| 目标脚本说明符（“To script”）缺失或无效。|
| 400005| 输入文本缺失或无效。|
| 400006| 语言和脚本的组合无效。|
| 400018| 源脚本说明符（“From script”）缺失或无效。|
| 400019| 指定的某个语言不受支持。|
| 400020| 输入文本数组中的某个元素无效。|
| 400021| API 版本参数缺失或无效。|
| 400023| 指定的某个语言对无效。|
| 400035| 源语言（“From”字段）无效。|
| 400036| 目标语言（“To”字段）缺失或无效。|
| 400042| 指定的某个选项（“Options”字段）无效。|
| 400043| 客户端跟踪 ID（ClientTraceId 字段或 X-ClientTranceId 标头）缺失或无效。|
| 400050| 输入文本过长。 查看[请求限制](../request-limits.md)。|
| 400064| “translation”参数缺失或无效。|
| 400070| 目标脚本（ToScript 参数）的数目与目标语言（To 参数）的数目不匹配。|
| 400071| TextType 的值无效。|
| 400072| 输入文本的数组包含过多的元素。|
| 400073| 脚本参数无效。|
| 400074| 请求正文是无效的 JSON。|
| 400075| 语言对和类别组合无效。|
| 400077| 超过了最大请求大小。 查看[请求限制](../request-limits.md)。|
| 400079| 请求用于在源语言与目标语言之间进行翻译的自定义系统不存在。|
| 400080| 语言或脚本不支持音译。|
| 401000| 由于凭据缺失或无效，请求未授权。|
| 401015| “提供的凭据适用于语音 API。 此请求需要文本 API 的凭据。 请使用文本翻译 API 的订阅。”|
| 403000| 不允许该操作。|
| 403001| 由于订阅已超过其免费配额，因此不允许该操作。|
| 405000| 请求的资源不支持该请求方法。|
| 408001| 正在准备所请求的翻译系统。 请在几分钟后重试。|
| 408002| 等待传入流时请求超时。 客户端没有在服务器准备等待的时间内生成请求。 客户端可以在以后的任何时间重复该请求，而不做任何修改。|
| 415000| Content-Type 标头缺失或无效。|
| 429000、429001、429002| 由于客户端已超出请求限制，服务器拒绝了请求。|
| 500000| 发生了意外错误。 如果该错误持续出现，请报告发生错误的日期/时间、响应标头 X-RequestId 中的请求标识符，以及请求标头 X-ClientTraceId 中的客户端标识符。|
| 503000| 服务暂时不可用。 请重试。 如果该错误持续出现，请报告发生错误的日期/时间、响应标头 X-RequestId 中的请求标识符，以及请求标头 X-ClientTraceId 中的客户端标识符。|

