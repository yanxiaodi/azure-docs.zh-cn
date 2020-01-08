---
title: 从 macOS 连接到 Windows 虚拟桌面-Azure
description: 如何使用 macOS 客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: fa61d69ae7aa650b6011696fd45d05ba20d2ac8b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338706"
---
# <a name="connect-with-the-macos-client"></a>使用 macOS 客户端进行连接

> 适用于： macOS 10.12 或更高版本

>[!NOTE]
> 当前在预览版中提供从 macOS 客户端访问 Windows 虚拟桌面资源的功能。

可以使用可下载的客户端从 macOS 设备访问 Windows 虚拟桌面资源。 本指南将说明如何设置客户端。

## <a name="install-the-client"></a>安装客户端

若要开始，请在 macOS 设备上[下载](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) 并安装客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

订阅你的管理员提供的源，你可以在 macOS 设备上获取可供你使用的托管资源的列表。

订阅源：

1. 在主页上选择 "**添加源**" 以连接到服务并检索资源。
2. 输入源 URL。 这可以是 URL 或电子邮件地址：
   - 如果使用 URL，请使用管理员提供的 URL。 通常，该 URL 是<https://rdweb.wvd.microsoft.com>。
   - 若要使用电子邮件，请输入你的电子邮件地址。 如果管理员已将服务器配置为，则此方法将通知客户端搜索与你的电子邮件地址关联的 URL。
3. 选择 "**订阅**"。
4. 出现提示时，请用用户帐户登录。

登录后，应会看到可用资源的列表。

订阅源后，该源的内容会定期自动更新。 可以根据管理员所做的更改来添加、更改或删除资源。

## <a name="next-steps"></a>后续步骤

若要了解有关 macOS 客户端的详细信息，请查看[macOS 客户端入门](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac)文档。
