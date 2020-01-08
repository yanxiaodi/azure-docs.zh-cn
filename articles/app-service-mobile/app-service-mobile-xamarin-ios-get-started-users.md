---
title: Xamarin iOS 应用中的移动应用身份验证入门
description: 了解如何使用移动应用通过各种标识提供者（包括 AAD、Google、Facebook、Twitter 和 Microsoft）对 Xamarin iOS 应用的用户进行身份验证。
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: fa1f4bae314025a71568e1e04cbf950ebbe26dbe
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446240"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>向 Xamarin.iOS 应用添加身份验证
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center 投入新和集成服务移动应用开发的核心。 开发人员可以使用**构建**，**测试**并**分发**服务来设置持续集成和交付管道。 应用程序部署后，开发人员可以监视状态和其应用程序使用的使用情况**Analytics**并**诊断**服务，并与用户使用**推送**服务。 开发人员还可以利用**身份验证**其用户进行身份验证并**数据**服务以持久保存并在云中的应用程序数据同步。 请查看[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started-users)今天。
>

## <a name="overview"></a>概述

本主题演示如何从客户端应用程序对应用服务移动应用的用户进行身份验证。 在本教程中，使用应用服务支持的标识提供者向 Xamarin.iOS 快速入门项目添加身份验证。 移动应用成功进行身份验证和授权后，会显示用户 ID 值，该用户能够访问受限制的表数据。

必须先完成教程[创建 Xamarin.iOS 应用]。 如果不使用下载的快速入门服务器项目，必须将身份验证扩展包添加到项目。 有关服务器扩展包的详细信息，请参阅[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>注册应用以进行身份验证并配置应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>将应用添加到允许的外部重定向 URL

安全身份验证要求为应用定义新的 URL 方案。 此方案允许在完成身份验证过程后，身份验证系统重定向到应用。 在本教程中，我们将通篇使用 URL 方案 _appname_。 但是，可以使用所选择的任何 URL 方案。 对于移动应用程序而言，它应是唯一的。 在服务器端启用重定向：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择“应用服务”。

2. 单击“身份验证/授权”  菜单选项。

3. 在“允许的外部重定向 URL”  中，输入 `url_scheme_of_your_app://easyauth.callback`。  此字符串中的 url_scheme_of_your_app 是移动应用程序的 URL 方案  。  它应该遵循协议的正常 URL 规范（仅使用字母和数字，并以字母开头）。  请记下所选的字符串，你将需要在几个地方使用 URL 方案调整移动应用程序代码。

4. 单击“确定”。 

5. 单击“ **保存**”。

## <a name="restrict-permissions-to-authenticated-users"></a>将权限限制给已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* 在 Visual Studio 或 Xamarin Studio 中，运行设备或模拟器中的客户端项目。 验证在应用程序启动后是否引发状态代码为 401（“未授权”）的未处理异常。 失败将记录到调试器的控制台中。 因此，在 Visual Studio 中，应在输出窗口中看到失败。

    发生此未授权失败的原因是应用尝试以未经身份验证的用户身份访问移动应用后端。 *TodoItem* 表现在要求身份验证。

接下来，更新客户端应用，以使用经过身份验证的用户从移动应用后端请求资源。

## <a name="add-authentication-to-the-app"></a>向应用程序添加身份验证
在本部分中，会修改应用程序，以便在显示数据之前显示登录屏幕。 应用启动时，它不会连接到应用服务，并且不会显示任何数据。 用户首次执行刷新笔势后，会显示登录屏幕；成功登录后，会显示 Todo 项列表。

1. 在客户端项目中，打开文件 **QSTodoService.cs**，向 QSTodoService 类添加以下 using 语句和带访问器的 `MobileServiceUser`：

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. 使用以下定义向 **QSTodoService** 添加名为 **Authenticate** 的新方法：

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > 如果使用 Google 以外的标识提供程序，请将以上传递至 LoginAsync  的值更改为以下值之一：MicrosoftAccount、Twitter、Google 或 WindowsAzureActiveDirectory     。

3. 打开 **QSTodoListViewController.cs**。 修改 **ViewDidLoad** 的方法定义，删除接近结尾处对 **RefreshAsync()** 的调用：

    ```csharp
    public override async void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        todoService = QSTodoService.DefaultService;
        await todoService.InitializeStoreAsync();

        RefreshControl.ValueChanged += async (sender, e) => {
            await RefreshAsync();
        }

        // Comment out the call to RefreshAsync
        // await RefreshAsync();
    }
    ```

4. 修改方法 **RefreshAsync**，以便在 **User** 属性为 null 时进行身份验证。 将以下代码添加到方法定义顶部：

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. 打开 AppDelegate.cs，添加以下方法  ：

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. 打开 Info.plist 文件，导航到“高级”部分中的“URL 类型”    。 现在配置 URL 类型的标识符  和 URL 方案  ，然后单击“添加 URL 类型”  。 URL 方案  应与你的 {url_scheme_of_your_app} 相同。
7. 在已连接到 Mac 主机的 Visual Studio 中或在 Visual Studio for Mac 中，针对设备或模拟器运行客户端项目。 验证应用程序是否未显示任何数据。

    通过向下拉动项列表来执行刷新笔势，这会导致显示登录屏幕。 成功输入有效的凭据后，应用会显示待办事项列表，用户可以对数据进行更新。

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[创建 Xamarin.iOS 应用]: app-service-mobile-xamarin-ios-get-started.md
