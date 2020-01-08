---
title: 使用 Azure 应用服务的移动应用功能将推送通知添加到 Apache Cordova 应用 | Microsoft Docs
description: 了解如何使用移动应用将推送通知发送到 Apache Cordova 应用。
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: ''
author: elamalani
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: e6755c3fb1fca342d94fdaa96c0dce614d762172
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443554"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>将推送通知添加到 Apache Cordova 应用

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center 投入新和集成服务移动应用开发的核心。 开发人员可以使用**构建**，**测试**并**分发**服务来设置持续集成和交付管道。 应用程序部署后，开发人员可以监视状态和其应用程序使用的使用情况**Analytics**并**诊断**服务，并与用户使用**推送**服务。 开发人员还可以利用**身份验证**其用户进行身份验证并**数据**服务以持久保存并在云中的应用程序数据同步。 请查看[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started-push)今天。
>

## <a name="overview"></a>概述

在本教程中，添加将通知推送到[Apache Cordova 快速入门][5]项目，以便每次插入一条记录时，向设备发送推送通知。

如果不使用下载的快速入门服务器项目，则需要推送通知扩展包。 有关详细信息，请参阅[使用.NET 后端服务器 SDK 适用于移动应用][1]。

## <a name="prerequisites"></a>先决条件

本教程假设已使用 Visual Studio 2015 开发了一个 Apache Cordova 应用程序。 此设备应在 Google Android 模拟器、Android 设备、Windows 设备或 iOS 设备上运行。

要完成本教程，需要：

* 使用 PC [Visual Studio Community 2015][2]或更高版本
* [用于 Apache Cordova 的 Visual Studio 工具][4]
* [有效的 Azure 帐户][3]
* 已完成[Apache Cordova 快速入门][5]项目
* (Android)一个[Google 帐户][6]具有已验证电子邮件地址
* (iOS)[Apple Developer Program 会员资格][7]和 iOS 设备 （iOS 模拟器不支持推送通知）
* (Windows)一个[Microsoft Store 开发人员帐户][8]和 Windows 10 设备

## <a name="configure-hub"></a>配置通知中心

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[观看演示本部分中的步骤的视频][9]。

## <a name="update-the-server-project"></a>更新服务器项目

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>修改 Cordova 应用

确保 Apache Cordova 应用项目已准备就绪，可通过安装 Cordova 推送插件和任何平台特定的推送服务来处理推送通知。

#### <a name="update-the-cordova-version-in-your-project"></a>更新项目中的 Cordova 版本。

如果项目使用早于版本 6.1.1 的 Apache Cordova 版本，请更新客户端项目。 若要更新项目，请执行以下步骤：

* 若要打开配置设计器，请右键单击 `config.xml`。
* 选择“平台”选项卡。 
* 在“Cordova CLI”  文本框中选择“6.1.1”。  
* 若要更新项目，请依次选择“生成”、“生成解决方案”。  

#### <a name="install-the-push-plugin"></a>安装推送插件

Apache Cordova 应用程序不支持在本地处理设备或网络功能。  这些功能由发布在 [npm][10] 或 GitHub 上的插件提供。 `phonegap-plugin-push` 插件可处理网络推送通知。

可通过下面其中一种方式安装推送插件：

**从命令提示符：**

运行以下命令：

    cordova plugin add phonegap-plugin-push

**从 Visual Studio 内部：**

1. 在“解决方案资源管理器”中，打开 `config.xml` 文件。 接下来，选择“插件” > “自定义”。   然后选择“Git”作为安装源。 

2. 输入 `https://github.com/phonegap/phonegap-plugin-push` 作为源。

    ![在解决方案资源管理器中打开 config.xml 文件][img1]

3. 选择安装源旁边的箭头。

4. 在 **SENDER_ID** 中，如果已经有 Google Developer Console 项目的数值项目 ID，可将其添加在此处。 否则，请输入一个占位符值，如 777777。 如果以 Android 为目标，可稍后在 config.xml 文件中更新该值。

    >[!NOTE]
    >从版本 2.0.0 开始，需要在项目根文件夹中安装 google-services.json 才能配置发送方 ID。 有关详细信息，请参阅[安装文档](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)。

5. 选择 **添加** 。

现已安装推送插件。

#### <a name="install-the-device-plugin"></a>安装设备插件

按照用于安装推送插件的相同步骤进行操作。 从“核心插件”列表添加“设备”插件。 （选择“插件”   > “核心”  可找到它。）需使用此插件获取平台名称。

#### <a name="register-your-device-when-the-application-starts"></a>在应用程序启动时注册设备 

最初，我们会针对 Android 提供一些最少的代码。 稍后，可以修改应用以便在 iOS 或 Windows 10 上运行。

1. 在登录过程的回调期间，添加对 **registerForPushNotifications** 的调用。 或者，可以在 **onDeviceReady** 方法的底部添加该调用：

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    此示例演示在身份验证成功之后调用 **registerForPushNotifications**。 可以根据需要经常调用 `registerForPushNotifications()`。

2. 按如下所示添加新的 **registerForPushNotifications** 方法：

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. (Android)在上述代码中，替换`Your_Project_ID`的数值项目 ID 为你的应用[Google Developer Console][18]。

## <a name="optional-configure-and-run-the-app-on-android"></a>（可选）在 Android 上配置并运行应用

完成此部分以启用 Android 的推送通知。

#### <a name="enable-gcm"></a>启用 Firebase Cloud Messaging

由于我们最初面向的是 Google Android 平台，因此必须启用 Firebase Cloud Messaging。

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>使用 FCM 配置移动应用后端以发送推送请求

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>配置适用于 Android 的 Cordova 应用

在 Cordova 应用中打开 **config.xml**。 然后，替换`Your_Project_ID`的数值项目 ID 为你的应用[Google Developer Console][18]。

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

打开 **index.js**。 然后更新代码以使用数值项目 ID。

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>配置 Android 设备以进行 USB 调试

需要启用 USB 调试才能将应用程序部署到 Android 设备。 在 Android 手机上执行以下步骤：

1. 转到“设置”   >   “关于手机”。 然后不断点击“内部版本号”  （大约 7 次），直到启用开发人员模式。
2. 返回“设置”   >    “开发人员选项”，启用“USB 调试”。 然后使用 USB 线缆将 Android 手机连接到开发电脑。

已使用运行 Android 6.0 (Marshmallow) 的 Google Nexus 5X 设备对此进行了测试。 但是，任何新式 Android 版本中都普遍具有此技术。

#### <a name="install-google-play-services"></a>安装 Google Play Services

推送插件依赖 Android Google Play 服务发送推送通知。

1. 在 Visual Studio 中，选择“工具”   > “Android”   >   “Android SDK 管理器”。 展开“Extras”文件夹。  勾选相应的复选框，确保安装以下每个 SDK：

   * Android 2.3 或更高版本
   * Google 存储库修订 27 或更高版本
   * Google Play Services 9.0.2 或更高版本

2. 选择“安装程序包”  。 等待安装完成。

[phonegap-plugin-push 安装文档][19]中列出了当前需要的库。

#### <a name="test-push-notifications-in-the-app-on-android"></a>在 Android 上测试应用中的推送通知

现在，可以通过运行该应用并将项插入 TodoItem 表中测试推送通知。 只要使用同一后端，就可以在同一设备或另一台设备中进行测试。 通过下面其中一种方式在 Android 平台上测试 Cordova 应用：

* *在物理设备上：* 使用 USB 线缆将 Android 设备连接到开发计算机。  不是使用 **Google Android 模拟器**，而是选择“设备”  。 Visual Studio 会将应用程序部署到设备，并运行应用程序。 可以随后在该设备上与该应用程序进行交互。

  如屏幕共享应用程序[Mobizen][20]可以帮助你开发 Android 应用程序。 Mobizen 会将 Android 屏幕投影到电脑上的 Web 浏览器。

* *在 Android 模拟器中：* 使用模拟器时，还需要其他配置步骤。

    请确保部署到将 Google API 设置为目标的虚拟设备，如 Android 虚拟设备 (AVD) 管理器所示。

    ![Android 虚拟设备管理器](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    如果你想要使用更快的 x86 仿真程序中，[安装 HAXM 驱动程序][11]，然后配置模拟器以使用它。

    选择“应用”   > “设置”   > “添加帐户”  ，将 Google 帐户添加到 Android 设备。 然后遵照提示操作。

    ![将 Google 帐户添加到 Android 设备](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    像以前一样运行 todolist 应用并插入一个新的 todo 项。 此时通知区域中会显示一个通知图标。 可以打开通知抽屉查看通知的完整文本。

    ![查看通知](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>（可选）配置并在 iOS 上运行

本部分介绍了在 iOS 设备上运行 Cordova 项目。 如果不使用 iOS 设备，可以跳过本部分。

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>在 Mac 或云服务上安装并运行 iOS 远程生成代理

在使用 Visual Studio 在 iOS 上运行 Cordova 应用前，请通过中的步骤[iOS 安装指南][12]安装和运行远程生成代理。

请确保可以生成 iOS 应用。 需要执行安装指南中的步骤，通过 Visual Studio 生成适用于 iOS 的应用。 如果没有 Mac，可以使用服务（如 MacInCloud）中的远程生成代理针对 iOS 生成。 有关详细信息，请参阅[云中运行 iOS 应用][21]。

> [!NOTE]
> 在 iOS 上使用推送插件需要 Xcode 7 或更高版本。

#### <a name="find-the-id-to-use-as-your-app-id"></a>查找要作为应用程序 ID 的 ID

在注册应用以推送通知前，在 Cordova 应用中打开 config.xml，找到小组件元素中的 `id` 属性值，复制该值以供将来使用。 在以下 XML 中，ID 是 `io.cordova.myapp7777777`。

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

稍后，在 Apple 开发人员门户创建应用程序 ID 时，使用此标识符。 如果在开发人员门户上创建不同的应用 ID，则必须执行本教程后面所述的一些额外步骤。 小组件元素中的 ID 必须与开发人员门户中的应用 ID 匹配。

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>在 Apple 的开发人员门户上为推送通知注册应用

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[观看显示类似步骤的视频](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>配置 Azure 以发送推送通知

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>验证应用程序 ID 是否匹配 Cordova 应用

如果 Apple 开发人员帐户中创建的应用程序 ID 已与 config.xml 中的小组件元素的 ID 匹配，则可以跳过此步骤。 但是，如果 ID 不匹配，请执行以下步骤：

1. 从项目中删除平台文件夹。
2. 从项目中删除插件文件夹。
3. 从项目中删除 node_modules 文件夹。
4. 更新 config.xml 中的小组件元素的 ID 属性，以便使用 Apple 开发人员帐户中创建的应用 ID。
5. 重新生成项目。

##### <a name="test-push-notifications-in-your-ios-app"></a>在 iOS 应用中测试推送通知

1. 在 Visual Studio 中，确保已选择“iOS”作为部署目标。  然后选择“设备”，以便在连接的 iOS 设备上运行推送通知。 

    可以在使用 iTunes 连接到电脑的 iOS 设备上运行推送通知。 iOS 模拟器不支持推送通知。

2. 在 Visual Studio 中选择“运行”  按钮或按 **F5** 生成项目并在 iOS 设备中启动应用， 然后选择“确定”接受推送通知。 

   > [!NOTE]
   > 首次运行时，应用会请求对推送通知进行确认。

3. 在应用中，键入一项任务，并选择加号 **(+)** 图标。
4. 确认收到了通知。 然后选择“确定”以关闭通知。 

## <a name="optional-configure-and-run-on-windows"></a>（可选）配置并在 Windows 上运行

本部分介绍如何在 Windows 10 设备上运行 Apache Cordova 应用项目（ Windows 10 支持 PhoneGap 推送插件）。 如果不使用 Windows 设备，可以跳过本部分。

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>向 WNS 注册用于推送通知的 Windows 应用

若要在 Visual Studio 中使用应用商店选项，从解决方案平台列表中选择 Windows 目标，如 **Windows-x64** 或 **Windows-x86**。 （避免使用 **Windows-AnyCPU** 发送推送通知。）

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[观看显示类似步骤的视频][13]

#### <a name="configure-the-notification-hub-for-wns"></a>为 WNS 配置通知中心

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>配置 Cordova 应用，以支持 Windows 推送通知

若要打开配置设计器，请右键单击“config.xml”。  然后选择“视图设计器”  。 接下来，选择“Windows”选项卡，并选择“Windows 目标版本”下的“Windows 10”。   

若要在默认（调试）版本中支持推送通知，请打开 **build.json** 文件。 然后将“版本”配置复制到调试配置。

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

更新后，**build.json** 文件应包含以下代码：

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

生成应用并确保没有错误。 客户端应用现在应从移动应用后端注册通知。 对于解决方案中的每个 Windows 项目，重复此部分的操作。

#### <a name="test-push-notifications-in-your-windows-app"></a>在 Windows 应用中测试推送通知

在 Visual Studio 中，确保已选择 Windows 平台作为部署目标，例如 **Windows-x64** 或 **Windows-x86**。 若要在托管 Visual Studio 的 Windows 10 电脑上运行该应用，请选择“本地计算机”  。

1. 选择“运行”按钮生成项目并启动应用程序。 

2. 在应用中，为新 todoitem 键入一个名称，并选择加号 **(+)** 图标添加该项。

确认在添加该项目时收到了通知。

## <a name="next-steps"></a>后续步骤

* 有关推送通知的信息，请参阅[通知中心][17]。
* 如果尚未这样做，则继续通过本教程[添加身份验证][14]到 Apache Cordova 应用。

了解如何使用以下 SDK：

* [Apache Cordova SDK][15]
* [ASP.NET Server SDK][1]
* [Node.js Server SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
