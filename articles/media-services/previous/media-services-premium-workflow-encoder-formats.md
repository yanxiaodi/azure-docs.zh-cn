---
title: 媒体编码器高级工作流格式和编解码器 | Microsoft Docs
description: 本主题概述媒体编码器高级工作流格式和编解码器
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 9ec8e2df8befa28fc023edb30889f177b60939f9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "69015044"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>媒体编码器高级工作流格式和编解码器

> [!NOTE]
> 有关高级编码器的问题，请发送电子邮件到 mepd@microsoft.com。
> 
> 本主题中所述的媒体编码器高级工作流媒体处理器在中国不可用。 

本文档包含受**媒体编码器高级工作流**公开预览版编码器支持的输入和输出文件格式及编解码器列表。

[Media Encoder Premium Workflow 输入格式和编解码器](#input_formats)

Media Encoder Premium Workflow 输出格式和编解码器

**媒体编码器高级工作流**支持[此](#closed_captioning)部分中所述的隐藏字幕。 

## <a id="input_formats"></a>媒体编码器高级工作流输入格式和编解码器

以下部分列出了此媒体处理器支持的作为输入的编解码器和文件格式。

### <a name="input-containerfile-formats"></a>输入容器/文件格式

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Mpeg-2 传输流
* MPEG-2 节目流
* MPEG-4/MP4
* Windows Media/ASF
* AVI（8 位/10 位未压缩）

### <a name="input-video-codecs"></a>输入视频编解码器

* AVC 8 位/10 位，最高支持 4:2:2，包括 AVCIntra
* Avid DNxHD（MXF 格式）
* DVCPro/DVCProHD（MXF 格式）
* HEVC/H.265、Main 和 Main 10 Profile
* JPEG2000
* Mpeg-2（最高支持 422 Profile 和 High Level；包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs® 和 D10 等变体）
* MPEG-1
* Windows Media 视频/VC-1

### <a name="input-audio-codecs"></a>输入音频编解码器

* AES（SMPTE 331M 和 302M、AES3-2003）
* Dolby® E
* Dolby® Digital (AC3)
* AAC(AAC-LC、AAC-HE 和 AAC-HEv2；最高支持 5.1）
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media 音频
* WAV/PCM

## <a id="output_format"></a>媒体编码器高级工作流输出格式和编解码器

以下部分列出了支持作为此媒体处理器输入的编解码器和文件格式。

### <a name="output-containerfile-formats"></a>输出容器/文件格式

* Adobe® Flash® F4V
* MXF（OP1a、XDCAM 和 AS02）
* DPP（包括 AS11）
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI（8 位/10 位未压缩）
* 平滑流文件格式 (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>输出视频编解码器

* AVC（H.264；8 位；最高支持 High Profile、Level 5.2；4K Ultra HD；AVC Intra）
* Avid DNxHD（MXF 格式）
* DVCPro/DVCProHD（MXF 格式）
* Mpeg-2（最高支持 422 Profile 和 High Level；包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs® 和 D10 等变体）
* MPEG-1
* Windows Media 视频/VC-1
* JPEG 缩图创建
* HEVC（H.265；8 位和 10 位，Main 和 Main 10 Profile）

  在某些情况下可获得对 HDR 10 的支持，有关详细信息，请联系 mepd@microsoft.com


### <a name="output-audio-codecs"></a>输出音频编解码器

* AES（SMPTE 331M 和 302M、AES3-2003）
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3)，最高支持 7.1
* AAC(AAC-LC、AAC-HE 和 AAC-HEv2；最高支持 5.1）
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Windows Media 音频

>[!NOTE]
>如果编码为 Dolby® Digital (AC3)，则只能将输出写入到 ISO MP4 文件。

## <a id="closed_captioning"></a>支持隐藏式字幕

引入时，**媒体编码器高级工作流**支持：

1. SCC 文件
2. SMPTE-TT 文件
3. CEA-608/CEA-708 – 作为用户数据（H.264 基本流的 SEI 消息，ATSC/53，SCTE20）或作为 MXF 文件中的辅助数据携载
4. STL 字幕文件

输出时，提供以下选项：

1. CEA-608 到 CEA-708 的转换
2. CEA-608/CEA-708 传递（内嵌在 H.264 基本流的 SEI 消息中，或作为 MXF 文件中的辅助数据携载）
3. SCC
4. SMPTE 时序文本（来自符合 SMPTE RP2052 的源 CEA-608；包括 DFXP 文件创建）
5. SRT 字幕文件
6. DVB 字幕流

> [!NOTE]
> 不一定支持通过 Azure 媒体服务中的流式传输来传送上述所有输出格式。

## <a name="known-issues"></a>已知问题

如果输入视频不包含隐藏式字幕，输出资产仍将包含一个空的 TTML 文件。 

## <a name="media-services-learning-paths"></a>媒体服务学习路径

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

