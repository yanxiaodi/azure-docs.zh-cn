---
title: 通用 Windows 平台注意事项（适用于 .NET 的 Microsoft 身份验证库）| Azure
description: 了解将通用 Windows 平台与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的具体注意事项。
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
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 263264742088a0012ea844946e13cffbab634b29
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532460"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>与 MSAL.NET 配合使用时特定于通用 Windows 平台的注意事项
在 UWP 上, 使用 MSAL.NET 时, 必须考虑几个注意事项。

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 属性
在 WinRT 平台中，`PublicClientApplication` 具有下面的布尔属性 ``UseCorporateNetwork``。 如果用户是使用联合 Azure AD 租户中的帐户登录的, 则通过此属性, Win 8.1 和 UWP 应用程序可以受益于集成的 Windows 身份验证 (因此, SSO 与使用操作系统登录的用户)。 设置此属性时, MSAL.NET 利用 WAB (Web 身份验证代理)。

> [!IMPORTANT]
> 将此属性设置为 true 时，已经假定应用程序开发人员在应用程序中启用了 Windows 集成身份验证 (IWA)。 为此，请执行以下操作：
> - 在适用于 UWP 应用程序的 ``Package.appxmanifest`` 的“功能”选项卡中，启用以下功能：
>   - 企业身份验证
>   - 专用网络(客户端和服务器)
>   - 共享用户证书

默认情况下, IWA 不启用, 因为请求企业身份验证或共享用户证书功能的应用程序要求在 Windows 应用商店中接受较高级别的验证, 而不是所有开发人员都希望执行更高级别的验证级别。

UWP 平台 (WAB) 上的基础实现在启用条件访问的企业方案中无法正常运行。 症状是用户尝试使用 Windows hello 登录, 并建议选择证书, 但是:

- 找不到该 pin 的证书,
- 用户也可以选择它, 但绝不会收到 Pin 提示。

解决方法是使用另一种方法 (用户名/密码 + 电话身份验证), 但体验并不好。

## <a name="troubleshooting"></a>疑难解答

有些客户报告, 在某些特定的企业环境中, 出现以下登录错误:

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

尽管他们知道他们有 internet 连接, 并且可以与公用网络一起工作。

解决方法是确保 WAB (底层 Windows 组件) 允许专用网络。 可以通过设置注册表项来执行此操作:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

有关详细信息, 请参阅[Web 身份验证 Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)。

## <a name="next-steps"></a>后续步骤
以下示例提供了更多详细信息：

样本 | 平台 | 描述 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | 通用 Windows 平台客户端应用程序，它使用 msal.net，访问 Microsoft Graph 来通过 Azure AD v2.0 终结点进行用户身份验证。 <br>![拓扑](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 一个简单的 Xamarin Forms 应用，它展示了如何使用 MSAL 通过 AAD v2.0 终结点对 MSA 和 Azure AD 进行身份验证，以及如何使用生成的令牌访问 Microsoft Graph。 <br>![拓扑](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
