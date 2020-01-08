---
title: 配置 FMLE 编码器以发送单比特率实时流 | Microsoft Docs
description: 本主题说明如何配置 Flash Media Live Encoder (FMLE) 编码器，以便将单比特率流发送到 AMS 频道进行实时编码。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 09d9bdffefe9204e9f58b8f07af5b21228269f6c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016750"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>使用 FMLE 编码器发送单比特率实时流 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

本文说明如何配置 [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) (FMLE) 编码器，以便将单比特率流发送到 AMS 频道进行实时编码。 有关详细信息，请参阅 [使用能够通过 Azure 媒体服务执行实时编码的频道](media-services-manage-live-encoder-enabled-channels.md)。

本教程演示了如何通过 Azure 媒体服务浏览器 (AMSE) 工具管理 Azure 媒体服务 (AMS)。 此工具仅在 Windows 电脑上运行。 如果使用的是 Mac 或 Linux，则可使用 Azure 门户创建[频道](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel)和[节目](media-services-portal-creating-live-encoder-enabled-channel.md)。

本教程介绍如何使用 AAC。 但在默认情况下，FMLE 不支持 AAC。 需要购买一个进行 AAC 编码用的插件，例如由 MainConcept 提供的：[AAC 插件](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

## <a name="prerequisites"></a>先决条件
* [创建 Azure 媒体服务帐户](media-services-portal-create-account.md)
* 确保流式处理终结点正在运行。 有关详细信息，请参阅[在媒体服务帐户中管理流式处理终结点](media-services-portal-manage-streaming-endpoints.md)
* 安装最新版本的 [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) 工具。
* 启动该工具并连接到 AMS 帐户。

## <a name="tips"></a>提示
* 尽可能使用硬编码的 Internet 连接。
* 在确定带宽要求时，可以认为它就是将流式处理比特率翻倍。 虽然此要求不是强制性要求，但它可以减轻网络拥塞的影响。
* 使用基于软件的编码器时，请关闭任何不需要的程序。

## <a name="create-a-channel"></a>创建频道
1. 在 AMSE 工具中，导航到“实时”选项卡，并右键单击频道区域。 从菜单中选择“创建频道…” 从菜单中。

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. 指定频道名称，说明字段为可选字段。 在“频道设置”下针对“实时编码”选项选择“标准”，将“输入协议”设置为“RTMP”。 所有其他设置可保留原样。

    确保选中“立即启动新频道”。

3. 单击“创建频道”。

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> 启动频道可能需要长达 20 分钟的时间。
>
>

启动频道时，可以[配置编码器](media-services-configure-fmle-live-encoder.md)。

> [!IMPORTANT]
> 请注意，只要频道进入就绪状态，就会开始计费。 有关详细信息，请参阅[频道的状态](media-services-manage-live-encoder-enabled-channels.md#states)。
>
>

## <a id=configure_fmle_rtmp></a>配置 FMLE 编码器
在本教程中，将使用以下输出设置。 本部分的其余内容介绍更详细的配置步骤。

**视频**：

* 编解码器：H.264
* 配置文件：高（级别 4.0）
* 比特率：5000 kbps
* 关键帧：2 秒（60 秒）
* 帧速率：30

**音频**：

* 编解码器：AAC (LC)
* 比特率：192 kbps
* 采样率：44.1 kHz

### <a name="configuration-steps"></a>配置步骤
1. 在所使用的计算机上导航到 Flash Media Live Encoder (FMLE) 的界面。

    该界面是进行设置的一个主页面。 在开始使用 FMLE 进行流式处理时，请记下以下建议的设置。

   * 格式:H.264 帧速率：30.00
   * 输入大小：1280 x 720
   * 比特率：5000 Kbps（可根据网络限制进行调整）  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     当使用隔行扫描的源时，请选中“取消隔行扫描”选项
2. 选择“格式”旁边的扳手图标，那些额外的设置应该如下所示：

   * 配置文件：主要
   * 级别：4.0
   * 关键帧频率：2 秒

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. 设置以下重要的音频设置：

   * 格式:AAC
   * 采样率：44100 Hz
   * 比特率：192 Kbps

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. 获取频道的输入 URL，以便将其分配给 FMLE 的“RTMP 终结点”。

    导航回 AMSE 工具，查看频道完成状态。 一旦状态从“正在启动”变为“正在运行”，即可获取输入 URL。

    频道正在运行时，右键单击频道名称，向下导航，将鼠标悬停在“将输入 URL 复制到剪贴板”上方，然后选择“主要输入 URL”。  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. 将此信息粘贴到输出部分的“FMS URL” 字段，并指定一个流名称。

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    若要实现额外的冗余，可对“辅助输入 URL”重复这些步骤。
6. 选择“连接”。

> [!IMPORTANT]
> 在单击“连接”之前，**必须**确保频道已就绪。
> 另外，请确保不要让频道在没有一个输入/贡献源的情况下处于就绪状态的时间超出 15 分钟。
>
>

## <a name="test-playback"></a>测试播放

导航回 AMSE 工具，并右键单击要测试的频道。 在菜单中，将鼠标悬停在“播放预览”上方，然后选择“使用 Azure 媒体播放器”。  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

如果流出现在播放器中，则编码器已正确配置，可以连接到 AMS。

如果收到错误，则需重置频道并调整编码器设置。 有关指南，请参阅[故障排除](media-services-troubleshooting-live-streaming.md)一文。  

## <a name="create-a-program"></a>创建节目
1. 一旦确认频道可以播放，则可创建节目。 在 AMSE 工具的“实时”选项卡下，右键单击节目区域，并选择“创建新节目”。  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. 为节目命名，并根据需要调整“存档时段长度”（默认为 4 小时）。 还可以指定存储位置，也可以将其保留为默认值。  
3. 选中“立即启动节目”框。
4. 单击“创建节目”。  

    >[!NOTE]
    >创建节目需要的时间比创建频道需要的时间少。
        
5. 运行节目以后，可通过下述方式来确认其是否能够播放：右键单击该节目，导航到“播放节目”，并选择“使用 Azure Media Player”。  
6. 确认以后，再次右键单击该节目，然后选择“将输出 URL 复制到剪贴板”（也可通过菜单从“节目信息和设置”选项检索此信息）。

现在可以将流嵌入到播放器中，也可将其分发给受众进行实时观看。  

## <a name="troubleshooting"></a>疑难解答
有关指南，请参阅[故障排除](media-services-troubleshooting-live-streaming.md)一文。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
