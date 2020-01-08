---
title: 使用 Azure 通知中心向特定用户推送通知 | Microsoft Docs
description: 了解如何使用 Azure 通知中心向特定用户推送通知。
documentationcenter: ios
author: sethm
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 1f7d1410-ef93-4c4b-813b-f075eed20082
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 85461f72d4385805e2aa13691a574a2161036ca5
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212235"
---
# <a name="tutorial-push-notifications-to-specific-users-using-azure-notification-hubs"></a>教程：使用 Azure 通知中心向特定用户推送通知

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

本教程说明如何使用 Azure 通知中心将推送通知发送到特定设备上的特定应用程序用户。 ASP.NET WebAPI 后端用于对客户端进行身份验证并生成通知，如指南主题[从应用后端注册](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)中所述。

在本教程中，我们将执行以下步骤：

> [!div class="checklist"]
> * 创建 WebAPI 项目
> * 在 WebAPI 后端对客户端进行身份验证
> * 使用 WebAPI 后端注册通知
> * 从 WebAPI 后端发送通知
> * 发布新的 WebAPI 后端
> * 修改 iOS 应用
> * 测试应用程序

## <a name="prerequisites"></a>先决条件

本教程假设已根据[通知中心入门 (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) 中所述创建并配置了通知中心。 此外，只有在学习本教程后，才可以学习[安全推送 (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) 教程。
如果要使用移动应用作为后端服务，请参阅[移动应用中的推送通知入门](../app-service-mobile/app-service-mobile-ios-get-started-push.md)。

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>修改 iOS 应用

1. 打开在[通知中心入门 (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) 教程中创建的单页视图应用。

   > [!NOTE]
   > 本节假定项目配置了空的组织名称。 如果未配置，需要在所有类名前面追加组织名称。

2. 在 `Main.storyboard` 文件中，添加屏幕截图中显示的对象库中的组件。

    ![在 Xcode interface builder 中编辑情节提要][1]

   * **用户名**：包含占位符文本“*输入用户名*”的 UITextField，直接位于发送结果标签的下面并受左右边距的限制。
   * **密码**：包含占位符文本“*输入密码*”的 UITextField，直接位于用户名文本字段的下面并受左右边距的限制。 选中属性检查器中“*返回密钥*”下的“**安全文本输入**”选项。
   * **登录**：直接位于密码文本字段下方的标签式 UIButton，并取消选中属性检查器中“控件内容”下的“已启用”选项
   * **WNS**：标签和开关，用于已在中心设置 Windows 通知服务时，启用将通知发送到 Windows 通知服务。 请参阅 [Windows 入门](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)教程。
   * **GCM**：标签和开关，用于已在中心设置 Google Cloud Messaging 时，启用将通知发送到 Google Cloud Messaging。 请参阅 [Android 入门](notification-hubs-android-push-notification-google-gcm-get-started.md)教程。
   * **APNS**：标签和开关，用于启用将通知发送到 Apple 平台通知服务。
   * **收件人用户名**：包含占位符文本“收件人用户名标记”的 UITextField，直接位于 GCM 标签下，受左右边距限制。

     某些组件已在[通知中心入 (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md) 教程中添加。

3. 按 **Ctrl** 的同时从视图中的组件拖至 `ViewController.h` 并添加这些新插座。

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. 在 `ViewController.h` 中，在 import 语句后面添加以下 `#define`。 将 `<Enter Your Backend Endpoint>` 占位符替换为在上一部分中用于部署应用后端的目标 URL。 例如， `http://your_backend.azurewebsites.net` 。

    ```objc
    #define BACKEND_ENDPOINT @"<Enter Your Backend Endpoint>"
    ```

5. 在项目中，创建一个名为 `RegisterClient` 的新 Cocoa Touch 类，以便与你创建的 ASP.NET 后端交互。 创建继承自 `NSObject` 的类。 然后在 `RegisterClient.h` 中添加以下代码。

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. 在 `RegisterClient.m` 中，更新 `@interface` 节：

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. 将 RegisterClient.m 中的 `@implementation` 节替换为以下代码：

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    此代码使用 NSURLSession 对应用后端执行 REST 调用并使用 NSUserDefaults 在本地存储通知中心返回的 registrationId，实现了指南文章[从应用后端注册](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)中所述的逻辑。

    该类需要设置其属性 `authorizationHeader`，才能正常工作。 登录后，由 `ViewController` 类设置此属性。

8. 在 `ViewController.h` 中，为 `RegisterClient.h` 添加一个 `#import` 语句。 然后在 `@interface` 部分中添加设备令牌的声明以及对 `RegisterClient` 实例的引用：

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. 在 ViewController.m 的 `@interface` 中添加私有方法声明：

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > 下面的代码片段不是安全的身份验证方案，应将 `createAndSetAuthenticationHeaderWithUsername:AndPassword:` 的实现替换为特定身份验证机制，该机制将生成要供注册客户端类（例如，OAuth、Active Directory）使用的身份验证令牌。

10. 然后在 `ViewController.m` 的 `@implementation` 部分中添加以下代码，这段代码会添加用于设置设备令牌和身份验证标头的实现。

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    请注意设置设备令牌时如何启用登录按钮。 这是因为在登录操作过程中，视图控制器将使用应用后端注册推送通知。 因此，在正确设置设备令牌前，系统不希望出现登录操作。 只要登录操作发生在推送注册前，即可分离这两个操作。

11. 在 ViewController.m 中，使用以下代码段实现“**登录**”按钮的操作方法以及使用 ASP.NET 后端发送通知消息的方法。

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. 更新“**发送通知**”按钮的操作以使用 ASP.NET 后端，发送开关启用的任何 PNS。

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. 在 `ViewDidLoad` 函数中，添加以下内容来实例化 `RegisterClient` 实例并设置文本字段的委托。

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. 现在，在 `AppDelegate.m` 中，删除 `application:didRegisterForPushNotificationWithDeviceToken:` 方法的所有内容并将其替换为以下内容（以确保视图控制器包含从 APN 中检索到的最新设备令牌）：

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. 最后，在 `AppDelegate.m` 中，确保使用了以下方法：

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>测试应用程序

1. 在 XCode 中，在物理 iOS 设备上运行此应用（推送通知无法在模拟器中正常工作）。
2. 在 iOS 应用 UI 中，为用户名和密码输入相同的值。 然后单击“登录”。

    ![iOS 测试应用程序][2]

3. 应看到弹出窗口通知你注册成功。 单击 **“确定”** 。

    ![显示的 iOS 测试通知][3]

4. 在*“*收件人用户名标记*”文本字段中，输入用于从另一台设备注册的用户名标记。
5. 输入通知消息，并单击“**发送通知**”。 只有使用该用户名标记注册的设备才会收到通知消息。 该消息将只发送给那些用户。

    ![iOS 测试带标记的通知][4]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何向其标记与注册相关联的特定用户推送通知。 若要了解如何推送基于位置的通知，请转到以下教程： 

> [!div class="nextstepaction"]
>[推送基于位置的通知](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
