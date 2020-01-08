---
title: Azure 媒体服务 - HEVC 的平滑流式处理协议 (MS-SSTR) 修正 | Microsoft Docs
description: 本规范描述 Azure 媒体服务中基于 HEVC 分片 MP4 的实时传送视频流的协议和格式。 本规范对平滑流式处理协议文档 (MS-SSTR) 做了修正，包括对 HEVC 引入和流式处理的支持。 本文中仅指定了传送 HEVC 所要进行的更改，“（未更改）”表示文本是复制的，仅用于澄清目的。
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: e0637b2a015a610f9c3f92809f63a442980b63b1
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624806"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>HEVC 的平滑流式处理协议 (MS-SSTR) 修正 

## <a name="1-introduction"></a>1 简介 

本文提供适用于平滑流式处理协议规范 [MS-SSTR]（实现 HEVC 编码视频的平滑流式处理）的详细修正。 本规范只阐述了交付 HEVC 视频编解码器所要进行的更改。 本文遵循 [MS-SSTR] 规范所用的相同编号架构。 整篇文章中显示的空白标题行用于将读者定位到 [MS-SSTR] 规范中的相应位置。  “（未更改）”表示文本是复制的，仅用于澄清目的。

本文针对平滑流式处理清单中的 HEVC 视频编解码器 (使用 "hev1" 或 "hvc1" 格式跟踪) 提供技术实现要求, 并更新规范引用以引用当前 MPEG 标准包括 HEVC、HEVC 的通用加密, 以及 ISO 基本媒体文件格式的 box 名称已更新, 以与最新的规范保持一致。 

参考的平滑流式处理协议规范 [MS-SSTR] 描述了通过以下方式传送实时和点播数字媒体（例如音频和视频）所用的在线格式：从编码器传送到 Web 服务器、从一台服务器传送到另一个服务器，以及从服务器传送到 HTTP 客户端。
通过 HTTP 使用基于 MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) 的数据结构传送方法能够在不同的压缩媒体内容质量级别之间近乎实时地无缝切换。 因此，即使客户端计算机或设备的网络和视频呈现条件发生变化，也能为 HTTP 客户端最终用户带来一致的播放体验。

## <a name="11-glossary"></a>1.1 术语表 

*[MS-GLOS]* 中定义了以下术语：

>   **全局唯一标识符 (GUID)、通用唯一标识符 (UUID)**

本文档专门使用了以下术语：

>  **构图时间：** 按[[ISO/IEC-iec-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)中定义的方式在客户端上显示示例的时间。
> 
>   **CENC**：通用加密，符合 [ISO/IEC 23001-7] 第二版中的定义。
> 
>   **解码时间：** 根据[[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695)中的定义, 需要在客户端上对示例进行解码的时间。

**片段：** 一个可单独下载的**媒体**单元，由一个或多个**样本**构成。

>   **HEVC：** 高效视频编码，符合 [ISO/IEC 23008-2] 中的定义
> 
>   **清单：** 有关**呈现内容**的元数据，可让客户端发出**媒体**请求。 **媒体：** 由客户端用来播放**呈现内容**的压缩音频、视频和文本数据。 **媒体格式：** 以压缩**样本**形式呈现音频或视频时所用的妥善定义的格式。
> 
>   **呈现内容：** 播放单部电影所需的所有**流**和相关元数据的集。 **请求：** 在[[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372) **响应**中定义的从客户端发送到服务器的 HTTP 消息:从服务器发送到客户端的 HTTP 消息, 如[[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)中所定义
> 
>   **样本：** 存储和处理**媒体**的最小基本单位（例如帧）。
> 
>   **可以、应该、必须、不应、不得：** 如 [RFC2119] 中所述, 按[[]](https://go.microsoft.com/fwlink/?LinkId=90317)中所述使用了这些术语: "可能"、"应" 或 "不应"。

## <a name="12-references"></a>1.2 参考

>   对 Microsoft 开放规范文档的参考不包括发布年份，因为链接指向最新版本的文档，而这些文档经常更新。 对其他文档的参考包括发布年份（如果已提供）。

### <a name="121-normative-references"></a>1.2.1 规范参考 

>  [MS SSTR] 平滑流式处理协议 *v20140502* [https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] 国际标准化组织编写的“信息技术 -- 音频-视频对象编码 -- 第 12 部分：ISO 基本媒体文件格式”，ISO/IEC 14496-12:2014 版本 4，以及勘误 1、修正 1 和 2。
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] 国际标准化组织编写的“信息技术 -- 音频-视频对象编码 -- 第 15 部分：以 ISO 基本媒体文件格式传送 NAL 单位结构化视频”，ISO 14496-15:2015 版本 3。
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] 信息技术 -- 异构环境中的高效编码和媒体传送 -- 第 2 部分：高效视频编码：2013 或最新版本<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] 信息技术 -- MPEG 系统技术 -- 第 7 部分：ISO 基本媒体格式文件中的通用加密，CENC 版本 2:2015<https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381 中阐述的“‘存储桶’媒体类型的‘编解码器’和‘配置文件’参数”<https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4]MP4REG 注册机构, ""[http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   RFC2119Bradner, S. "在 Rfc 中用于指示要求级别的关键字", BCP 14, RFC 2119, 三月1997[https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 信息性参考 

>   [MS-GLOS] Microsoft Corporation 编写的“*Windows 协议主要术语表*”。
> 
>   RFC3548Josefsson、Base16、Base32 和 Base64 数据编码, RFC 3548, 2003 年7月[https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed. 和 Overell, P. 编写的“扩充的 BNF 语法规范：ABNF ", STD 68, RFC 5234, 年1月2008[https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>1.3 概述 

>   下面指定了传送 HEVC 之前需要对平滑流式处理规范所做的唯一几处更改。 此处列出的小节标题未发生变化，目的是保留所参考的平滑流式处理规范 [MS-SSTR] 中的相应位置。

## <a name="14-relationship-to-other-protocols"></a>1.4 与其他协议之间的关系 

## <a name="15-prerequisitespreconditions"></a>1.5 先决条件/前提条件 

## <a name="16-applicability-statement"></a>1.6 适用性声明 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 版本控制和功能协商 

## <a name="18-vendor-extensible-fields"></a>1.8 供应商可扩展的字段 

>   应使用以下方法来识别采用 HEVC 视频格式的流：
> 
>   * **媒体格式的自定义描述性代码：** 此功能根据第 *2.2.2.5* 节中的指定，由 **FourCC** 字段提供。
>   实现者可以通过在[[ISO/IEC-iec-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)中指定的方式, 使用 MPEG4-RA 注册扩展代码, 以确保扩展不会冲突。

## <a name="19-standards-assignments"></a>1.9 标准分配 

## <a name="2-messages"></a>2 消息 

## <a name="21-transport"></a>2.1 传输

## <a name="22-message-syntax"></a>2.2 消息语法 

### <a name="221-manifest-request"></a>2.2.1 清单请求 

### <a name="222-manifest-response"></a>2.2.2 清单响应 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion（变量）：** 清单响应消息的次要版本。 必须设置为 2。 （未更改）
> 
>   **TimeScale（变量）：** Duration 属性的时标，指定为一秒的增量数。 默认值为
> 1. （未更改）
> 
>    建议值为 90000, 表示包含以帧为帧的小数值视频的视频帧和片段的确切持续时间 (例如, 30/1.001 Hz)。

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

将通用加密 (CENC) 应用到视频或音频流后，应显示 ProtectionElement。 HEVC 加密流应符合通用加密第 2 版 [ISO/IEC 23001-7]。 只应加密 VCL NAL 单元中的切片数据。

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale（变量）：** 此流中的持续时间和时间值的时标，指定为一秒的增量数。 对于 HEVC 流，建议指定值 90000。 对于音频流，建议指定与波形样本频率匹配的值（例如 48000 或 44100）。

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPattern 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC（变量）：** 由四个字符组成的代码，用于确定对每个样本使用了哪种媒体格式。 以下值范围是保留的，其语义含义如下：
> 
> * "hev1”：此轨迹的视频样本使用 HEVC 视频，并采用 [ISO/IEC-14496-15] 中指定的“hev1”样本说明格式。
>
> * "hvc1":此曲目的视频示例使用 HEVC 视频, 使用 [ISO/IEC-iec-14496-15] 中指定的 "hvc1" 示例说明格式。
> 
>   **CodecPrivateData（变量）：** 指定特定于媒体格式的参数，并在轨迹的所有样本中通用的数据，以十六进制编码字节的字符串表示。 字节序列的格式和语义含义根据 **FourCC** 字段的值而异，如下所述：
> 
>   * 当 TrackElement 描述 HEVC 视频时, **FourCC**字段应等于 **"hev1"** 或 **"hvc1"**
> 
>   **CodecPrivateData**字段应包含以下字节序列的十六进制编码的字符串表示形式, 在 ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096)中指定: (无从 MS-SSTR 中更改)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField 包含序列参数集 (SPS)。
> 
>   * PPSField 包含切片参数集 (PPS)。
> 
>   注意:视频参数集 (VPS) 未包含在 CodecPrivateData 中，但应包含在“hvcC”框中存储的文件的文件标头中。 使用平滑流式处理协议的系统必须使用自定义属性“codecs”来告知附加的解码参数（例如 HEVC 层）。

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   **SmoothStreamingMedia 的 MajorVersion** 字段必须设置为 2，**MinorVersion** 字段必须设置为 2。 （未更改）

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 片段请求 

>   **说明**：为**MinorVersion** 2 和 "hev1" 或 "hvc1" 请求的默认媒体格式为 "iso8" 品牌 ISO 基本媒体文件格式, 其中指定为 [ISO/iec 14496-12] Iso 基本媒体文件格式第四版, [ISO/iec 23001-7] 通用加密第二版。

### <a name="224-fragment-response"></a>2.2.4 片段响应 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   **TfxdBox** 已弃用，其功能已被 [ISO/IEC 14496-12] 第 8.8.12 节中指定的轨迹片段解码时间框（“tfdt”）取代。
> 
>   **说明**：客户端可以通过将轨迹运行框（“trun”）中列出的样本持续时间相加，或者将样本数量乘以默认样本持续时间，来计算片段的持续时间。 将“tfdt”中的 baseMediaDecodeTime 加上片段持续时间等于下一个片段的 URL 时间参数。
> 
>   应该根据 [ISO/IEC 14496-12] 第 8.16.5 节中的指定，视需要在电影片段框（“moof”）的前面插入生成器引用时间框（“prft”），表示电影片段框引用的第一个样本的轨迹片段解码时间对应的 UTC 时间。

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   **TfrfBox** 已弃用，其功能已被 [ISO/IEC 14496-12] 第 8.8.12 节中指定的轨迹片段解码时间框（“tfdt”）取代。
> 
>   **说明**：客户端可以通过将轨迹运行框（“trun”）中列出的样本持续时间相加，或者将样本数量乘以默认样本持续时间，来计算片段的持续时间。 将“tfdt”中的 baseMediaDecodeTime 加上片段持续时间等于下一个片段的 URL 时间参数。 预提地址已被弃用，因为它们会延迟实时传送视频流。

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   **TfhdBox** 和相关字段根据片段中的样本元数据封装默认值。 **TfhdBox** 字段的语法是 [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) 第 8.8.7 节中定义的轨迹片段标头框的语法的严格子集。
> 
>   **BaseDataOffset（8 字节）：** 从 **MdatBox** 字段开始位置到 **MdatBox** 字段中样本字段的偏移量，以字节表示。 若要告知此限制，必须设置 default-base-is-moof 标志 (0x020000)。

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   **TrunBox** 和相关字段根据请求片段的样本元数据封装。 **TrunBox** 的语法是 [[ISO/IEC 14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* 第 8.8.8 节中定义的版本 1 轨迹片段运行框的严格子集。
> 
>   **SampleCompositionTimeOffset（4 字节）：** 每个样本的样本构图时间偏移量经过调整，使片段中第一个呈现的样本的呈现时间等于第一个解码样本的解码时间。 应该根据
> 
>   [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) 中的定义，使用负值视频样本构图偏移量。
> 
>   注意:这可以避免视频滞后音频等于最大解码图片缓冲消除延迟所造成的视频同步错误，并保持可能具有不同消除延迟的备用片段之间的呈现计时。
> 
>   本节中定义的字段语法（符合 ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) 中的指定）保持不变，但以下各项例外：
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 片段响应通用字段 

### <a name="225-sparse-stream-pointer"></a>2.2.5 稀疏流指针 

### <a name="226-fragment-not-yet-available"></a>2.2.6 片段目前不可用 

### <a name="227-live-ingest"></a>2.2.7 实时引入 

#### <a name="2271-filetype"></a>2.2.7.1 FileType 

>   **FileType（变量）：** 指定 MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) 文件的子类型和目标用途，以及高级属性。
> 
>   **MajorBrand（变量）：** 媒体文件的主要品牌。 必须设置为“isml”。
> 
>   **MinorVersion（变量）：** 媒体文件的次要版本。 必须设置为 1。
> 
>   **CompatibleBrands（变量）：** 指定支持的 MPEG-4 品牌。
>   必须包含“ccff”和“iso8”。
> 
>   本节中定义的字段语法（符合 ABNF [[RFC5234]](https://go.microsoft.com/fwlink/?LinkId=123096) 中的指定）如下：

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**说明**：兼容性品牌“ccff”和“iso8”指示片段符合“通用容器文件格式”、通用加密 [ISO/IEC 23001-7] 和 ISO 基本媒体文件格式版本 4 [ISO/IEC 14496-12]。

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 轨迹片段扩展标头 

### <a name="228-server-to-server-ingest"></a>2.2.8 服务器到服务器的引入 

## <a name="3-protocol-details"></a>3 协议详细信息 


## <a name="31-client-details"></a>3.1 客户端详细信息 

### <a name="311-abstract-data-model"></a>3.1.1 抽象化数据模型 

#### <a name="3111-presentation-description"></a>3.1.1.1 呈现内容说明 

>   “呈现说明”数据元素封装呈现内容的所有元数据。
> 
>   呈现元数据：呈现内容中所有流通用的一组元数据。 呈现内容元数据包括第 *2.2.2.1* 节中指定的以下字段：
> 
> * **MajorVersion**
> * **MinorVersion**
> * **TimeScale**
> * **持续时间**
> * **IsLive**
> * **LookaheadCount**
> * **DVRWindowLength**
> 
>   包含 HEVC 流的呈现内容应该设置：

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0（注意：框已弃用）
> 
>   呈现内容还应设置：

    TimeScale = 90000

>   流集合：根据第 *3.1.1.1.2* 节指定的“流说明”数据元素集合。
> 
>   保护说明：根据第 *3.1.1.1.1* 节指定的“保护系统元数据说明”数据元素集合。

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 保护系统元数据说明 

>   “保护系统元数据说明”数据元素封装特定于单个内容保护系统的元数据。 （未更改）
> 
>   保护标头说明：与单个内容保护系统相关的内容保护元数据。 保护标头说明包括第 *2.2.2.2* 节中指定的以下字段：
> 
>   * **SystemID**
>   * **ProtectionHeaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 流说明 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 轨迹说明 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 自定义属性说明 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 片段引用说明 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 轨迹特定的片段引用说明 

#### <a name="3112-fragment-description"></a>3.1.1.2 片段说明 

##### <a name="31121-sample-description"></a>3.1.1.2.1 样本说明 

### <a name="312-timers"></a>3.1.2 计时器 

### <a name="313-initialization"></a>3.1.3 初始化 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 较高层触发的事件 

#### <a name="3141-open-presentation"></a>3.1.4.1 开放式呈现内容 

#### <a name="3142-get-fragment"></a>3.1.4.2 获取片段 

#### <a name="3143-close-presentation"></a>3.1.4.3 闭合式呈现内容 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 事件处理和规则定序 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 清单请求和清单响应 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 片段请求和片段响应

## <a name="32-server-details"></a>3.2 服务器详细信息

## <a name="33-live-encoder-details"></a>3.3 实时编码器详细信息 

## <a name="4-protocol-examples"></a>4 协议示例 

## <a name="5-security"></a>5 安全性 

## <a name="51-security-considerations-for-implementers"></a>5.1 实现者的安全注意事项

>   如果使用此协议传输的内容具有较高的商业价值，则应使用内容保护系统来防止未经授权使用内容。 可以使用 **ProtectionElement** 来承载与内容保护系统的用法相关的元数据。 应该根据 MPEG 通用加密第二版：2015 [ISO/IEC 23001-7] 中的指定加密受保护的音频和视频内容。
> 
>   **说明**：对于 HEVC 视频，只会加密 VCL NAL 中的切片数据。 在解密之前，呈现应用程序可以访问切片标头和其他 NAL。 呈现应用程序无法使用安全视频路径的加密信息。

## <a name="52-index-of-security-parameters"></a>5.2 安全参数的索引 


| **安全参数**  | **节**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| 通用加密框 | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 通用加密框

如果已应用通用加密并且在 [ISO/IEC 23001-7] 或 [ISO/IEC 14496-12] 中指定了片段响应，则可以在片段响应中呈现以下框：

1.  保护系统特定的标头框（“pssh”）

2.  样本加密框（“senc”）

3.  样本辅助信息偏移量框（“saio”）

4.  样本辅助信息大小框（“saiz”）

5.  样本组说明框（“sgpd”）

6.  要分组的样本框（“sbgp”）

---

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
