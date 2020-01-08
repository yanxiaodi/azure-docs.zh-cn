---
title: 通过在 Azure CDN 中压缩文件来提高性能 | Microsoft Docs
description: 了解如何通过在 Azure CDN 中压缩文件来提高文件传输速度和增加页面加载性能。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: magattus
ms.openlocfilehash: 9f185f58e1d33a3985777cb22bc7578f9f2c4541
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593807"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>通过在 Azure CDN 中压缩文件来提高性能
文件压缩是提高文件传输速度和增加页面加载性能的一种简单有效的方法，可通过在从服务器发送文件之前减少其大小来实现。 文件压缩可以减少带宽成本，并为用户提供更快的响应体验。

有两种方法可启用文件压缩：

- 在源服务器上启用压缩。 在此示例中，Azure CDN 传递压缩文件，将这些文件传递给发出请求的客户端。
- 直接在 CDN POP 服务器上启用压缩（“快速压缩”  ）。 在这种情况下，CDN 会压缩文件并将其提供给最终用户，即使源服务器未压缩文件也是如此。

> [!IMPORTANT]
> Azure CDN 配置更改可能需要一段时间，才能在整个网络中传播： 
> - 对于 **Microsoft 推出的 Azure CDN 标准版**配置文件，传播通常可在 10 分钟内完成。 
> - 对于 **Akamai 的 Azure CDN 标准版**配置文件，传播通常可在一分钟内完成。 
> - 对于“Verizon 提供的 Azure CDN 标准版”  和“Verizon 提供的 Azure CDN 高级版”  配置文件，传播通常在 10 分钟内完成。 
> 
> 如果首次为 CDN 终结点设置压缩，请考虑等待 1-2 个小时，确保将压缩设置传播到 POP 之后再排查问题。

## <a name="enabling-compression"></a>启用压缩
标准和高级 CDN 层提供相同的压缩功能，但用户界面不同。 有关标准和高级 CDN 层之间的差异的详细信息，请参阅 [Azure CDN 概述](cdn-overview.md)。

### <a name="standard-cdn-profiles"></a>标准 CDN 配置文件 
> [!NOTE]
> 本部分适用于 **Microsoft 的 Azure CDN 标准版**、**Verizon 的 Azure CDN 标准版**和 **Akamai 的 Azure CDN 标准版**配置文件。
> 
> 

1. 从 CDN 配置文件页，选择想要管理的 CDN 终结点。

    ![CDN 配置文件终结点](./media/cdn-file-compression/cdn-endpoints.png)

    CDN 终结点页面会打开。
2. 选择“压缩”  。

    ![CDN 压缩选择](./media/cdn-file-compression/cdn-compress-select-std.png)

    此时将打开“压缩”页。
3. 选择“启用”  以启用压缩。

    ![CDN 文件压缩选项](./media/cdn-file-compression/cdn-compress-standard.png)
4. 使用默认 MIME 类型，或通过添加或删除 MIME 类型来修改列表。

   > [!TIP]
   > 建议不要对压缩格式应用压缩（虽然可以执行此操作）。 压缩格式示例有 ZIP、MP3、MP4 或 JPG。
   > 

   > [!NOTE]
   > Microsoft 提供的 Azure CDN 标准版暂不支持修改 MIME 类型的默认列表。
   > 

5. 进行更改后，请选择“保存”  。

### <a name="premium-cdn-profiles"></a>高级 CDN 配置文件
> [!NOTE]
> 本部分仅适用于**来自 Verizon 的高级 Azure CDN** 配置文件。
> 

1. 从“CDN 配置文件”页中，选择“管理”  。

    ![CDN 管理选择](./media/cdn-file-compression/cdn-manage-btn.png)

    CDN 管理门户打开。
2. 将鼠标悬停在“**HTTP 大**”选项卡上，然后悬停在“**缓存设置**”浮出控件。 选择“压缩”  。

    ![CDN 压缩选择](./media/cdn-file-compression/cdn-compress-select.png)

    此时将显示压缩选项。

    ![CDN 文件压缩选项](./media/cdn-file-compression/cdn-compress-files.png)
3. 通过选择“启用压缩”  来启用压缩。 在“文件类型”  框中，以逗号分隔的列表（无空格）的形式输入想要压缩的 MIME 类型。

   > [!TIP]
   > 建议不要对压缩格式应用压缩（虽然可以执行此操作）。 压缩格式示例有 ZIP、MP3、MP4 或 JPG。
   > 

4. 进行更改后，请选择“更新”  。

## <a name="compression-rules"></a>压缩规则

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Microsoft 的 Azure CDN 标准版配置文件

对于“Microsoft 提供的 Azure CDN 标准版”  配置文件，只有符合条件的文件才能进行压缩。 要符合压缩条件，文件必须：
- 已为 MIME 类型[配置为压缩](#enabling-compression)。
- 大于 1 KB
- 小于 8 MB

这些配置文件支持以下压缩编码：
- gzip (GNU zip)
- brotli 

如果请求支持多个压缩类型，那么 brotli 压缩优先。

如果对资产的请求指定了 gzip 压缩并且请求导致缓存未命中，则 Azure CDN 将直接在 POP 服务器上对资产执行 gzip 压缩。 此后，将从缓存提供压缩的文件。

### <a name="azure-cdn-from-verizon-profiles"></a>Verizon 的 Azure CDN 配置文件

对于 **Verizon 的 Azure CDN 标准版**和 **Verizon 的 Azure CDN 高级版**配置文件，只有符合条件的文件才会被压缩。 要符合压缩条件，文件必须：
- 大于 128 个字节
- 小于 3 MB

这些配置文件支持以下压缩编码：
- gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 

如果请求支持多个压缩类型，这些压缩类型优先于 brotli 压缩。

如果对资产的请求指定了 brotli 压缩（HTTP 标头为 `Accept-Encoding: br`）并且请求导致缓存未命中，则 Azure CDN 将直接在 POP 服务器上对资产执行 brotli 压缩。 此后，将从缓存提供压缩的文件。

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Akamai 的 Azure CDN 标准版配置文件

对于 **Akamai 的 Azure CDN 标准版**配置文件，所有文件都适合压缩。 但文件必须为已[配置为压缩](#enabling-compression)的 MIME 类型。

这些配置文件仅支持 gzip 压缩编码。 配置文件终结点请求 gzip 编码的文件时，始终是从源请求，而不考虑客户端请求。 

## <a name="compression-behavior-tables"></a>压缩行为表
这些表描述每种方案的 Azure CDN 压缩行为：

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>已禁用压缩或文件不适合压缩
| 客户端请求的格式（通过 Accept-Encoding 标头） | 缓存文件格式 | CDN 对客户端的响应 | 说明&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| 压缩 |压缩 |压缩 | |
| 压缩 |未压缩 |未压缩 | |
| 压缩 |未缓存 |压缩或未压缩 |源响应确定 CDN 是否执行压缩。 |
| 未压缩 |压缩 |未压缩 | |
| 未压缩 |未压缩 |未压缩 | |
| 未压缩 |未缓存 |未压缩 | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>已启用压缩且文件适合压缩
| 客户端请求的格式（通过 Accept-Encoding 标头） | 缓存文件格式 | CDN 对客户端的响应 | 说明 |
| --- | --- | --- | --- |
| 压缩 |压缩 |压缩 |在支持的格式之间进行 CDN 转码。 |
| 压缩 |未压缩 |压缩 |CDN 执行压缩。 |
| 压缩 |未缓存 |压缩 |如果源返回未压缩文件，CDN 将执行压缩。 <br/>来自 Verizon 的 Azure CDN 将传递第一个请求上的未压缩文件，然后压缩并缓存文件以供后续请求使用  。 <br/>永远不会压缩带 `Cache-Control: no-cache` 标头的文件。 |
| 未压缩 |压缩 |未压缩 |CDN 执行解压缩。 |
| 未压缩 |未压缩 |未压缩 | |
| 未压缩 |未缓存 |未压缩 | |

## <a name="media-services-cdn-compression"></a>媒体服务 CDN 压缩
对于启用了媒体服务 CDN 流式处理的终结点，默认为以下 MIME 类型启用压缩： 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

## <a name="see-also"></a>请参阅
* [排查 CDN 文件压缩问题](cdn-troubleshoot-compression.md)    

