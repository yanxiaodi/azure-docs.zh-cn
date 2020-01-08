---
title: 身份验证流（Microsoft 身份验证库）| Azure
description: 了解 Microsoft 身份验证库 (MSAL) 使用的身份验证流和授权。
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
ms.date: 04/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd932d2b11c61c380638a1a95f8da357d0c62e3
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532998"
---
# <a name="authentication-flows"></a>身份验证流

本文介绍 Microsoft 身份验证库 (MSAL) 提供的不同身份验证流。  可在各种不同的应用程序方案中使用这些流。

| 流向 | 描述 | 使用位置|  
| ---- | ----------- | ------- | 
| [交互式](#interactive) | 通过交互进程获取标记, 该进程通过浏览器或弹出窗口提示用户提供凭据。 | [桌面应用](scenario-desktop-overview.md)、[移动应用](scenario-mobile-overview.md) |
| [隐式授权](#implicit-grant) | 允许应用获取令牌, 而无需执行后端服务器凭据交换。 这允许应用在客户端 JavaScript 代码中登录用户、维护会话并获取其他 web Api 的令牌。| [单页应用程序 (SPA)](scenario-spa-overview.md) |
| [授权代码](#authorization-code) | 在设备上安装的应用中使用，以访问受保护的资源，例如 Web API。 这使你可以向移动应用和桌面应用添加登录和 API 访问权限。 | [桌面应用](scenario-desktop-overview.md)、[移动应用](scenario-mobile-overview.md)、 [web 应用](scenario-web-app-call-api-overview.md) | 
| [代理](#on-behalf-of) | 应用程序调用服务或 web API, 而后者又需要调用另一个服务或 web API。 思路是通过请求链传播委托用户标识和权限。 | [Web API](scenario-web-api-call-api-overview.md) |
| [客户端凭据](#client-credentials) | 允许你使用应用程序的标识访问 Web 托管的资源。 通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 | [守护程序应用](scenario-daemon-overview.md) |
| [设备代码](#device-code) | 允许用户登录到智能电视、IoT 设备或打印机等输入受限的设备。 | [桌面/移动应用](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Windows 集成身份验证](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 允许域或 Azure Active Directory (Azure AD) 的已加入计算机上的应用程序以无提示方式获取令牌 (无需用户的 UI 交互)。| [桌面/移动应用](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [用户名/密码](scenario-desktop-acquire-token.md#username--password) | 允许应用程序通过直接处理用户的密码将用户登录。 不建议使用此流。 | [桌面/移动应用](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>交互式
MSAL 支持以交互方式提示用户输入其凭据进行登录, 并使用这些凭据获取令牌。

![交互式流关系图](media/msal-authentication-flows/interactive.png)

有关使用 MSAL.NET 以交互方式获取特定平台上的令牌的详细信息, 请参阅:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [通用 Windows 平台](msal-net-uwp-considerations.md)

有关 MSAL 中交互式调用的详细信息, 请参阅[MSAL 交互式请求中的提示行为](msal-js-prompt-behavior.md)。

## <a name="implicit-grant"></a>隐式授权

MSAL 支持[OAuth 2 隐式授权流](v2-oauth2-implicit-grant-flow.md), 这允许应用程序从 Microsoft 标识平台获取令牌, 而无需执行后端服务器凭据交换。 这允许应用在客户端 JavaScript 代码中登录用户、维护会话并获取其他 web Api 的令牌。

![隐式授权流关系图](media/msal-authentication-flows/implicit-grant.svg)

许多新式 web 应用程序构建为使用 JavaScript 或 SPA 框架 (如角度、Vue 和响应) 编写的客户端、单页应用程序。 这些应用程序在 web 浏览器中运行, 并具有不同于传统服务器端 web 应用程序的身份验证特征。 Microsoft 标识平台允许单页面应用程序使用隐式授予流登录用户, 并获取用于访问后端服务或 web Api 的令牌。 隐式流允许应用程序获取 ID 令牌来表示经过身份验证的用户, 还可以访问调用受保护的 Api 所需的令牌。

此身份验证流不包括使用跨平台 JavaScript 框架 (如 Electron 和响应本机) 的应用程序方案, 因为它们需要更多的功能来与本机平台交互。

## <a name="authorization-code"></a>授权代码
MSAL 支持[OAuth 2 授权代码授予](v2-oauth2-auth-code-flow.md)。 此授予可用于在设备上安装的应用, 以获取对受保护资源 (例如 web Api) 的访问权限。 这使你可以向移动应用和桌面应用添加登录和 API 访问权限。 

当用户登录到 web 应用程序 (网站) 时, web 应用程序将收到授权代码。  兑换该授权代码可获取用于调用 Web API 的令牌。 在 ASP.NET 和 ASP.NET Core web apps 中, 唯一的`AcquireTokenByAuthorizationCode`目标是向令牌缓存添加令牌。 然后, 应用程序可以使用该令牌 (通常是在控制器中, 这只是通过使用`AcquireTokenSilent`获取 API 的令牌)。

![授权代码流示意图](media/msal-authentication-flows/authorization-code.png)

在上图中, 应用程序:

1. 请求用于兑换访问令牌的授权代码。
2. 使用访问令牌调用 Web API。

### <a name="considerations"></a>注意事项
- 仅可使用授权代码一次来兑换令牌。 不要尝试使用相同的授权代码多次获取令牌 (协议标准规范明确禁止使用该令牌)。 如果你有意兑换代码几次, 或者你不知道某个框架还能为你执行此操作, 你将收到以下错误:`AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- 如果你正在编写 ASP.NET 或 ASP.NET Core 应用程序, 如果你不知道已经兑换了授权代码, 则可能会发生这种情况。 为此, 需要调用`context.HandleCodeRedemption()` `AuthorizationCodeReceived`事件处理程序的方法。

- 避免与 ASP.NET 共享访问令牌，否则可能无法正常进行增量许可。 有关详细信息，请参阅[问题 #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)。

## <a name="on-behalf-of"></a>代理

MSAL 支持 [OAuth 2 代理身份验证流](v2-oauth2-on-behalf-of-flow.md)。  当应用程序调用服务或 web API 时使用此流, 后者又需要调用另一个服务或 web API。 思路是通过请求链传播委托用户标识和权限。 要使中间层服务向下游服务发出身份验证请求，该服务需要代表用户保护 Microsoft 标识平台提供的访问令牌。

![代表流的关系图](media/msal-authentication-flows/on-behalf-of.png)

在上图中:

1. 应用程序获取 web API 的访问令牌。
2. 客户端 (web、桌面、移动或单页应用程序) 调用受保护的 web API, 并将访问令牌作为持有者令牌添加到 HTTP 请求的身份验证标头中。 Web API 对用户进行身份验证。
3. 当客户端调用 web API 时, web API 代表用户请求另一个令牌。  
4. 受保护的 web API 使用此令牌代表用户调用下游 web API。  然后, web API 还可以为其他下游 Api (但仍代表同一个用户) 请求令牌。

## <a name="client-credentials"></a>客户端凭据

MSAL 支持 [OAuth 2 客户端凭据流](v2-oauth2-client-creds-grant-flow.md)。 此流允许你使用应用程序的标识访问 Web 托管的资源。 这种授予通常用于必须在后台运行的服务器间交互，不需要立即与用户交互。 此类应用程序通常称为守护程序或服务帐户。 

客户端凭据授权流允许 web 服务 (机密客户端) 在调用其他 web 服务时使用其自己的凭据 (而不是模拟用户) 进行身份验证。 在此方案中, 客户端通常是中间层 web 服务、后台程序服务或网站。 为了进行更高级别的保证，Microsoft 标识平台还允许调用服务将证书（而不是共享机密）用作凭据。

> [!NOTE]
> 机密客户端流在移动平台 (UWP、Xamarin 和 Xamarin) 上不可用, 因为它们仅支持公共客户端应用程序。 公共客户端应用程序不知道如何向标识提供者证明应用程序的身份。 通过部署证书, 可以在 web 应用或 web API 后端实现安全连接。

MSAL.NET 支持两种类型的客户端凭据。 这些客户端凭据需要注册到 Azure AD。 凭据将传入代码中机密客户端应用程序的构造函数。

### <a name="application-secrets"></a>应用程序密钥 

![具有密码的机密客户端示意图](media/msal-authentication-flows/confidential-client-password.png)

在上图中, 应用程序:

1. 使用应用程序机密或密码凭据获取令牌。
2. 使用该令牌发出资源请求。

### <a name="certificates"></a>证书 

![证书的机密客户端示意图](media/msal-authentication-flows/confidential-client-certificate.png)

在上图中, 应用程序:

1. 使用证书凭据获取令牌。
2. 使用该令牌发出资源请求。

这些客户端凭据需要：
- 注册到 Azure AD。
- 传入代码中机密客户端应用程序的构造。


## <a name="device-code"></a>设备代码
MSAL 支持[OAuth 2 设备代码流](v2-oauth2-device-code.md), 这允许用户登录到输入限制的设备, 如智能电视、IoT 设备或打印机。 使用 Azure AD 的交互式身份验证需要 Web 浏览器。 设备代码流允许用户使用另一台设备 (例如, 另一台计算机或移动电话) 以交互方式登录, 其中设备或操作系统不提供 web 浏览器。

通过使用设备代码流, 应用程序将通过两个步骤来获取令牌, 此过程尤其适用于这些设备或操作系统。 此类应用程序的示例包括在 IoT 设备上运行的应用程序或命令行工具 (CLI)。 

![设备代码流示意图](media/msal-authentication-flows/device-code.png)

在上图中:

1. 只要需要用户身份验证, 应用程序就会提供代码, 并要求用户使用其他设备 (如连接 internet 的智能手机) 来访问 URL (例如, https://microsoft.com/devicelogin) 。 然后, 将提示用户输入代码, 并通过正常的身份验证体验 (如有必要) 进行身份验证。

2. 身份验证成功后, 命令行应用通过后通道接收所需的令牌, 并使用它们来执行所需的 web API 调用。

### <a name="constraints"></a>约束

- 设备代码流仅适用于公共客户端应用程序。
- 构造公用客户端应用程序时传入的颁发机构必须是以下各项之一:
  - 租户 (格式`https://login.microsoftonline.com/{tenant}/`为, 其中`{tenant}`是表示租户 ID 的 GUID 或与租户关联的域)。
  - 适用于任何工作和学校帐户`https://login.microsoftonline.com/organizations/`()。
- Azure AD v2.0 终结点尚不支持 Microsoft 个人帐户 (不能使用`/common`或`/consumers`租户)。

## <a name="integrated-windows-authentication"></a>集成 Windows 身份验证
对于已加入域或已加入 Azure AD 的 Windows 计算机上运行的桌面或移动应用程序，MSAL 支持 Windows 集成身份验证 (IWA)。 这些应用程序可以使用 IWA 以静默方式获取令牌（无需用户进行任何 UI 交互）。 

![集成的 Windows 身份验证示意图](media/msal-authentication-flows/integrated-windows-authentication.png)

在上图中, 应用程序:

1. 使用集成 Windows 身份验证获取令牌。
2. 使用该令牌发出资源请求。

### <a name="constraints"></a>约束

IWA 仅支持联合用户, 这意味着在 Active Directory 中创建的用户以及 Azure AD 的支持用户。 直接在 Azure AD 中创建的用户 (不 Active Directory 支持) 无法使用此身份验证流。 此限制不会影响[用户名/密码流](#usernamepassword)。

IWA 适用于针对 .NET Framework、.NET Core 和通用 Windows 平台编写的应用。

IWA 不会绕过多重身份验证。 如果配置了多重身份验证, 则当需要多重身份验证质询时, IWA 可能会失败。 多重身份验证需要用户交互。

不控制标识提供者请求执行双重身份验证的时间。 租户管理员将执行。 通常, 当你从不同的国家/地区进行身份验证时, 如果不是通过 VPN 连接到公司网络, 甚至是通过 VPN 进行连接时, 都需要双重身份验证。 Azure AD 使用 AI 持续了解是否需要双因素身份验证。 如果 IWA 失败, 则应回退到 [交互用户提示] (#interactive)。

构造公用客户端应用程序时传入的颁发机构必须是以下各项之一:
- 租户 (格式`https://login.microsoftonline.com/{tenant}/`为, 其中`tenant`是表示租户 ID 的 guid 或与租户关联的域)。
- 适用于任何工作和学校帐户`https://login.microsoftonline.com/organizations/`()。 不支持 Microsoft 个人帐户 (你不能使用`/common`或`/consumers`租户)。

由于 IWA 是一个静默流, 因此必须满足以下条件之一:
- 应用程序的用户必须事先同意使用该应用程序。 
- 租户管理员之前必须同意租户中的所有用户使用该应用程序。

这意味着满足以下条件之一:
- 你作为开发人员选择了对 Azure 门户的**授权**。
- 租户管理员已在应用程序注册的 " **API 权限**" 选项卡中选择了 "**授予/撤消管理员许可**" (请参阅[添加访问 web api 的权限](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis))。
- 你为用户提供了同意应用程序的方式 (请参阅[请求单个用户同意](v2-permissions-and-consent.md#requesting-individual-user-consent))。
- 你为租户管理员提供了向应用程序授予许可的方式 (请参阅[管理员许可](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))。

已针对 .NET Desktop、.NET Core 和 Windows 通用平台应用启用 IWA 流。 在 .NET Core 中, 只有带用户名的重载可用。 .NET Core 平台无法向操作系统询问用户名。
  
有关许可的详细信息，请参阅 [v2.0 权限和许可](v2-permissions-and-consent.md)。

## <a name="usernamepassword"></a>用户名/密码 
MSAL 支持 [OAuth 2 资源所有者密码凭据授予](v2-oauth-ropc.md)，后者允许应用程序通过直接处理用户的密码来登录用户。 在桌面应用程序中，可以使用用户名/密码流以静默方式获取令牌。 使用应用程序时无需 UI。

![用户名/密码流示意图](media/msal-authentication-flows/username-password.png)

在上图中, 应用程序:

1. 通过将用户名和密码发送到标识提供者来获取令牌。
2. 使用令牌来调用 web API。

> [!WARNING]
> 不建议使用此流。 它需要高度的信任和用户公开。  仅当无法使用其他更安全的流时，才使用此流。 有关详细信息, 请参阅[不断增长的密码问题的解决方案？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

在已加入域的 Windows 计算机上以无提示方式获取令牌的首选流程是[集成的 Windows 身份验证](#integrated-windows-authentication)。 否则, 还可以使用[设备代码流](#device-code)。

尽管这在某些情况下 (DevOps 方案) 很有用, 但如果你想要在提供自己的 UI 的交互方案中使用用户名/密码, 请尝试避免出现这种情况。 使用用户名/密码:
- 需要进行多重身份验证的用户将无法登录 (因为没有交互)。
- 用户无法执行单一登录。

### <a name="constraints"></a>约束

除了 [Windows 集成身份验证约束](#integrated-windows-authentication)以外，以下约束也适用：

- 用户名/密码流与条件性访问和多重身份验证不兼容。 因此，如果应用在 Azure AD 租户中运行，而该租户中的租户管理员需要多重身份验证，则你无法使用此流。 许多组织都会提出这种要求。
- 它仅适用于工作和学校帐户 (而不是 Microsoft 帐户)。
- 流在 .NET desktop 和 .NET Core 中可用, 但在通用 Windows 平台上不可用。

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 细节

有关使用 MSAL.NET 和 Azure AD B2C 的详细信息, 请参阅将[ROPC 与 Azure AD B2C 结合使用 (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)。
