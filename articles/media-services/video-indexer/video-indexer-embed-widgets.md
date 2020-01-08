---
title: 在应用程序中嵌入视频索引器小组件
titlesuffix: Azure Media Services
description: 了解如何在应用程序中嵌入视频索引器小组件。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: fc0b447630b5e1ac360b1d84869cea02186672fc
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036623"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>在应用程序中嵌入视频索引器小组件

本文介绍如何在应用程序中嵌入视频索引器小组件。 视频索引器支持在应用程序中嵌入三种类型的小组件：*认知见解*、*播放器*和*编辑器*。 

从版本2开始，小组件基 URL 包含指定帐户的区域。 例如，美国西部区域中的帐户将生成：`https://wus2.videoindexer.ai/embed/insights/...`。

## <a name="widget-types"></a>小组件类型

### <a name="cognitive-insights-widget"></a>认知见解小组件

认知见解小组件包括从视频索引过程中提取的所有视觉对象。 认知见解小组件支持以下可选 URL 参数。

|姓名|定义|描述|
|---|---|---|
|`widgets`|用逗号分隔的字符串|允许您控制要呈现的见解。 <br/> 示例： `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`仅呈现人员和品牌 UI 见解。<br/>可用选项：people、keywords、annotations、brands、sentiments、transcript、search。<br/>请注意， `widgets`在版本2中不支持 URL 参数。<br/>|
|`locale`|短语言代码|控制 insights 语言。 默认值为 `en`。 <br/> 例如：`locale=de`。|
|`tab`|默认选定的选项卡|控制默认呈现的 "**见解**" 选项卡。 <br/> 示例： `tab=timeline`在选择 "**时间线**" 选项卡的情况上呈现见解。|

### <a name="player-widget"></a>播放器小组件

可使用播放机小组件通过自适应比特率流式传输视频。 播放机小组件支持以下可选 URL 参数。

|姓名|定义|描述|
|---|---|---|
|`t`|开始时间（秒）|使播放机从指定的时间点开始播放。<br/> 例如：`t=60`。|
|`captions`|语言代码|在加载小组件时提取指定语言的标题，在 "**标题**" 菜单中可用。<br/> 例如：`captions=en-US`。|
|`showCaptions`|布尔值|使播放器与已启用的字幕一起加载。<br/> 例如：`showCaptions=true`。|
|`type`||激活音频播放器外观（视频部分被删除）。<br/> 例如：`type=audio`。|
|`autoplay`|布尔值|指示播放机是否应在加载时开始播放视频。 默认值为 `true`。<br/> 例如：`autoplay=false`。|
|`language`|语言代码|控制播放器语言。 默认值为 `en-US`。<br/>例如：`language=de-DE`。|

### <a name="editor-widget"></a>编辑器小组件

您可以使用编辑器小组件来创建新项目并管理视频见解。 编辑器小组件支持以下可选 URL 参数。

|姓名|定义|描述|
|---|---|---|
|`accessToken`<sup>*</sup>|String|提供对仅用于嵌入小组件的帐户的视频的访问。<br> 编辑器小组件要求`accessToken`参数。|
|`language`|语言代码|控制播放器语言。 默认值为 `en-US`。<br/>例如：`language=de-DE`。|
|`locale`|短语言代码|控制 insights 语言。 默认值为 `en`。<br/>例如：`language=de`。|

<sup>*</sup>所有者应小心提供`accessToken` 。

## <a name="embedding-public-content"></a>嵌入公共内容

1. 登录到[视频索引器](https://www.videoindexer.ai/)网站。
2. 选择要使用的视频。
3. 选择视频下显示的 "**嵌入**" 按钮。

    ![小组件](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    选择 "**嵌入**" 按钮后，可以选择要在应用程序中嵌入的小组件。 
4. 选择所需的小组件类型（**认知见解**、**播放器**或**编辑**）。
 
5. 复制嵌入代码，然后将其添加到你的应用程序。 

    ![小组件](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> 如果在共享视频 url 时遇到问题，请将`location`参数添加到链接中。 应将参数设置为[视频索引器所在的 Azure 区域](regions.md)。 例如：`https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`。

## <a name="embedding-private-content"></a>嵌入专用内容

若要嵌入专用视频，必须在 iframe 的**src**属性中传递访问令牌：

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
若要获取认知见解小组件内容，请使用以下项之一：<br/>
- [获取 Insights 小组件](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)API。<br/>
- [获取视频访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?)。 将其作为查询参数添加到 URL。 指定此 URL 作为 iframe 的**src**值，如前面所示。

若要在嵌入的小组件中提供编辑见解功能，必须传递包含编辑权限的访问令牌。 使用 "[获取 Insights 小组件](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)" 或 " `&allowEdit=true`[获取视频访问令牌](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Video-Access-Token?)"。 

## <a name="widgets-interaction"></a>小组件交互

认知见解小组件可以与应用程序上的视频交互。 本部分说明如何实现此交互。

![小组件](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>跨域通信

若要获取视频索引器小组件来与其他组件通信，视频索引器服务如下：

- 使用跨域通信 HTML5 方法**postMessage**。 
- 跨 VideoIndexer.ai 域验证消息。 

如果实现自己的播放器代码，并将其与认知见解小组件集成，则需负责验证来自 VideoIndexer.ai 的消息源。

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>在应用程序或博客中嵌入小组件（推荐） 

本部分介绍如何实现两个视频索引器小组件之间的交互，以便当用户在应用程序上选择见解控件时，播放机会跳到相关时刻。

1. 复制播放机小组件嵌入代码。
2. 复制认知见解嵌入代码。
3. 添加[转存进程文件](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)以处理两个小组件之间的通信：<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

现在，当用户在应用程序上选择见解控件时，播放机会跳到相关时刻。

有关详细信息，请参阅[视频索引器-嵌入这两个小组件演示](https://codepen.io/videoindexer/pen/NzJeOb)。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>嵌入认知见解小组件并使用 Azure Media Player 来播放内容

本部分演示如何使用[AMP 插件](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)实现认知见解小组件与 Azure Media Player 实例之间的交互。
 
1. 为 AMP 播放器添加视频索引器插件：<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. 用视频索引器插件实例化 Azure Media Player。

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance.
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. 复制认知见解嵌入代码。

你现在应该能够与 Azure Media Player 通信。

有关详细信息，请参阅[Azure Media Player + VI Insights 演示](https://codepen.io/videoindexer/pen/rYONrO)。

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>嵌入视频索引器认知见解小组件并使用不同的视频播放器

如果使用 Azure Media Player 以外的视频播放器，则必须手动操作视频播放器才能实现通信。 

1. 插入视频播放器。

    例如，标准 HTML5 播放器：

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. 嵌入认知见解小组件。
3. 通过侦听“消息”事件实现播放器的通信。 例如：

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

有关详细信息，请参阅[Azure Media Player + VI Insights 演示](https://codepen.io/videoindexer/pen/YEyPLd)。

## <a name="adding-subtitles"></a>添加字幕

如果使用自己的[Azure Media Player](https://aka.ms/azuremediaplayer)嵌入视频索引器见解，则可以使用**GetVttUrl**方法来获取隐藏式字幕（副标题）。 还可以从视频索引器 AMP 插件**getSubtitlesUrl**调用 JavaScript 方法（如前文所述）。 

## <a name="customizing-embeddable-widgets"></a>自定义可嵌入式小组件

### <a name="cognitive-insights-widget"></a>认知见解小组件

您可以选择所需的见解类型。 为此，请将它们指定为以下 URL 参数的值，该参数将添加到从 API 或 web 应用程序中获取的嵌入代码： `&widgets=<list of wanted widgets>`。

可能的值包括：**人员**、**关键字**、**情绪**、**脚本**和**搜索**。

例如，如果你想嵌入只包含人员和搜索见解的小组件，则 iframe 嵌入 URL 将如下所示：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

iframe 窗口的标题也可自定义，只需为 iframe URL 提供 `&title=<YourTitle>` 即可。 （它自定义 HTML \<标题 > 值）。
    
例如，如果需要为 iframe 窗口提供标题“MyInsights”，则 URL 将如下所示：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

请注意，仅当你需要在新窗口中打开见解时，此选项才适用。

### <a name="player-widget"></a>播放器小组件

如果嵌入视频索引器播放器，则可通过指定 iframe 的大小来选择播放器的大小。

例如：

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

默认情况下，视频索引器播放器具有自动生成的隐藏式字幕，它们基于视频记录。 此脚本是从视频中提取的，其中包含上传视频时选择的源语言。

如果要使用其他语言嵌入，可以将添加`&captions=< Language | ”all” | “false” >`到嵌入播放机 URL。 如果需要所有可用语言标题中的标题，请使用值`all`。 如果需要默认显示字幕，则可传递 `&showCaptions=true`。

然后，嵌入 URL 将如下所示： 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

如果要禁用标题，可以将`captions`参数值作为`false`传递。

#### <a name="autoplay"></a>功能
默认情况下，播放机将开始播放视频。 您可以选择不传递`&autoplay=false`到前面的嵌入 URL。

## <a name="next-steps"></a>后续步骤

有关如何查看和编辑视频索引器的信息，请参阅[查看和编辑视频索引器见解](video-indexer-view-edit.md)。

此外，请查看[视频索引器 CodePen](https://codepen.io/videoindexer/pen/eGxebZ)。
