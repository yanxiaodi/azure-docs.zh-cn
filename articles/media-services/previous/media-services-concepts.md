---
title: Azure 媒体服务概念 | Microsoft Docs
description: 本主题提供 Azure 媒体服务概念的概述
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 2b28dde812dcce120c951730c27809f7f024e122
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681563"
---
# <a name="azure-media-services-concepts"></a>Azure 媒体服务概念 

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 <br/>查看最新版本：[媒体服务 v3](https://docs.microsoft.com/azure/media-services/latest/)。 此外，请参阅[从 v2 到 v3 迁移指南](../latest/migrate-from-v2-to-v3.md)

本部分概述最重要的媒体服务概念。

## <a name="a-idassetsassets-and-storage"></a><a id="assets"/>资产和存储
### <a name="assets"></a>资产
[资产](https://docs.microsoft.com/rest/api/media/operations/asset)包含数字文件（包括视频、音频、图像、缩略图集合、文本轨道和隐藏式字幕文件）以及这些文件的相关元数据。 数字文件在上传到资产中后，即可用于媒体服务编码和流式处理工作流。

资产将映射到 Azure 存储帐户中的 blob 容器，资产中的文件将作为块 blob 存储在该容器中。 页 blob 不受 Azure 媒体服务支持。

确定要将哪些媒体内容上传和存储到资产中时，需注意以下事项：

* 资产应仅包含一个唯一的媒体内容实例。 例如，一段电视剧、电影或广告剪辑。
* 资产不应包含多版视听文件或多段视听剪辑。 其中一种不当使用资产的示例包括：尝试在资产中存储多段电视剧、广告或某一作品的不同拍摄角度。 在资产中存储多版或多段视听文件会对提交编码作业、流式处理和保障资产后续在工作流中的传送造成困难。  

### <a name="asset-file"></a>资产文件
[AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) 表示 Blob 容器中存储的实际视频或音频文件。 一个资产文件始终与一个资产关联，而一个资产则可能包含一个或多个文件。 如果资产文件对象未与 BLOB 容器中的数字文件关联，则媒体服务 Encoder 任务会失败。

**AssetFile** 实例和实际媒体文件是两个不同的对象。 AssetFile 实例包含有关媒体文件的元数据，而媒体文件包含实际媒体内容。

在不使用媒体服务 API 的情况下，不应该尝试更改媒体服务生成的 BLOB 容器内容。

### <a name="asset-encryption-options"></a>资产加密选项
根据你要上传、存储和传递的内容的不同类型，媒体服务提供了多个加密选项供你选择。

>[!NOTE]
>不使用加密。 这是默认值。 使用此选项时，内容在传送过程中或静态存储过程中都不会受到保护。

如果计划使用渐进式下载交付 MP4，则使用此选项上传内容。

**StorageEncrypted** - 使用此选项可以通过 AES 256 位加密在本地加密明文内容，并将其上传到 Azure 存储中以加密形式静态存储相关内容。 受存储加密保护的资产会在编码前自动解密并放入经过加密的文件系统中，并可选择在重新上传为新的输出资产前重新加密。 存储加密的主要用例是在磁盘上通过静态增强加密来保护高品质的输入媒体文件。 

要传送存储加密资产，必须配置资产的传送策略，以使媒体服务了解要如何传送内容。 在流式传输资产之前，流式处理服务器会删除存储加密，再使用指定的传传送策略（例如 AES、PlayReady 或无加密）流式传输用户的内容。 

**CommonEncryptionProtected** - 如果要采用通用加密或 PlayReady DRM 加密（或上传已加密的）内容（例如，受 PlayReady DRM 保护的平滑流式处理），请使用此选项。

**EnvelopeEncryptionProtected** - 如果要保护（或上传已加密的）采用高级加密标准 (AES) 加密的 HTTP Live Streaming (HLS)，请使用此选项。 如果上传已采用 AES 加密的 HLS，则该 HLS 必须已经由 Transform Manager 加密。

### <a name="access-policy"></a>访问策略
[AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy) 定义对资产的访问权限（如读取、写入和列出）和持续时间。 通常将 AccessPolicy 对象传递给某个定位符，并使用该定位符来访问资产中包含的文件。

>[!NOTE]
>不同 AMS 策略的策略限制为 1,000,000 个（例如，对于定位器策略或 ContentKeyAuthorizationPolicy）。 如果始终使用相同的日期/访问权限，则应使用相同的策略 ID，例如，用于要长期就地保留的定位符的策略（非上传策略）。 有关详细信息，请参阅[此](media-services-dotnet-manage-entities.md#limit-access-policies)主题。

### <a name="blob-container"></a>Blob 容器
一个 Blob 容器包含一组 Blob 集。 Blob 容器用作媒体服务中的访问控制分界点和资产上的共享访问签名 (SAS) 定位符。 一个 Azure 存储帐户可以包含无数个 Blob 容器。 一个容器可以存储无数个 Blob。

>[!NOTE]
> 在不使用媒体服务 API 的情况下，不应该尝试更改媒体服务生成的 BLOB 容器内容。
> 
> 

### <a name="a-idlocatorslocators"></a><a id="locators"/>定位符
[定位符](https://docs.microsoft.com/rest/api/media/operations/locator)提供访问资产中所含文件的入口点。 访问策略用于定义客户端对给定资产具有的访问权限和持续时间。 定位符与访问策略的关系可以为多对一的关系，因此，不同定位符可以向不同客户端提供不同的开始时间和连接类型，而全部使用相同的权限和持续时间设置；但是，由于 Azure 存储服务设置的共享访问策略限制，一项给定的资产一次最多只能与五个唯一的定位符相关联。 

媒体服务支持两种类型的定位符：OnDemandOrigin 定位符，用于对媒体进行流式处理（例如，MPEG DASH、HLS 或平滑流式处理）；渐进式下载媒体和 SAS URL 定位符，用于与 Azure 存储相互上传或下载媒体文件。 

>[!NOTE]
>创建 OnDemandOrigin 定位符时，不应使用列表权限 (AccessPermissions.List)。 

### <a name="storage-account"></a>存储帐户
对 Azure 存储进行的所有访问都要通过存储帐户完成。 一个 Media Service 帐户可与一个或多个存储帐户相关联。 一个帐户可以包含无限个容器，只要每个帐户的容器总大小不超过 500TB 即可。  媒体服务提供 SDK 级工具，可用于管理多个存储帐户，并在上传到这些帐户时基于指标或随机分发使资产分发达到负载均衡。 有关详细信息，请参阅[使用 Azure 存储](https://msdn.microsoft.com/library/azure/dn767951.aspx)。 

## <a name="jobs-and-tasks"></a>作业和任务
[作业](https://docs.microsoft.com/rest/api/media/operations/job)通常用于处理（例如，索引或编码）音频/视频演示。 如果要处理多个视频，应为要编码的每个视频创建一个作业。

作业包含有关要执行的处理的元数据。 每个作业包含一个或多个[任务](https://docs.microsoft.com/rest/api/media/operations/task)，这些任务指定一个原子处理任务、该任务的输入资产和输出资产、一个媒体处理器及其关联的设置。 作业中的各个任务可连接在一起，其中一个任务的输出资产指定为下一任务的输入资产。 因此，一个作业可以包含播放媒体所必需的全部处理过程。

## <a id="encoding"></a>编码
Azure 媒体服务提供了多个用于在云中对媒体进行编码的选项。

一开始使用媒体服务时，了解编解码器与文件格式之间的区别很重要。
编解码器是实现压缩/解压缩算法的软件，而文件格式是用于保存压缩视频的容器。

媒体服务所提供的动态打包，允许以媒体服务支持的流格式（MPEG DASH、HLS、平滑流式处理）传送自适应比特率 MP4 或平滑流式处理编码内容，而无须重新打包成这些流格式。

要利用[动态打包](media-services-dynamic-packaging-overview.md)，需要将夹层（源）文件编码成一组自适应比特率 MP4 文件或自适应比特率平滑流文件，并且至少有一个标准或高级流式处理终结点处于已启动状态。

媒体服务支持本文中介绍的以下按需编码器：

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [媒体编码器高级工作流](media-services-encode-asset.md#media-encoder-premium-workflow)

有关受支持的编码器的信息，请参阅[编码器](media-services-encode-asset.md)。

## <a name="live-streaming"></a>实时流式处理
在 Azure 媒体服务中，通道表示用于处理实时流内容的管道。 通道通过以下两种方式之一接收实时输入流：

* 本地实时编码器将多比特率 RTMP 或平滑流（分片 MP4）发送到通道。 可以使用以下输出多比特率平滑流的实时编码器：MediaExcel、Ateme、Imagine Communications、Envivio、Cisco 和 Elemental。 以下实时编码器输出 RTMP：Adobe Flash Live Encoder、Telestream Wirecast、Teradek、Haivision 和 Tricaster 编码器。 引入的流会直接通过通道，而不会经过任何进一步的转码和编码。 收到请求时，媒体服务会将该流传递给客户。
* 将单比特率流（采用以下某种格式：RTMP 或平滑流式处理（分片 MP4））发送到能够使用媒体服务执行实时编码的通道。 然后，频道将对传入的单比特率流执行实时编码，使之转换为多比特率（自适应）视频流。 收到请求时，媒体服务会将该流传递给客户。

### <a name="channel"></a>通道
在媒体服务中，[频道](https://docs.microsoft.com/rest/api/media/operations/channel)负责处理实时传送视频流内容。 通道提供输入终结点（引入 URL），然后要将该终结点提供给实时转码器。 通道从实时转码器接收实时输入流，并通过一个或多个 StreamingEndpoints 使其可用于流式处理。 通道还提供可用于预览的预览终结点（预览 URL），并在进一步处理和传递流之前对流进行验证。

可以在创建通道时获取引入 URL 和预览 URL。 若要获取这些 URL，通道不一定要处于已启动状态。 准备好开始将数据从实时转码器推送到频道时，频道必须已启动。 实时转码器开始引入数据后，可以预览流。

每个媒体服务帐户均可包含多个通道、多个节目以及多个 StreamingEndpoint。 根据带宽和安全性需求，StreamingEndpoint 服务可专用于一个或多个通道。 任何 StreamingEndpoint 都可以从任何通道拉取。

### <a name="program-event"></a>节目（事件）
[节目（事件）](https://docs.microsoft.com/rest/api/media/operations/program)用于控制实时流中片段的发布和存储。 频道管理节目（事件）。 频道和节目的关系类似于传统媒体，其中频道具有恒定的内容流，而节目的范围限定为该频道上的一些定时事件。
可以通过设置 **ArchiveWindowLength** 属性，指定希望保留多少小时的节目录制内容。 此值的设置范围是最短 5 分钟，最长 25 小时。

ArchiveWindowLength 还决定了客户端能够从当前实时位置按时间向后搜索的最长时间。 超出指定时间长度后，节目也能够运行，但落在时间窗口长度后面的内容将全部被丢弃。 此属性的这个值还决定了客户端清单能够增加多长时间。

每个节目（事件）都与某个资产关联。 若要发布节目，必须为关联的资产创建定位符。 创建此定位符后，可以生成提供给客户端的流 URL。

一个频道最多支持三个同时运行的节目，因此可为同一传入流创建多个存档。 这样，便可以根据需要发布和存档事件的不同部分。 例如，业务要求是存档 6 小时的节目，但只广播过去 10 分钟的内容。 为了实现此目的，需要创建两个同时运行的节目。 一个节目设置为存档 6 小时的事件但不发布该节目。 另一个节目设置为存档 10 分钟的事件，并且要发布该节目。

有关详细信息，请参阅：

* [使用能够使用 Azure 媒体服务执行实时编码的频道](media-services-manage-live-encoder-enabled-channels.md)
* [使用从本地编码器接收多比特率实时流的频道](media-services-live-streaming-with-onprem-encoders.md)
* [配额和限制](media-services-quotas-and-limitations.md)

## <a name="protecting-content"></a>保护内容
### <a name="dynamic-encryption"></a>动态加密
使用 Azure 媒体服务，可以在媒体从离开计算机到存储、处理和传送的整个过程中确保其安全。 借助媒体服务，可以传送使用高级加密标准（AES，使用 128 位加密密钥）和通用加密（CENC，使用 PlayReady 和/或 Widevine DRM）进行动态加密的内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 PlayReady 许可证的服务。

当前可以加密以下流格式：HLS、MPEG DASH 和平滑流式处理。 无法加密渐进式下载。

如果需要媒体服务来加密资产，则需要将加密密钥（CommonEncryption 或 EnvelopeEncryption）与资产相关联，并且配置密钥的授权策略。

如果要流式传输存储加密的资产，必须配置资产的传送策略，以指定要如何传送资产。

播放器请求流时，媒体服务将使用指定的密钥通过信封加密（使用 AES）或通用加密（使用 PlayReady 或 Widevine）来动态加密内容。 为了解密流，播放器将从密钥传送服务请求密钥。 为了确定用户是否被授权获取密钥，服务将评估你为密钥指定的授权策略。

### <a name="token-restriction"></a>令牌限制
内容密钥授权策略可能受到一种或多种授权限制：开放、令牌限制或 IP 限制。 令牌限制策略必须附带由安全令牌服务 (STS) 颁发的令牌。 媒体服务支持采用简单 Web 令牌 (SWT) 格式和 JSON Web 令牌 (JWT) 格式的令牌。 媒体服务不提供安全令牌服务。 可以创建自定义 STS。 必须将 STS 配置为创建令牌，该令牌使用指定密钥以及在令牌限制配置中指定的颁发声明进行签名。 如果令牌有效，而且令牌中的声明与为密钥（或许可证）配置的声明相匹配，则媒体服务密钥传送服务会将请求的密钥（或许可证）返回到客户端。

在配置令牌限制策略时，必须指定主验证密钥、颁发者和受众参数。 主验证密钥包含用来为令牌签名的密钥，颁发者是颁发令牌的安全令牌服务。 受众（有时称为范围）描述该令牌的意图，或者令牌授权访问的资源。 媒体服务密钥传送服务验证令牌中的这些值是否与模板中的值匹配。

有关详细信息，请参阅以下文章：
- [保护内容概述](media-services-content-protection-overview.md)
- [使用 AES-128 进行保护](media-services-protect-with-aes128.md)
- [使用 PlayReady/Widevine 进行保护](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>传送
### <a name="a-iddynamicpackagingdynamic-packaging"></a><a id="dynamic_packaging"/>动态打包
使用媒体服务时，建议始终将夹层文件编码为自适应比特率 MP4 集，然后使用[动态打包](media-services-dynamic-packaging-overview.md)将该集转换为所需格式。

### <a name="streaming-endpoint"></a>流式处理终结点
StreamingEndpoint 表示一个流服务，该服务可以直接将内容传递给客户端播放器应用程序，也可以传递给内容分发网络 (CDN) 以进一步分发（Azure 媒体服务现在还提供了 Azure CDN 集成）。流式处理终结点服务的出站流可以是实时流，也可以是媒体服务帐户中的视频点播资产。 媒体服务客户可以根据自身需要，选择**标准**流式处理终结点或者一个或多个**高级**流式处理终结点。 标准流式处理终结点适合用于大多数流式处理工作负荷。 

标准流式处理终结点适合用于大多数流式处理工作负荷。 标准流式处理终结点可以动态地将内容打包成 HLS、MPEG-DASH 和平滑流式处理，并针对 Microsoft PlayReady、Google Widevine、Apple Fairplay 和 AES128 进行动态加密，从而灵活地将内容传送到几乎所有设备。  它们还从很小的受众扩展到非常大的受众，并通过 Azure CDN 集成提供数千个并发查看器。 如果有高级工作负荷或者流式处理容量要求无法适应标准流式处理终结点吞吐量目标，或者希望控制 StreamingEndpoint 服务的容量，以便处理不断增长的带宽需求，则我们建议分配缩放单元（也称为高级流单元）。

建议使用动态打包和/或动态加密。

>[!NOTE]
>创建 AMS 帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。  若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。  

有关详细信息，请参阅[此](media-services-portal-manage-streaming-endpoints.md)主题。

默认情况下，每个媒体服务帐户最多可以包含 2 个流式处理终结点。 若要请求更高的限制，请参阅[配额和限制](media-services-quotas-and-limitations.md)。

仅当 StreamingEndpoint 处于运行状态时才进行计费。

### <a name="asset-delivery-policy"></a>资产传送策略
媒体服务内容传送工作流中的步骤之一是配置[资产传送策略](https://docs.microsoft.com/rest/api/media/operations/assetdeliverypolicy)，以便对其进行流式传输。 资产传送策略告知媒体服务希望如何传送资产：应该将资产动态打包成哪种流式处理协议（例如 MPEG DASH、HLS、平滑流或全部），是否要动态加密资产以及如何加密（信封或常用加密）。

如果有存储加密的资产，在流式传输资产之前，流式处理服务器会删除存储加密，再使用指定的传送策略流式传输内容。 例如，要传送使用高级加密标准 (AES) 加密密钥加密的资产，请将策略类型设为 DynamicEnvelopeEncryption。 要删除存储加密并以明文的形式流式传输资产，请将策略类型设为 NoDynamicEncryption。

### <a name="progressive-download"></a>渐进式下载
渐进式下载可在下载完整个文件之前开始播放媒体。 只能渐进式下载 MP4 文件。

>[!NOTE]
>如果希望已加密的资产可用于渐进式下载，则必须将这些资产解密。

若要为用户提供渐进式下载 URL，必须先创建一个 OnDemandOrigin 定位符。 创建定位符可以生成资产的基本路径。 然后，需要追加 MP4 文件的名称。 例如：

http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### <a name="streaming-urls"></a>流式处理 URL
将内容流式传输到客户端。 若要为用户提供流式处理 URL，必须先创建一个 OnDemandOrigin 定位符。 创建定位符可提供包含要流式传输的内容的资产的基本路径。 但是，为了能够流式传输该内容，需要进一步修改此路径。 要构造流清单文件的完整 URL，必须将定位符的 Path 值与清单 (filename.ism) 文件名连接起来。 然后，向定位符路径追加 /Manifest 和相应的格式（如果需要）。

也可以通过 SSL 连接流式传输内容。 为此，请确保流 URL 以 HTTPS 开头。 目前，AMS 对自定义域不支持 SSL。  

>[!NOTE]
>仅当要从中传送内容的流式处理终结点是在 2014 年 9 月 10 日之后创建的情况下，才可以通过 SSL 流式传输内容。 如果流式处理 URL 基于 9 月 10 日之后创建的流式处理终结点，则 URL 会包含“streaming.mediaservices.windows.net”（新格式）。 包含“origin.mediaservices.windows.net”（旧格式）的流式处理 URL 不支持 SSL。 如果 URL 采用旧格式，并且希望能够通过 SSL 流式传输内容，请创建新的流式处理终结点。 使用基于新流式处理终结点创建的 URL 通过 SSL 流式传输内容。

以下列表描述了流格式并提供了示例：

* 平滑流

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

* MPEG DASH

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

* Apple HTTP 实时流 (HLS) V4

{流式处理终结点名称-媒体服务帐户名称}.streaming.mediaservices.windows.net/{定位符 ID}/{文件名}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

* Apple HTTP 实时流 (HLS) V3

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

