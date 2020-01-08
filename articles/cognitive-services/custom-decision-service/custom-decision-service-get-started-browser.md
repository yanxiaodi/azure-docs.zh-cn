---
title: 在浏览器中调用 API - 自定义决策服务
titlesuffix: Azure Cognitive Services
description: 如何通过直接在浏览器中向自定义决策服务执行 API 调用来优化网页。
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 28ad4681242765bf2da9b1f13dc828e23cce1794
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707272"
---
# <a name="call-api-from-a-browser"></a>从浏览器调用 API

本文可帮助你直接从浏览器调用 Azure 自定义 AI 决策服务 API。

确保先[注册应用程序](custom-decision-service-get-started-register.md)。

让我们开始吧。 应用程序建模为具有首页，该页面链接到多个文章页面。 首页使用自定义 AI 决策服务来指定其文章页面中的排序。 将下面代码插入首页的 HTML 标头：

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

`data` 参数包含要呈现的 URL 排名。 有关详细信息，请参阅 [API 参考](custom-decision-service-api-reference.md)。

要处理热门文章的用户单击，请在首页上调用以下代码：

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

此处，`data` 是 `callback()` 函数的参数。 可在此[教程](custom-decision-service-tutorial-news.md#use-the-apis)中找到实现示例。

最后，需要提供操作集 API，这将返回自定义 AI 决策服务考虑的文章（操作）列表。 将此 API 实现为 RSS 源，如下所示：

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

此处，每个顶级 `<item>` 元素各描述一篇文章。 `<link>` 是必需的，被自定义 AI 决策服务用作操作 ID。 如果有 15 篇以上的文章，指定 `<date>`（采用标准 RSS 格式）。 使用 15 篇最新文章。 `<title>` 是可选的，用于为文章创建与文本相关的功能。

## <a name="next-steps"></a>后续步骤

* 通读[教程](custom-decision-service-tutorial-news.md)以获取更深入的示例。
* 请参阅 [API 参考](custom-decision-service-api-reference.md)，详细了解提供的功能。
