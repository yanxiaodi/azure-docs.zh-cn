---
title: Xamarin Android 注意事项（适用于 .NET 的 Microsoft 身份验证库）| Azure
description: 了解将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的具体注意事项。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 465902cf6ef6db1d867f7cc986da8c9e06e4fbbf
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532474"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>与 MSAL.NET 配合使用时特定于 Xamarin Android 的注意事项
本文介绍将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的具体注意事项。

## <a name="set-the-parent-activity"></a>设置父活动

需在 Xamarin.Android 上设置父活动，以便交互发生后返回令牌。

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
还可以通过回调在 PublicClientApplication 级别 (在 MSAL 4.2 + 中) 设置此项。

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

建议在[此处](https://github.com/jamesmontemagno/CurrentActivityPlugin)使用 CurrentActivityPlugin。  然后, PublicClientApplication 生成器代码将如下所示:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>确保身份验证流的交互部分结束后控制返回到 MSAL。
在 Android 上，需重写 `Activity` 的 `OnActivityResult` 方法并调用 AuthenticationContinuationHelper MSAL 类的 SetAuthenticationContinuationEventArgs 方法。

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
该行确保在身份验证流的交互部分结束后控制会返回到 MSAL。

## <a name="update-the-android-manifest"></a>更新 Android 清单
`AndroidManifest.xml` 应包含以下值：
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>使用嵌入式 Web 视图（可选）

默认情况下, MSAL.NET 使用系统 web 浏览器, 可让你获取 Web 应用程序和其他应用的 SSO。 在某些罕见情况下，可能需要指定你需要使用嵌入式 Web 视图。 有关详细信息，请参阅 [MSAL.NET 使用 Web 浏览器](msal-net-web-browsers.md)和 [Android 系统浏览器](msal-net-system-browser-android-considerations.md)。

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>疑难解答
如果创建新的 Xamarin.Forms 应用程序并添加对 MSAL.Net NuGet 包的引用，则这会刚好适用。
但是，如果需要将现有的 Xamarin.Forms 应用程序升级到 MSAL.NET 预览版 1.1.2 或更高版本，则可能会遇到内部版本问题。

若要排查这些问题，应执行以下操作：
- 将现有的 MSAL.NET NuGet 包更新到 MSAL.NET 预览版 1.1.2 或更新版本
- 检查 Xamarin.Forms 是否已自动更新到版本 2.5.0.122203（否则请更新到此版本）
- 检查 Xamarin.Android.Support.v4 是否已自动更新到版本 25.4.0.2（否则请更新到此版本）
- 所有 Xamarin.Android.Support 包应该都以版本 25.4.0.2 为目标
- 清除/重新生成
- 尝试在 Visual Studio 中将最大并行项目内部版本数设置为 1（“选项”->“项目和解决方案”->“生成并运行”->“最大并行项目内部版本数”）
- 或者, 如果要从命令行生成, 请尝试从命令中删除/m (如果使用)。


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>错误：名称 'AuthenticationContinuationHelper' 不存在于当前上下文中

这可能是因为 Visual Studio 未能正确更新 Android * 文件。 有时,  **\<HintPath >** filepath 不正确地包含 netstandard13 而不是**monoandroid90**。

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>后续步骤

如需更多详细信息和示例，请参阅以下示例的 readme.md 文件的[特定于 Android 的注意事项](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)段落：

| 样本 | 平台 | 描述 |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 一个简单的 Xamarin Forms 应用，它展示了如何使用 MSAL 通过 AADD v2.0 终结点对 MSA 和 Azure AD 进行身份验证，以及如何使用生成的令牌访问 Microsoft Graph。 <br>![拓扑](media/msal-net-xamarin-android-considerations/topology.png) |