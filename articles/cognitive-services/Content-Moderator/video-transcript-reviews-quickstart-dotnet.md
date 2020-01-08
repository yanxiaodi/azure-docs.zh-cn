---
title: 使用 .NET 创建视频脚本评审 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 使用用于 .NET 的内容审查器 SDK 创建视频脚本评审
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: f3f93824eb021e0fb75e1a6b81935292379d50e5
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883074"
---
# <a name="create-video-transcript-reviews-using-net"></a>使用 .NET 创建视频脚本评论

本文提供了信息和代码示例，帮助你快速开始结合使用[内容审查器 SDK 和 C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 来执行以下操作：

- 为人工审查器创建视频评论
- 向评论中添加已审查的脚本
- 发布评论

## <a name="prerequisites"></a>先决条件

- 如果尚未这样做, 请登录或在内容审查器[审核工具](https://contentmoderator.cognitive.microsoft.com/)站点上创建帐户。
- 本文假定你已[审查视频](video-moderation-api.md)，并在评论工具中[创建了视频评论](video-reviews-quickstart-dotnet.md)以便进行人工决策。 你现在想要在评论工具中添加已审查的视频脚本。

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>确保 API 密钥可以调用评审 API（创建作业）

完成上述步骤后，如果从 Azure 门户着手，最终可能会得到两个内容审查器密钥。

如果计划在 SDK 示例中使用 Azure 提供的 API 密钥，请按照[将 Azure 密钥与评审 API 配合使用](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis)部分中提到的步骤操作，以允许应用程序调用评审 API 并创建评审。

如果使用评审工具生成的免费试用密钥，则评审工具帐户已经知道密钥，因此无需其他步骤。

## <a name="prepare-your-video-for-review"></a>准备视频以供评论

将脚本添加到视频评论中。 视频必须在线发布。 你需要它的流式处理终结点。 流式处理终结点允许评论工具视频播放器播放视频。

![视频演示缩略图](images/ams-video-demo-view.PNG)

- 复制此 [Azure 媒体服务演示](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest)页面上的 **URL** 作为清单 URL。

## <a name="create-your-visual-studio-project"></a>创建 Visual Studio 项目

1. 向解决方案添加新的“控制台应用(.NET Framework)”项目。

1. 将项目命名为“VideoTranscriptReviews”。

1. 将此项目选为解决方案的单一启动项目。

### <a name="install-required-packages"></a>安装所需程序包

为 TermLists 项目安装以下 NuGet 包。

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>更新程序的 using 语句

按下面所示修改程序的 using 语句。


```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>添加私有属性

将以下私有属性添加到 VideoTranscriptReviews 命名空间中的 Program 类。

如果需要，可替换这些属性的示例值。

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Replace this example location with the location for your Content Moderator account.
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

        // NOTE: Replace this example key with a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>创建内容审查器客户端对象

将以下方法定义添加到 VideoTranscriptReviews 命名空间中的 Program 类。

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureBaseURL
    };
}
```

## <a name="create-a-video-review"></a>创建视频评论

使用 **ContentModeratorClient.Reviews.CreateVideoReviews** 创建视频评论。 有关详细信息，请参阅 [API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)。

**CreateVideoReviews** 具有以下必需参数：
1. 一个字符串，包含应为“application/json”的 MIME 类型。 
1. 内容审查器团队名称。
1. **IList\<CreateVideoReviewsBodyItem >** 对象。 每个 CreateVideoReviewsBodyItem 对象表示一次视频审查。 本快速入门一次创建一条评论。

**CreateVideoReviewsBodyItem** 具有多个属性。 至少应设置以下属性：
- Content。 要评论的视频的 URL。
- ContentId。 要分配给视频评论的 ID。
- Status。 将该值设置为“未发布”。 如果未进行设置，则默认为“挂起”，这意味着视频评论已发布并且正在等待人工评论。 视频评论发布后，就无法再向其中添加视频帧、脚本或脚本审查结果。

> [!NOTE]
> **CreateVideoReviews**返回 > 的\<IList 字符串。 这些字符串中的每一个都包含视频评论 ID。 这些 ID 是 GUID，与 **ContentId** 属性的值不同。

将以下方法定义添加到 VideoReviews 命名空间中的 Program 类。

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> 内容审查器服务密钥有一个每秒请求数 (RPS) 速率限制。 如果超过该限制，SDK 会引发错误代码为 429 的异常。
>
> 免费层密钥有一个 RPS 速率限制。

## <a name="add-transcript-to-video-review"></a>将脚本添加到视频评论中

可以使用 **ContentModeratorClient.Reviews.AddVideoTranscript** 将脚本添加到视频评论中。 **AddVideoTranscript** 具有以下必需参数：
1. 内容审查器团队 ID。
1. **CreateVideoReviews** 返回的视频评论 ID。
1. 包含脚本的 **Stream** 对象。

脚本必须采用 WebVTT 格式。 有关更多信息，请参阅 [WebVTT：Web 视频文本轨道格式](https://www.w3.org/TR/webvtt1/)。

> [!NOTE]
> 该程序使用 VTT 格式的示例脚本。 在实际解决方案中，可使用 Azure Media Indexer 服务根据视频[生成脚本](https://docs.microsoft.com/azure/media-services/media-services-index-content)。

将以下方法定义添加到 VideotranscriptReviews 命名空间中的 Program 类。

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>将脚本审查结果添加到视频评论中

除了向视频评论中添加脚本外，还可以添加该脚本的审查结果。 可使用 **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult** 执行此操作。 有关详细信息，请参阅 [API 参考](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff)。

**AddVideoTranscriptModerationResult** 具有以下必需参数：
1. 一个字符串，包含应为“application/json”的 MIME 类型。 
1. 内容审查器团队名称。
1. **CreateVideoReviews** 返回的视频评论 ID。
1. IList\<TranscriptModerationBodyItem >。 **TranscriptModerationBodyItem** 具有以下属性：
1. **Terms**。 IList\<TranscriptModerationBodyItemTermsItem >。 **TranscriptModerationBodyItemTermsItem** 具有以下属性：
1. **Index**。 该词语从零开始的索引。
1. **Term**。 一个包含该词语的字符串。
1. **Timestamp**。 一个字符串，包含在脚本中找到词语的时间（以秒为单位）。

脚本必须采用 WebVTT 格式。 有关更多信息，请参阅 [WebVTT：Web 视频文本轨道格式](https://www.w3.org/TR/webvtt1/)。

将以下方法定义添加到 VideoTranscriptReviews 命名空间中的 Program 类。 此方法将脚本提交到 **ContentModeratorClient.TextModeration.ScreenText** 方法。 它还将结果转换为 IList\<TranscriptModerationBodyItem >, 并将其提交给**AddVideoTranscriptModerationResult**。

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>发布视频评论

使用 **ContentModeratorClient.Reviews.PublishVideoReview** 发布视频评论。 **PublishVideoReview** 具有以下必需参数：
1. 内容审查器团队名称。
1. **CreateVideoReviews** 返回的视频评论 ID。

将以下方法定义添加到 VideoReviews 命名空间中的 Program 类。

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>汇总

将 **Main** 方法定义添加到 VideoTranscriptReviews 命名空间中的 Program 类。 最后，关闭 Program 类和 VideoTranscriptReviews 命名空间。

> [!NOTE]
> 该程序使用 VTT 格式的示例脚本。 在实际解决方案中，可使用 Azure Media Indexer 服务根据视频[生成脚本](https://docs.microsoft.com/azure/media-services/media-services-index-content)。

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>运行程序并检查输出

运行应用程序时，将显示以下行中的输出：

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>导航到视频脚本评论

转到“评论”>“视频”>“视频”屏幕上内容审查器评论工具中的的视频脚本评论。

你会看到以下功能：
- 你添加的两行脚本
- 由文本审查服务找到并突出显示的不敬词
- 选择脚本文本将从该时间戳开始播放视频

![用于人工审查器的视频脚本评论](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>后续步骤

为适用于 .NET 的此内容审查器快速入门以及其他内容审查器快速入门获取[内容审查器 .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 和 [Visual Studio 解决方案](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)。

了解如何在评论工具中生成[视频评论](video-reviews-quickstart-dotnet.md)。

查看有关如何开发[完整视频审查解决方案](video-transcript-moderation-review-tutorial-dotnet.md)的详细教程。
