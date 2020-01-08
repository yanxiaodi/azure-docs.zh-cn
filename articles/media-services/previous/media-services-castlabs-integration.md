---
title: 使用 castLabs 将 Widevine 许可证传送到 Azure 媒体服务 | Microsoft Docs
description: 本文介绍如何使用 Azure 媒体服务(AMS) 传送 AMS 使用 PlayReady 和 Widevine DRM 动态加密的流。 PlayReady 许可证来自媒体服务 PlayReady 许可证服务器，而 Widevine 许可证则由 castLabs 许可证服务器传送。
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 9c61fad333037074f392b019ae61c161673e4008
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "69016691"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>使用 castLabs 将 Widevine 许可证传送到 Azure 媒体服务 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>概述

本文介绍如何使用 Azure 媒体服务(AMS) 传送 AMS 使用 PlayReady 和 Widevine DRM 动态加密的流。 PlayReady 许可证来自媒体服务 PlayReady 许可证服务器，而 Widevine 许可证则由 **castLabs** 许可证服务器传送。

若要播放受 CENC（PlayReady 和/或 Widevine）保护的流式处理内容，可使用 [Azure Media Player](https://aka.ms/azuremediaplayer)。 有关详细信息，请参阅 [AMP 文档](https://amp.azure.net/libs/amp/latest/docs/)。

下图演示了高级 Azure 媒体服务和 castLabs 集成体系结构。

![集成](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>典型的系统设置

* 媒体内容存储在 AMS 中。
* 内容密钥的密钥 ID 存储在 castLabs 和 AMS 中。
* castLabs 和 AMS 均内置了令牌身份验证。 以下部分讨论身份验证令牌。 
* 客户端请求流式传输视频时，内容将使用**通用加密** (CENC) 进行动态加密，并由 AMS 动态打包成平滑流和 DASH。 我们还针对 HLS 流式处理协议提供 PlayReady M2TS 基本流加密。
* PlayReady 许可证从 AMS 许可证服务器检索，而 Widevine 许可证则从 castLabs 许可证服务器检索。 
* Media Player 自动根据客户端平台功能决定要提取哪个许可证。 

## <a name="authentication-token-generation-for-getting-a-license"></a>用于获取许可证的身份验证令牌生成

castLabs 和 AMS 均支持用于授予许可证的 JWT（JSON Web 令牌）令牌格式。 

### <a name="jwt-token-in-ams"></a>AMS 中的 JWT 令牌

下表描述了 AMS 中的 JWT 令牌。 

| 颁发者 | 所选安全令牌服务 (STS) 中的颁发者字符串 |
| --- | --- |
| 受众 |所用 STS 中的受众字符串 |
| 声明 |一组声明 |
| NotBefore |令牌的有效起始日期 |
| Expires |令牌的有效结束日期 |
| SigningCredentials |在 PlayReady 许可证服务器、castLabs 许可证服务器和 STS 之间共享的密钥，它可以是对称或非对称密钥。 |

### <a name="jwt-token-in-castlabs"></a>castLabs 中的 JWT 令牌

下表描述了 castLabs 中的 JWT 令牌。 

| 姓名 | 描述 |
| --- | --- |
| optData |一个包含相关信息的 JSON 字符串。 |
| crt |一个包含有关资源、其许可证信息和播放权限的信息的 JSON 字符串。 |
| iat |用 epoch 表示的当前日期时间。 |
| jti |有关此令牌的唯一标识符（每个令牌只能在 castLabs 系统中使用一次）。 |

## <a name="sample-solution-setup"></a>示例解决方案设置

[示例解决方案](https://github.com/AzureMediaServicesSamples/CastlabsIntegration)由两个项目组成：

* 可用于对 PlayReady 和 Widevine 的已引入资源设置 DRM 限制的控制台应用程序。
* 分发令牌的 Web 应用程序，可将其视为 STS 的非常简化版本。

若要使用控制台应用程序，请执行以下操作：

1. 更改 app.config 以设置 AMS 凭据、castLabs 凭据、STS 配置和共享密钥。
2. 将资源上传到 AMS。
3. 从上传的资源中获取 UUID 并更改 Program.cs 文件中的第 32 行：
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. 使用 AssetId 来命名 castLabs 系统中的资源（Program.cs 文件中的第 44 行）。
   
   必须为 **castLabs** 设置 AssetId；它必须是唯一的字母数字字符串。
5. 运行该程序。

若要使用 Web 应用程序 (STS)，请执行以下操作：

1. 更改 web.config 以设置 castlabs 商家 ID、STS 配置和共享密钥。
2. 部署到 Azure 网站。
3. 导航到该网站。

## <a name="playing-back-a-video"></a>播放视频

若要播放使用通用加密（PlayReady 和/或 Widevine）加密的视频，可以使用 [Azure Media Player](https://aka.ms/azuremediaplayer)。 运行控制台应用程序时，将回显内容密钥 ID 和清单 URL。

1. 打开新的选项卡并启动 STS： http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid]。
2. 转到 [Azure 媒体播放器](https://aka.ms/azuremediaplayer)。
3. 粘贴到流 URL 中。
4. 单击“高级选项”复选框。
5. 在“保护”下拉列表中选择 PlayReady 和/或 Widevine。
6. 将从 STS 获取的令牌粘贴到“令牌”文本框中。 
   
   castLab 许可证服务器不需要在令牌前面加“Bearer=”前缀。 因此，请在提交令牌之前删除该前缀。
7. 更新播放器。
8. 视频应正在播放。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

