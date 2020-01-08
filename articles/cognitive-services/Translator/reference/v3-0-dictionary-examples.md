---
title: 文本翻译 API 字典示例方法
titleSuffix: Azure Cognitive Services
description: 使用文本翻译 API 字典示例方法。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: 419e11862b2c584686922cfc8d1db72ee4751a03
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934036"
---
# <a name="translator-text-api-30-dictionary-examples"></a>文本翻译 API 3.0：字典示例

提供示例，说明如何在上下文中使用字典中的术语。 此操作与[字典查找](./v3-0-dictionary-lookup.md)一起使用。

## <a name="request-url"></a>请求 URL

将 `POST` 请求发送到：

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>请求参数

查询字符串上传递的请求参数如下：

<table width="100%">
  <th width="20%">查询参数</th>
  <th>说明</th>
  <tr>
    <td>api-version</td>
    <td>必需参数。<br/>客户端所请求的 API 的版本。 值必须是 `3.0`。</td>
  </tr>
  <tr>
    <td>from</td>
    <td>必需参数。<br/>指定输入文本的语言。 源语言必须是 `dictionary` 范围中包含的[支持的语言](./v3-0-languages.md)之一。</td>
  </tr>
  <tr>
    <td>to</td>
    <td>必需参数。<br/>指定输出文本的语言。 目标语言必须是 `dictionary` 范围中包含的[支持的语言](./v3-0-languages.md)之一。</td>
  </tr>
</table>

请求标头包括：

<table width="100%">
  <th width="20%">标头</th>
  <th>描述</th>
  <tr>
    <td>身份验证标头</td>
    <td>必需的请求标头。<br/>请参阅<a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">用于身份验证的可用选项</a>。</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>必需的请求标头。<br/>指定有效负载的内容类型。 可能的值为：`application/json`</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>必需的请求标头。<br/>请求正文的长度。</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>可选。<br/>客户端生成的 GUID，用于唯一标识请求。 如果在查询字符串中使用名为 `ClientTraceId` 的查询参数包括了跟踪 ID，则可以省略此标头。</td>
  </tr>
</table> 

## <a name="request-body"></a>请求正文

请求的正文是一个 JSON 数组。 每个数组元素都是一个具有以下属性的 JSON 对象：

  * `Text`：一个字符串，指定要查找的术语。 这应该是前一个[字典查找](./v3-0-dictionary-lookup.md)请求的反向翻译中 `normalizedText` 字段的值。 它也可以是 `normalizedSource` 字段的值。

  * `Translation`：一个字符串，指定[字典查找](./v3-0-dictionary-lookup.md)操作先前返回的翻译文本。 这应该是[字典查找](./v3-0-dictionary-lookup.md)响应的 `translations` 列表中 `normalizedTarget` 字段的值。 该服务将返回特定源-目标字对的示例。

示例如下：

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

以下限制适用：

* 该数组最多可具有 10 个元素。
* 数组元素的文本值不能超过 100 个字符（包括空格）。

## <a name="response-body"></a>响应正文

成功的响应是一个 JSON 数组，其中的每个结果对应于输入数组中的一个字符串。 结果对象包括以下属性：

  * `normalizedSource`：一个字符串，提供源术语的规范化形式。 通常，这应该与请求正文中匹配列表索引处的 `Text` 字段的值相同。
    
  * `normalizedTarget`：一个字符串，给出目标术语的规范化形式。 通常，这应该与请求正文中匹配列表索引处的 `Translation` 字段的值相同。
  
  * `examples`：（源术语、目标术语）对的示例列表。 每个列表元素都是一个具有以下属性的对象：

    * `sourcePrefix`：在 `sourceTerm` 的值之前连接以形成完整示例的字符串。 不要添加空格字符，因为它在应存在时已经存在了。 此值可能为空字符串。

    * `sourceTerm`：一个字符串，等于被查找的实际术语。 该字符串添加了 `sourcePrefix` 和 `sourceSuffix` 以形成完整示例。 其值是分开的，因此可以在用户界面中标记，例如通过将其标为粗体。

    * `sourceSuffix`：在 `sourceTerm` 的值之后连接以形成完整示例的字符串。 不要添加空格字符，因为它在应存在时已经存在了。 此值可能为空字符串。

    * `targetPrefix`：一个类似于 `sourcePrefix` 但用于目标的字符串。

    * `targetTerm`：一个类似于 `sourceTerm` 但用于目标的字符串。

    * `targetSuffix`：一个类似于 `sourceSuffix` 但用于目标的字符串。

    > [!NOTE]
    > 如果字典中没有示例，则响应为“200 (正常)”，但 `examples` 列表为空列表。

## <a name="examples"></a>示例

此示例演示如何查找由英语术语 `fly` 及其西班牙语翻译 `volar` 组成的配对的示例。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

（为清楚起见已缩写）响应正文是：

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
