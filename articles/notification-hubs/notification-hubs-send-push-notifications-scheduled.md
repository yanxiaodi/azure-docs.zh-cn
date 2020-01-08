---
title: 如何发送预定通知 | Microsoft 文档
description: 本主题介绍如何使用 Azure 通知中心发送预定通知。
services: notification-hubs
documentationcenter: .net
keywords: 推送通知,push notification,计划推送通知
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ef5eedaa903480ae670f9bc48d0af89744a99d22
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213001"
---
# <a name="how-to-send-scheduled-notifications"></a>如何：发送预定通知

如果在你的方案中，需要在将来的某个时间点发送通知，但却无法轻松地唤醒后端代码来发送该通知。 标准层通知中心支持安排最多未来 7 天的通知。


## <a name="schedule-your-notifications"></a>计划通知
发送通知时，只需如以下示例中所示，使用通知中心 SDK 中的 [`ScheduledNotification` 类](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx)：

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>取消预定通知
此外，可以使用其 notificationId 取消以前计划的通知：

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

可以发送的预定通知数没有限制。

## <a name="next-steps"></a>后续步骤

参阅以下教程：

 - [向所有注册的设备推送通知](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [向特定设备推送通知](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [推送本地化通知](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [向特定用户推送通知](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [推送基于位置的通知](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
