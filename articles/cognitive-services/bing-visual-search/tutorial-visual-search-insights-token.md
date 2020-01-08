---
title: 使用 ImageInsightsToken 在前面的搜索结果中查找类似的图像 - 必应视觉搜索
titleSuffix: Azure Cognitive Services
description: 使用必应视觉搜索 SDK 获取由 ImageInsightsToken 指定的图像的 URL。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: rosh
ms.openlocfilehash: 5f4faa290fe4ed02ab1ed75d23755af5dc20f215
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880614"
---
# <a name="find-similar-images-from-previous-searches-using-imageinsightstoken"></a>使用 ImageInsightsToken 在前面的搜索结果中查找类似的图像

可以使用视觉搜索 SDK 从以前返回 `ImageInsightsToken` 的搜索中查找联机图像。 此应用程序获取 `ImageInsightsToken` 并在后续搜索中使用该令牌。 然后，它将 `ImageInsightsToken` 发送到必应，并返回包含必应搜索 URL 以及以联机方式找到的类似图像的 URL 的结果。

在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchInsightsTokens.cs) 上可以找到此教程的完整源代码，以及附加的错误处理方法和注释。

## <a name="prerequisites"></a>先决条件

* 任何版本的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)。
* 如果使用的是 Linux/MacOS，则可以使用 [Mono](https://www.mono-project.com/) 运行此应用程序。
* NuGet 视觉搜索和图像搜索包。
    - 在 Visual Studio 的解决方案资源管理器中，右键单击项目，并从菜单中选择“管理 NuGet 包”  。 安装 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 包和 `Microsoft.Azure.CognitiveServices.Search.ImageSearch` 包。 安装 NuGet 包还会安装以下程序：
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="get-the-imageinsightstoken-from-the-bing-image-search-sdk"></a>从必应图像搜索 SDK 获取 ImageInsightsToken

此应用程序使用的 `ImageInsightsToken` 是通过[必应图像搜索 SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) 获得的。 在新的 C# 控制台应用程序中创建一个客户端，以便使用 `ImageSearchClient()` 来调用 API。 然后，将 `SearchAsync()` 与查询配合使用：

```csharp
var client = new ImageSearchClient(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey));
var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
Console.WriteLine("Search images for query \"canadian rockies\"");
```

使用 `imageResults.Value.First()` 存储第一个搜索结果，然后存储图像见解的 `ImageInsightsToken`。

```csharp
String insightTok = "None";
if (imageResults.Value.Count > 0)
{
    var firstImageResult = imageResults.Value.First();
    insightTok = firstImageResult.ImageInsightsToken;
}
else
{
    insightTok = "None found";
    Console.WriteLine("Couldn't find image results!");
}
```

此 `ImageInsightsToken` 会通过请求发送到必应视觉搜索。

## <a name="add-the-imageinsightstoken-to-a-visual-search-request"></a>将 ImageInsightsToken 添加到视觉搜索请求中

为视觉搜索请求指定 `ImageInsightsToken`，方法是在使用必应视觉搜索时，通过包含在响应中的 `ImageInsightsToken` 创建一个 `ImageInfo` 对象。

```csharp
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```

## <a name="use-bing-visual-search-to-find-images-from-an-imageinsightstoken"></a>使用必应视觉搜索从 ImageInsightsToken 中查找图像

`VisualSearchRequest` 对象包含 `ImageInfo` 中要搜索的图像的相关信息。 `VisualSearchMethodAsync()` 方法获取结果。 不需提供图像二进制文件，因为图像由令牌代表。

```csharp
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;

```

## <a name="iterate-through-the-visual-search-results"></a>以迭代方式访问视觉搜索结果

视觉搜索结果为 `ImageTag` 对象。 每个标记都包含 `ImageAction` 对象的列表。 每个 `ImageAction` 均包含一个 `Data` 字段，它是依赖于操作类型的值的列表。 例如，可以通过迭代方式访问 `visualSearchResults.Tags` 中的 `ImageTag` 对象，获取其中的 `ImageAction` 标记。 下面的示例会输出 `PagesIncluding` 操作的详细信息：

```csharp
if (visualSearchResults.Tags.Count > 0)
{
    // List of tags
    foreach (ImageTag t in visualSearchResults.Tags)
    {
        foreach (ImageAction i in t.Actions)
        {
            Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

            if (i.ActionType == "PagesIncluding")
            {
                foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                {
                    Console.WriteLine("ContentURL: " + o.ContentUrl);
                }
            }
        }
    }
}
```

### <a name="pagesincluding-actiontypes"></a>PagesIncluding ActionTypes

从操作类型获取实际图像 URL 需要将 `ActionType` 读取为 `ImageModuleAction` 的强制转换，其中包含具有值的列表的 `Data` 元素。 每个值是图像的 URL。  以下代码将 `PagesIncluding` 操作类型强制转换为 `ImageModuleAction` 并读取这些值：

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

有关这些数据类型的详细信息，请参阅[图像 - 视觉搜索](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch)。

## <a name="returned-urls"></a>返回的 URL

完整的应用程序返回以下 URL：

|ActionType  |代码  | |
|---------|---------|---------|
|MoreSizes -> WebSearchUrl     |         |
|VisualSearch -> WebSearchUrl     |         |
|ImageById -> WebSearchUrl    |         |
|RelatedSearches -> WebSearchUrl:    |         |
|DocumentLevelSuggestions -> WebSearchUrl:     |         |
|TopicResults -> WebSearchUrl    | https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1       |
|ImageResults -> WebSearchUrl    |  https:\//www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1       |

如上所述，`TopicResults` 和 `ImageResults` 类型包含相关图像的查询。 URL 链接到必应搜索结果。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建视觉搜索单页 Web 应用](tutorial-bing-visual-search-single-page-app.md)
