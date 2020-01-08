---
title: 教程：使用必应视觉搜索 SDK 裁剪图像
description: 使用必应视觉搜索 SDK 从图像上的特定区域获得见解。
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 04/26/2019
ms.author: rosh
ms.openlocfilehash: a6b625325e2adfe441d8abd2012f100780b659b4
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910032"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>教程：使用适用于 C# 的必应视觉搜索 SDK 裁剪图像

使用必应视觉搜索 SDK，可以在查找相似的在线图像之前裁剪图像。 此应用程序从包含多个人物的图像中裁剪单个人物，并返回包含在线找到的相似图像的搜索结果。

在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs) 上可以找到此应用程序的完整源代码，以及附加的错误处理方法和注释。

本教程展示了如何执行以下操作：

> [!div class="checklist"]
> * 使用必应视觉搜索 SDK 发送请求
> * 使用必应视觉搜索裁剪要搜索的图像区域
> * 接收并处理响应
> * 在响应中查找操作项的 URL

## <a name="prerequisites"></a>先决条件

* 任何版本的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)。
* 如果使用的是 Linux/MacOS，则可使用 [Mono](https://www.mono-project.com/) 运行此应用程序。
* 已安装 [NuGet 自定义搜索](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)包。
    - 在 Visual Studio 的解决方案资源管理器中，右键单击项目，并从菜单中选择“管理 NuGet 包”  。 安装 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 包。 安装 NuGet 自定义搜索包还会安装以下程序集：
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>指定图像裁剪区域

此应用程序裁剪 Microsoft 高级领导团队的以下图像的某个区域。 此裁剪区域是使用左上角和右下角的坐标定义的，表示为占整个图像的百分比：  

![Microsoft 高级领导团队](./media/MS_SrLeaders.jpg)

将通过从裁剪区域创建 `ImageInfo` 对象并将 `ImageInfo` 对象加载到 `VisualSearchRequest` 中裁剪此图像。 `ImageInfo` 对象还包括图像的 URL：

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>搜索与裁剪区域相似的图像

变量 `VisualSearchRequest` 包含关于图像的裁剪区域及其 URL 的信息。 `VisualSearchMethodAsync()` 方法会获取结果：

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>从 `ImageModuleAction` 获取 URL 数据

必应视觉搜索结果为 `ImageTag` 对象。 每个标记都包含 `ImageAction` 对象的列表。 每个 `ImageAction` 均包含一个 `Data` 字段，它是依赖于操作类型的值的列表。

可以使用以下代码打印各种类型：

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

完整的应用程序返回：

|ActionType  |代码  | |
|---------|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entity -> WebSearchUrl     | https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1        |
|TopicResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1        |
|ImageResults -> WebSearchUrl    |  https\://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1        |

如上所示，`Entity` ActionType 包含一个必应搜索查询，该查询返回有关某个可识别人物、地点或事物的信息。 `TopicResults` 和 `ImageResults` 类型包含相关图像的查询。 列表中的 URL 链接到必应搜索结果。

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>获取 `PagesIncluding` `ActionType` 图像的 URL

获取实际图像 URL 需要将 `ActionType` 读取为 `ImageModuleAction` 的强制转换，其中包含具有值的列表的 `Data` 元素。 每个值是图像的 URL。 以下代码将 `PagesIncluding` 操作类型强制转换为 `ImageModuleAction` 并读取这些值：

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [创建视觉搜索单页 Web 应用](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>另请参阅
> [什么是必应视觉搜索 API？](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview)
