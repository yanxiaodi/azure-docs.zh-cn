---
title: 使用 Azure 通知中心和 Google Cloud Messaging 将通知推送到 Android 应用 | Microsoft Docs
description: 本教程介绍如何使用 Azure 通知中心和 Google Firebase Cloud Messaging 将通知推送到 Android 设备。
services: notification-hubs
documentationcenter: android
keywords: 推送通知、推送通知、android 推送通知
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 8268c6ef-af63-433c-b14e-a20b04a0342a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 36af79b90722041ddb16bb90a73175a8635531fd
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212359"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>教程：使用 Azure 通知中心和 Google Cloud Messaging（已弃用）将通知推送到 Android 设备

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!WARNING]
> 截至 2018 年 4 月 10 日，Google 已弃用 Google Cloud Messaging (GCM)。 GCM 服务器和客户端 API 已弃用，最快将于 2019 年 5 月 29 日移除。 有关详细信息，请参阅 [GCM 和 FCM 常见问题解答](https://developers.google.com/cloud-messaging/faq)。

## <a name="overview"></a>概述

本教程演示如何使用 Azure 通知中心将推送通知发送到 Android 应用程序。
请创建一个空白 Android 应用，以便使用 Google Cloud Messaging (GCM) 接收推送通知。

> [!IMPORTANT]
> Google Cloud Messaging (GCM) 已弃用，[很快](https://developers.google.com/cloud-messaging/faq)将会被删除。

> [!IMPORTANT]
> 本主题演示了使用 Google Cloud Messaging (GCM) 的推送通知。 如果使用的是 Google 的 Firebase Cloud Messaging (FCM)，请参阅[使用 Azure 通知中心和 FCM 将推送通知发送到 Android](notification-hubs-android-push-notification-google-fcm-get-started.md)。

可以从 [此处](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted)的 GitHub 中下载本教程的已完成代码。

在本教程中，将执行以下操作：

> [!div class="checklist"]
> * 创建支持 Google Cloud Messaging 的项目。
> * 创建通知中心
> * 将应用连接到通知中心
> * 测试应用程序

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前[创建一个免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)。

## <a name="creating-a-project-that-supports-google-cloud-messaging"></a>创建支持 Google Cloud Messaging 的项目

[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>创建通知中心

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-setting-for-the-notification-hub"></a>配置通知中心的 GCM 设置

1. 在“通知设置”中选择“Google (GCM)”。  
2. 输入从 Google 云控制台获得的 **API 密钥**。
3. 在工具栏上选择“保存”。 

    ![Azure 通知中心 - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

通知中心现在已配置为使用 GCM，并且你有连接字符串用于注册应用以接收和发送推送通知。

## <a id="connecting-app"></a>将你的应用连接到通知中心

### <a name="create-a-new-android-project"></a>创建新的 Android 项目

1. 在 Android Studio 中，启动新的 Android Studio 项目。

   ![Android Studio - 新项目][13]
2. 选择“手机和平板电脑”  外形规格和要支持的“最低 SDK 版本”  。 然后单击“下一步”  。

   ![Android Studio - 项目创建工作流][14]
3. 选择“空活动”作为主活动，单击“下一步”，并单击“完成”    。

### <a name="add-google-play-services-to-the-project"></a>将 Google Play 服务添加到项目

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>添加 Azure 通知中心库

1. 在**应用**的 `Build.Gradle` 文件中，在 **dependencies** 部分添加以下行。

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```
2. 在 **dependencies** 节的后面添加以下存储库。

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="updating-the-projects-androidmanifestxml"></a>更新项目的 AndroidManifest.xml

1. 若要支持 GCM，请在代码中实现实例 ID 侦听器服务，以便使用 [Google 的实例 ID API](https://developers.google.com/instance-id/) 来[获取注册令牌](https://developers.google.com/cloud-messaging/)。 在本教程中，类的名称为 `MyInstanceIDService`。

    将以下服务定义添加到 AndroidManifest.xml 文件的 `<application>` 标记内。 将 `<your package>` 占位符替换为 `AndroidManifest.xml` 文件顶部显示的实际包名称。
  
    ```xml
    <service android:name="<your package>.MyInstanceIDService" android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID"/>
        </intent-filter>
    </service>
    ```
2. 从实例 ID API 收到 GCM 注册令牌后，应用程序将使用它[在 Azure 通知中心注册](notification-hubs-push-notification-registration-management.md)。 在后台的注册使用名为 `RegistrationIntentService` 的 `IntentService` 来完成。 此服务负责[刷新 GCM 注册令牌](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)。

    将以下服务定义添加到 AndroidManifest.xml 文件的 `<application>` 标记内。 将 `<your package>` 占位符替换为 `AndroidManifest.xml` 文件顶部显示的实际包名称。

    ```xml
    <service
        android:name="<your package>.RegistrationIntentService"
        android:exported="false">
    </service>
    ```
3. 定义接收通知的接收者。 将以下接收者定义添加到 AndroidManifest.xml 文件的 `<application>` 标记内。 将 `<your package>` 占位符替换为 `AndroidManifest.xml` 文件顶部显示的实际包名称。

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```
4. 在 `</application>` 标记下面添加以下必要的 GCM 权限。 将 `<your package>` 替换为 `AndroidManifest.xml` 文件顶部显示的包名称。

    有关这些权限的详细信息，请参阅 [Setup a GCM Client app for Android](https://developers.google.com/cloud-messaging/)（设置适用于 Android 的 GCM 客户端应用）。

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>
    ```

### <a name="adding-code"></a>添加代码

1. 在项目视图中，展开 **app** > **src** > **main** > **java** 右键单击 **java** 下的包文件夹，单击“新建”，并单击“Java 类”。   `NotificationSettings`的新类。

    ![Android Studio - 新 Java 类][6]

    在 `NotificationSettings` 类的以下代码中更新三个占位符：

   * `SenderId`：之前在 [Google 云控制台](https://cloud.google.com/console)中获取的项目编号。
   * `HubListenConnectionString`：中心的 `DefaultListenAccessSignature` 连接字符串。 可以复制该连接字符串，方法是在 [Azure 门户]的中心的“设置”页上单击“访问策略”   。
   * `HubName`：使用 [Azure 门户]的中心页中显示的通知中心的名称。

     `NotificationSettings` 代码：

     ```java
     public class NotificationSettings {
        public static String SenderId = "<Your project number>";
        public static String HubName = "<Your HubName>";
        public static String HubListenConnectionString = "<Your default listen connection string>";
     }
     ```
2. 添加名为 `MyInstanceIDService` 的另一个新类。 此类是实例 ID 侦听器服务实现。

    此类的代码调用 `IntentService` 以在后台[刷新 GCM 令牌](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)。

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.android.gms.iid.InstanceIDListenerService;

    public class MyInstanceIDService extends InstanceIDListenerService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.i(TAG, "Refreshing GCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```
3. 将另一个名为 `RegistrationIntentService`的新类添加到项目。 此类实现的 `IntentService` 用于[刷新 GCM 令牌](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)和[在通知中心注册](notification-hubs-push-notification-registration-management.md)。

    针对此类使用以下代码。

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.google.android.gms.iid.InstanceID;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {
            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;

            try {
                InstanceID instanceID = InstanceID.getInstance(this);
                String token = instanceID.getToken(NotificationSettings.SenderId,
                        GoogleCloudMessaging.INSTANCE_ID_SCOPE);
                Log.i(TAG, "Got GCM Registration Token: " + token);

                // Storing the registration id that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if ((regID=sharedPreferences.getString("registrationID", null)) == null) {
                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.i(TAG, "Attempting to register with NH using token : " + token);

                    regID = hub.register(token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                    resultString = "Registered Successfully - RegId : " + regID;
                    Log.i(TAG, resultString);
                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                } else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete token refresh", e);
                // If an exception happens while fetching the new token or updating the registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```
4. 在 `MainActivity` 类的开头添加以下 `import` 语句。

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.google.android.gms.gcm.*;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    import android.content.Intent;
    ```
5. 将以下私有成员添加到类的顶部。 此代码[检查 Google 推荐的 Google Play Services 的可用性](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)。

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private GoogleCloudMessaging gcm;
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    private static final String TAG = "MainActivity";
    ```
6. 在 `MainActivity` 类中，添加以下方法来检查 Google Play 服务的可用性。

    ```java
    /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```
7. 在 `MainActivity` 类中添加以下代码，用于在调用 `IntentService` 之前检查 Google Play Services，以获取 GCM 注册令牌并向通知中心注册。

    ```java
    public void registerWithNotificationHubs()
    {
        Log.i(TAG, " Registering with Notification Hubs");

        if (checkPlayServices()) {
            // Start IntentService to register this application with GCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```
8. 在 `MainActivity` 类的 `OnCreate` 方法中添进行下代码，以便在创建活动时开始注册过程。

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        registerWithNotificationHubs();
    }
    ```
9. 将其他这些方法添加到 `MainActivity`，以验证和报告应用状态。

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```
10. `ToastNotify` 方法使用“Hello World”`TextView` 控件持续报告应用状态和通知  。 在 activity_main.xml 布局中，为该控件添加以下 ID。

    ```xml
    android:id="@+id/text_hello"
    ```
11. 为 AndroidManifest.xml 中定义的接收者添加一个子类。 将另一个名为 `MyHandler`的新类添加到项目。
12. 在 `MyHandler.java` 的顶部添加以下 import 语句：

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;
    import android.net.Uri;
    import android.media.RingtoneManager;
    ```
13. 为 `MyHandler` 类添加以下代码，使其成为 `com.microsoft.windowsazure.notifications.NotificationsHandler` 的子类。

    此代码将重写 `OnReceive` 方法，因此处理程序会报告所收到的通知。 处理程序还使用 `sendNotification()` 方法将推送通知发送到 Android 通知管理器。 应用未运行但收到通知时，应执行 `sendNotification()` 方法。

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }

        private void sendNotification(String msg) {

            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);

            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);

            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(ctx)
                            .setSmallIcon(R.mipmap.ic_launcher)
                            .setContentTitle("Notification Hub Demo")
                            .setStyle(new NotificationCompat.BigTextStyle()
                                    .bigText(msg))
                            .setSound(defaultSoundUri)
                            .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
    }
    ```
14. 在 Android Studio 的菜单栏上，单击“生成” > “重新生成项目”，确保代码中没有任何错误。  

## <a name="testing-your-app"></a>测试应用程序

### <a name="run-the-mobile-application"></a>运行移动应用程序

1. 运行应用，并留意报告注册成功的注册 ID。

      ![测试 Android - 通道注册][18]
2. 输入一条要发送到已在中心注册的所有 Android 设备的通知消息。

      ![测试 Android - 发送一条消息][19]

3. 按“发送通知”。  任何有应用运行的设备都会显示一个 `AlertDialog` 实例，其中包含推送通知消息。 未运行此应用程序，但之前已注册推送通知的设备会在 Android 通知管理器中收到通知。 从左上角向下轻扫即可查看通知消息。

      ![测试 Android - 通知][21]

### <a name="test-send-push-notifications-from-the-azure-portal"></a>从 Azure 门户测试性发送推送通知

可以通过 [Azure 门户]发送推送通知，以便测试此类通知在应用中的接收。

1. 在“故障排除”部分，选择“测试性发送”。  
2. 对于“平台”，请选择“Android”  。 
3. 选择“发送”，发送测试性通知。 
4. 确认在 Android 设备上看到通知消息。

    ![Azure 通知中心 - 测试发送](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

#### <a name="push-notifications-in-the-emulator"></a>模拟器中的推送通知

如果想要在模拟器中测试推送通知，请确保模拟器映像支持你为应用程序选择的 Google API 级别。 如果映像不支持本机 Google API，那么此测试最终以 **SERVICE\_NOT\_AVAILABLE** 异常结束。

另外，请确保已将 Google 帐户添加到运行的模拟器的“设置” > “帐户”下   。 否则，尝试向 GCM 注册可能会导致 **AUTHENTICATION\_FAILED** 异常。

## <a name="optional-send-push-notifications-directly-from-the-app"></a>（可选）直接从应用程序发送推送通知

通常，会使用后端服务器发送通知。 在某些情况下，你可能希望能够直接从客户端应用程序发送推送通知。 本部分说明了如何使用 [Azure 通知中心 REST API](https://msdn.microsoft.com/library/azure/dn223264.aspx)从客户端发送通知。

1. 在 Android Studio 项目视图中，展开 **App** > **src** > **main** > **res** > **layout** 打开 `activity_main.xml` 布局文件，并单击“文本”  选项卡以更新此文件的文本内容。 使用以下代码更新此文件，此代码将添加新的 `Button` 和 `EditText` 控件，用于将推送通知消息发送到通知中心。 将此代码添加到底部紧靠 `</RelativeLayout>`前面的位置。

    ```xml
    <Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/send_button"
    android:id="@+id/sendbutton"
    android:layout_centerVertical="true"
    android:layout_centerHorizontal="true"
    android:onClick="sendNotificationButtonOnClick" />

    <EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/editTextNotificationMessage"
    android:layout_above="@+id/sendbutton"
    android:layout_centerHorizontal="true"
    android:layout_marginBottom="42dp"
    android:hint="@string/notification_message_hint" />
    ```
2. 在 Android Studio 项目视图中，展开 **App** > **src** > **main** > **res** > **values** 打开 `strings.xml` 文件，并添加新的 `Button` 和 `EditText` 控件引用的字符串值。 在文件底部紧靠 `</resources>` 前面的位置添加以下行。

    ```xml
    <string name="send_button">Send Notification</string>
    <string name="notification_message_hint">Enter notification message text</string>
    ```
3. 在 `NotificationSetting.java` 文件中，将以下设置添加到 `NotificationSettings` 类。

    使用中心的 **DefaultFullSharedAccessSignature** 连接字符串更新 `HubFullAccess`。 可从 [Azure 门户]复制此连接字符串，方法是单击通知中心的“设置”页上的“访问策略”   。

    ```java
    public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";
    ```
4. 在 `MainActivity.java` 文件的开头添加以下 `import` 语句。

    ```java
    import java.io.BufferedOutputStream;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.io.OutputStream;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.net.URLEncoder;
    import javax.crypto.Mac;
    import javax.crypto.spec.SecretKeySpec;
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    ```
5. 在 `MainActivity.java` 文件中，在 `MainActivity` 类的最上面添加以下成员。

    ```java
    private String HubEndpoint = null;
    private String HubSasKeyName = null;
    private String HubSasKeyValue = null;
    ```
6. 创建用于对 POST 请求进行身份验证的软件访问签名 (SaS) 令牌，以便将消息发送到通知中心。 分析连接字符串中的密钥数据，然后按照[常见的概念](https://msdn.microsoft.com/library/azure/dn495627.aspx) REST API 参考中所述创建 SaS 令牌。 以下代码是示例实现。

    在 `MainActivity.java` 中，将以下方法添加到 `MainActivity` 类，以分析连接字符串。

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx
        * to parse the connection string so a SaS authentication token can be
        * constructed.
        *
        * @param connectionString This must be the DefaultFullSharedAccess connection
        *                         string for this example.
        */
    private void ParseConnectionString(String connectionString)
    {
        String[] parts = connectionString.split(";");
        if (parts.length != 3)
            throw new RuntimeException("Error parsing connection string: "
                    + connectionString);

        for (int i = 0; i < parts.length; i++) {
            if (parts[i].startsWith("Endpoint")) {
                this.HubEndpoint = "https" + parts[i].substring(11);
            } else if (parts[i].startsWith("SharedAccessKeyName")) {
                this.HubSasKeyName = parts[i].substring(20);
            } else if (parts[i].startsWith("SharedAccessKey")) {
                this.HubSasKeyValue = parts[i].substring(16);
            }
        }
    }
    ```
7. 在 `MainActivity.java` 中，将以下方法添加到 `MainActivity` 类，以创建 SaS 身份验证令牌。

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx to
        * construct a SaS token from the access key to authenticate a request.
        *
        * @param uri The unencoded resource URI string for this operation. The resource
        *            URI is the full URI of the Service Bus resource to which access is
        *            claimed. For example,
        *            "http://<namespace>.servicebus.windows.net/<hubName>"
        */
    private String generateSasToken(String uri) {

        String targetUri;
        String token = null;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Using android.util.Base64 for Android Studio instead of
            // Apache commons codec
            String signature = URLEncoder.encode(
                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

            // Construct authorization string
            token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
        } catch (Exception e) {
            if (isVisible) {
                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
            }
        }

        return token;
    }
    ```
8. 在 `MainActivity.java` 中，将以下方法添加到 `MainActivity` 类，以使用内置的 REST API 处理“发送通知”按钮的点击行为，并将推送通知消息发送到中心  。

    ```java
    /**
        * Send Notification button click handler. This method parses the
        * DefaultFullSharedAccess connection string and generates a SaS token. The
        * token is added to the Authorization header on the POST request to the
        * notification hub. The text in the editTextNotificationMessage control
        * is added as the JSON body for the request to add a GCM message to the hub.
        *
        * @param v
        */
    public void sendNotificationButtonOnClick(View v) {
        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
        final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";

        new Thread()
        {
            public void run()
            {
                try
                {
                    // Based on reference documentation...
                    // https://msdn.microsoft.com/library/azure/dn223273.aspx
                    ParseConnectionString(NotificationSettings.HubFullAccess);
                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                            "/messages/?api-version=2015-01");

                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();

                    try {
                        // POST request
                        urlConnection.setDoOutput(true);

                        // Authenticate the POST request with the SaS token
                        urlConnection.setRequestProperty("Authorization",
                            generateSasToken(url.toString()));

                        // Notification format should be GCM
                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");

                        // Include any tags
                        // Example below targets 3 specific tags
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags",
                        //        "tag1 || tag2 || tag3");

                        // Send notification message
                        urlConnection.setFixedLengthStreamingMode(json.length());
                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                        bodyStream.write(json.getBytes());
                        bodyStream.close();

                        // Get response
                        urlConnection.connect();
                        int responseCode = urlConnection.getResponseCode();
                        if ((responseCode != 200) && (responseCode != 201)) {
                            BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                            String line;
                            StringBuilder builder = new StringBuilder("Send Notification returned " +
                                    responseCode + " : ")  ;
                            while ((line = br.readLine()) != null) {
                                builder.append(line);
                            }

                            ToastNotify(builder.toString());
                        }
                    } finally {
                        urlConnection.disconnect();
                    }
                }
                catch(Exception e)
                {
                    if (isVisible) {
                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                    }
                }
            }
        }.start();
    }
    ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将广播通知发送到所有注册到后端的 Android 设备。 若要了解如何向特定的 Android 设备推送通知，请转到以下教程：  

 > [!div class="nextstepaction"]
 > [向特定设备推送通知](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png
[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png
[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png
[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md 
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure 门户]: https://portal.azure.com
