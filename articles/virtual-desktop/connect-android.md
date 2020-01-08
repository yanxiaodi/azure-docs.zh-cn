---
title: 从 Android 连接到 Windows 虚拟桌面-Azure
description: 如何使用 Android 客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: helohr
ms.openlocfilehash: c19aa6e0acc936c5b03afdab99ce0b9230838ce2
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71339005"
---
# <a name="connect-with-the-android-client"></a>与 Android 客户端连接

> 适用于：Android 4.1 及更高版本、Chromebook 和 ChromeOS 53 及更高版本。

>[!NOTE]
> 当前在预览版中提供了从 Android 客户端访问 Windows 虚拟桌面资源的功能。

可以使用可下载的客户端从 Android 设备访问 Windows 虚拟桌面资源。 你还可以在支持 Google Play 商店的 Chromebook 设备上使用 Android 客户端。 本指南将说明如何设置 Android 客户端。

## <a name="install-the-android-client"></a>安装 Android 客户端

若要开始，请在 Android 设备上[下载](https://play.google.com/store/apps/details?id=com.microsoft.rdc.android)并安装客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

订阅管理员提供的源，获取可在 Android 设备上访问的托管资源的列表。

订阅源：

1. 在连接中心中，点击 " **+** "，然后点击 "**远程资源源**"。
2. 在 "**源 url** " 字段中输入源 url。 源 URL 可以是 URL，也可以是电子邮件地址。
   - 如果使用 URL，请使用管理员提供的 URL，通常 <https://rdweb.wvd.microsoft.com>。
   - 若要使用电子邮件，请输入你的电子邮件地址。 如果管理员配置了服务器，则客户端将搜索与你的电子邮件地址关联的 URL。
3. 点击 "**下一步**"。
4. 在系统提示时提供凭据。
   - 对于 "**用户名**"，为用户名提供访问资源的权限。
   - 对于 "**密码**"，请提供与用户名关联的密码。
   - 如果管理员以这种方式配置了身份验证，则还可能会提示你提供其他因素。

订阅后，连接中心应显示远程资源。

订阅源后，该源的内容会定期自动更新。 可以根据管理员所做的更改来添加、更改或删除资源。

## <a name="next-steps"></a>后续步骤

若要了解有关如何使用 Android 客户端的详细信息，请查看[android 客户端入门](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-android)。
