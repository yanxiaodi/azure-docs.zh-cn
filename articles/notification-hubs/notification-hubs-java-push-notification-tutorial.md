---
title: 如何结合使用通知中心与 Java
description: 了解如何从 Java 后端使用 Azure 通知中心。
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 532ffc7a7393f016f27264b67b4ee5d3e6e5888f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213206"
---
# <a name="how-to-use-notification-hubs-from-java"></a>如何通过 Java 使用通知中心

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

本主题介绍完全受支持的全新官方 Azure 通知中心 Java SDK 的关键功能。
此项目为开源项目，可在 [Java SDK] 查看完整的 SDK 代码。

通常情况下，如 MSDN 主题[通知中心 REST API](https://msdn.microsoft.com/library/dn223264.aspx) 中所述，可以使用通知中心 REST 接口从 Java/PHP/Python/Ruby 后端访问所有通知中心功能。 此 Java SDK 在以 Java 形式表示的 REST 接口上提供瘦包装器。

SDK 目前支持以下内容：

* 通知中心上的 CRUD
* 注册上的 CRUD
* 安装管理
* 导入/导出注册
* 常规发送
* 计划发送
* 通过 Java NIO 的异步操作
* 支持的平台：APNS (iOS)、FCM (Android)、WNS（Windows 应用商店应用）、MPNS (Windows Phone)、ADM (Amazon Kindle Fire)、百度（没有 Google 服务的 Android）

## <a name="sdk-usage"></a>SDK 用法

### <a name="compile-and-build"></a>编译和生成

使用 [Maven]

生成：

    mvn package

## <a name="code"></a>代码

### <a name="notification-hub-cruds"></a>通知中心 CRUD

**NamespaceManager：**

    ```java
    NamespaceManager namespaceManager = new NamespaceManager("connection string")
    ```

**创建通知中心：**

    ```java
    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);
    ```

 或者

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**获取通知中心：**

    ```java
    hub = namespaceManager.getNotificationHub("hubname");
    ```

**更新通知中心：**

    ```java
    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);
    ```

**删除通知中心：**

    ```java
    namespaceManager.deleteNotificationHub("hubname");
    ```

### <a name="registration-cruds"></a>注册 CRUD

**创建通知中心客户端：**

    ```java
    hub = new NotificationHub("connection string", "hubname");
    ```

**创建 Windows 注册：**

    ```java
    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

**创建 iOS 注册：**

    ```java
    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);
    ```

同样，可以针对 Android (FCM)、Windows Phone (MPNS) 和 Kindle Fire (ADM) 创建注册。

**创建模板注册：**

    ```java
    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);
    ```

**采用“创建注册 ID + upsert”模式创建注册：**

如果在设备上存储注册 ID，请删除重复项以防出现任何响应丢失：

    ```java
    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);
    ```

**更新注册：**

    ```java
    hub.updateRegistration(reg);
    ```

**删除注册：**

    ```java
    hub.deleteRegistration(regid);
    ```

**查询注册：**

* **获取单个注册：**

    ```java
    hub.getRegistration(regid);
    ```

* **获取中心的所有注册：**

    ```java
    hub.getRegistrations();
    ```

* **获取具有标记的注册：**

    ```java
    hub.getRegistrationsByTag("myTag");
    ```

* **按渠道获取注册：**

    ```java
    hub.getRegistrationsByChannel("devicetoken");
    ```

所有集合查询都支持 $top 和继续标记。

### <a name="installation-api-usage"></a>安装 API 用法

安装 API 是一种注册管理的替代机制。 现在，可以使用“单个”安装对象，而不必维护多个注册，这些注册不但工作量大，而且执行起来可能容易出错或效率低下。

安装包含所需一切内容：推送通道（设备标记）、标记、模板、辅助磁贴（用于 WNS 和 APNS）。 无需再调用该服务即可获取 ID - 只需生成 GUID 或任何其他标识符，将其保存在设备上并连同推送通道（设备标记）一起发送到后端即可。

在后端，只能对 `CreateOrUpdateInstallation` 进行单个调用；该调用具有完全幂等性，因此，如果需要，可随时重试。

针对 Amazon Kindle Fire 的示例如下：

    ```java
    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);
    ```

如果希望进行更新：

    ```java
    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);
    ```

对于高级方案，使用部分更新功能，允许仅修改安装对象的特定属性。 部分更新是可针对安装对象运行的 JSON Patch 操作的子集。

    ```java
    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
    ```

删除安装：

    ```java
    hub.deleteInstallation(installation.getInstallationId());
    ```

`CreateOrUpdate`、`Patch` 和 `Delete` 最终与 `Get` 保持一致。 请求的操作会在调用期间进入系统队列并在后台执行。 Get 并不适用于主运行时方案，只适用于调试和故障排除，其会受到服务的严密限制。

安装的发送流与注册的一样。 将通知定向至特定安装 - 仅使用了标记“InstallationId:{desired-id}”。 在此事例中，代码为：

    ```java
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");
    ```

为多个模板之一：

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
    ```

### <a name="schedule-notifications-available-for-standard-tier"></a>计划通知（适用于标准层）

与常规发送相同，但多了一个参数 - scheduledTime，表示通知应传递的时间。 服务接受现在 + 5 分钟与现在 + 7 天之间的任何时间点。

**计划 Windows 本机通知：**

    ```java
    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());
    ```

### <a name="importexport-available-for-standard-tier"></a>导入/导出（可用于标准层）

可能需要对注册执行批量操作。 通常，这种方式适用于与其他系统的集成或为了更新标记而执行的大规模修复。 如果涉及数以千计的注册，我们不建议使用 Get/Update 流。 系统的“导入/导出”功能旨在应对这种情况。 你将提供对存储帐户下作为传入数据的源和输出的位置的 Blob 容器的访问。

**提交导出作业：**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**提交导入作业：**

    ```java
    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);
    ```

**等到作业完成：**

    ```java
    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }
    ```

**获取所有作业：**

    ```java
    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
    ```

**使用 SAS 签名的 URI：**

 此 URL 是 Blob 文件或 Blob 容器的 URL 加上一组参数（例如权限和到期时间），再加上使用帐户的 SAS 密钥生成的所有这些内容的签名。 Azure 存储 Java SDK 具有丰富的功能，包括创建这些 URI。 作为简单的备选方案，可以考虑使用 `ImportExportE2E` 测试类（来自 GitHub 位置），该测试类具有基本、精简的签名算法实现。

### <a name="send-notifications"></a>发送通知

通知对象只是一个带标头的正文，而一些实用工具方法有助于构建本机和模板通知对象。

* **Windows 应用商店和 Windows Phone 8.1（非 Silverlight）**

    ```java
    String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
    Notification n = Notification.createWindowsNotification(toast);
    hub.sendNotification(n);
    ```

* **iOS**

    ```java
    String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
    Notification n = Notification.createAppleNotification(alert);
    hub.sendNotification(n);
    ```

* **Android**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createFcmNotification(message);
    hub.sendNotification(n);
    ```

* **Windows Phone 8.0 和 8.1 Silverlight**

    ```java
    String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>Hello from Java!</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
    Notification n = Notification.createMpnsNotification(toast);
    hub.sendNotification(n);
    ```

* **Kindle Fire**

    ```java
    String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
    Notification n = Notification.createAdmNotification(message);
    hub.sendNotification(n);
    ```

* **发送到标记**
  
    ```java
    Set<String> tags = new HashSet<String>();
    tags.add("boo");
    tags.add("foo");
    hub.sendNotification(n, tags);
    ```

* **发送到标记表达式**

    ```java
    hub.sendNotification(n, "foo && ! bar");
    ```

* **发送模板通知**

    ```java
    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("prop1", "v1");
    prop.put("prop2", "v2");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n);
    ```

运行 Java 代码，现在应该生成显示在目标设备上的通知。

## <a name="next-steps"></a>后续步骤

本主题介绍了如何为通知中心创建简单的 Java REST 客户端。 从这里可以：

* 下载完整的 [Java SDK]，其中包含完整的 SDK 代码。
* 播放示例：
  * [通知中心入门]
  * [发送突发新闻]
  * [发送本地化的突发新闻]
  * [发送通知到经身份验证的用户]
  * [发送跨平台通知到经身份验证的用户]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[通知中心入门]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[发送突发新闻]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[发送本地化的突发新闻]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[发送通知到经身份验证的用户]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[发送跨平台通知到经身份验证的用户]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
