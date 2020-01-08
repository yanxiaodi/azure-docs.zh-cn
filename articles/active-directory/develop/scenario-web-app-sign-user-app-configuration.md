---
title: 用于登录用户的 Web 应用（代码配置）- Microsoft 标识平台
description: 了解如何构建用于登录用户的 Web 应用（代码配置）
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1453821561ab7bb361fbb3e5d57634cf23a7be2c
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310064"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>用于登录用户的 Web 应用 - 代码配置

了解如何为用于登录用户的 Web 应用配置代码。

## <a name="libraries-used-to-protect-web-apps"></a>库用于保护 Web 应用

<!-- This section can be in an include for Web App and Web APIs -->
用于保护 Web 应用（和 Web API）的库为：

| 平台 | 库 | 描述 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [适用于 .NET 的标识模型扩展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | 在由 ASP.NET 和 ASP.NET Core 直接使用的情况下，适用于 .NET 的 Microsoft 标识扩展提议了一组在 .NET Framework 和 .NET Core 上运行的 DLL。 在 ASP.NET/ASP.NET Core Web 应用中，可以使用 **TokenValidationParameters** 类控制令牌验证（尤其适用于某些 ISV 方案） |
| ![Java](media/sample-v2-code/small_logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | 适用于 Java 的 MSAL-当前为公共预览版 |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | 适用于 Python 的 MSAL-当前为公共预览版 |

选择与你感兴趣的平台相对应的选项卡：

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

本文和以下内容中的代码片段摘自 [ASP.NET Core Web 应用增量教程第 1 章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)。

有关完整的实现的详细信息，请参阅本教程。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

本文中的代码片段和以下代码片段从[ASP.NET Web 应用示例](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)中提取出来

你可能想要参考此示例以获取完整的实现细节。

# <a name="javatabjava"></a>[Java](#tab/java)

本文中的代码片段和以下代码片段从调用 Microsoft graph msal4j web 应用的[Java web 应用程序](https://github.com/Azure-Samples/ms-identity-java-webapp)示例中提取

你可能想要参考此示例以获取完整的实现细节。

# <a name="pythontabpython"></a>[Python](#tab/python)

本文中的代码片段和下面的代码片段从[调用 Microsoft graph MSAL 的 Python web 应用程序](https://github.com/Azure-Samples/ms-identity-python-webapp)中提取出来。Python web 应用示例

你可能想要参考此示例以获取完整的实现细节。

---

## <a name="configuration-files"></a>配置文件

使用 Microsoft 标识平台登录用户的 Web 应用程序通常是通过配置文件配置的。 需填充的设置为：

- `Instance`如果你希望你的应用程序运行（例如，在全国云和云中）
- `tenantId` 中的受众
- 应用程序的 `clientId`，从 Azure 门户中复制。

有时，应用程序可以参数化`authority`，这是`instance`和的串联。`tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core 中，这些设置位于 "AzureAD" 节中的[appsettings](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8)文件中。

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

在 ASP.NET Core 中，有另一个文件[properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) ，其中包含应用`applicationUrl`程序的 URL （）和`sslPort`SSL 端口（），以及各种配置文件。

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

在 Azure 门户中，需要在“身份验证”页中为应用程序注册的回复 URI必须与这些 URL 匹配；也就是说，对于上面的两个配置文件，它们需要是 `https://localhost:44321/signin-oidc`（因为 applicationUrl 是 `http://localhost:3110`），但 `sslPort` 已指定 (44321)，且 `CallbackPath` 为 `/signin-oidc`（在 `appsettings.json` 中定义）。
  
同样，注销 URI 也会设置为`https://localhost:44321/signout-callback-oidc`。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

在 ASP.NET 中，通过[web.config 文件行12-15 配置应用](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15)程序

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

在 Azure 门户中，需要在应用程序的**身份验证**页中注册的答复 uri 需要匹配这些 url;`https://localhost:44326/`即。

# <a name="javatabjava"></a>[Java](#tab/java)

在 Java 中[，该配置](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties)位于位于`src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

在 Azure 门户中，需要在应用程序的**身份验证**页中注册的答复 uri 需要与应用程序定义的 redirectUris 相匹配， `http://localhost:8080/msal4jsample/secure/aad`即`http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

下面是 app_config 中的 Python 配置文件[。 py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> 本快速入门建议在配置文件中存储客户端机密，以便简单起见。 在生产应用中，需要使用其他方法来存储机密（如 KeyVault）或环境变量（如 Flask 的文档所述）： https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>初始化代码

初始化代码因平台而异。 对于 ASP.NET Core 和 ASP.NET，将用户登录到 OpenIDConnect 中间件。 如今，ASP.NET/ASP.NET Core 模板为 Azure AD v2.0 终结点生成 web 应用程序。 因此，需要进行一些配置才能适应 Microsoft 标识平台（v2.0）终结点。 对于 Java，它是通过与应用程序的协作进行弹簧处理的。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

在 ASP.NET Core web 应用（和 web api）中，应用程序受到保护，因为控制器`[Authorize]`或控制器操作上有属性。 此属性检查是否对用户进行了身份验证。 执行应用程序初始化的代码位于`Startup.cs`文件中，若要添加 Microsoft 标识平台（以前称为 Azure AD v2.0）的身份验证，则需要添加以下代码。 代码中的注释应该浅显易懂。

  > [!NOTE]
  > 如果通过 Visual Studio 或 `dotnet new mvc` 使用默认的 ASP.NET Core Web 项目来启动项目，则可默认使用 `AddAzureAD` 方法，因为相关的包会自动加载。
  > 但是，如果从头生成一个项目并尝试使用以下代码，则建议将 NuGet 包“Microsoft.AspNetCore.Authentication.AzureAD.UI”添加到项目，使 `AddAzureAD` 方法可用。
  
以下代码可从启动中获得[。 cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);
  
      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

是在[WebAppServiceCollectionExtensions/.cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)中定义的扩展方法。 `AddMicrosoftIdentityPlatformAuthentication` 以便

- 添加身份验证服务
- 配置用于读取配置文件的选项
- 配置 OpenID connect 选项，使使用的颁发机构为 Microsoft 标识平台（以前称为 Azure AD v2.0）终结点
- 验证令牌的颁发者
- 对应于名称的声明从 ID 令牌中的 "preferred_username" 声明映射 

除了配置外，还可以在调用`AddMicrosoftIdentityPlatformAuthentication`时指定：

- 配置节的名称（默认情况下为 AzureAD）
- 如果你想要跟踪 OpenIdConnect 中间件事件，这些事件可帮助你在身份验证不起作用时对 Web `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents`应用`true`程序进行故障排除：设置为将显示信息的详细信息，ASP.NET Core中间件从 HTTP 响应前进到中`HttpContext.User`用户的标识。

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

`AadIssuerValidator`类允许在许多情况下验证令牌的颁发者（例如，1.0 版或 v2.0 版令牌、单租户应用程序或应用程序，这些应用程序在用户的个人 Microsoft 帐户登录到 Azure 公有云或国内云）。 它可从[Microsoft/Resource/AadIssuerValidator](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

与 ASP.NET Web 应用/Web Api 中的身份验证相关的代码位于[App_Start/](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)文件中。

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java 示例使用弹簧框架。 应用程序受到保护，因为你实现`Filter`了，它会截获每个 HTTP 响应。 在 Java Web 应用快速入门中，此筛选`AuthFilter`器`src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`处于。 该筛选器将处理 OAuth 2.0 授权代码流，因此：

- 验证是否对用户进行了身份`isAuthenticated()`验证（方法）
- 如果用户未通过身份验证，则会计算 Azure AD 授权终结点的 url，并将浏览器重定向到此 URI
- 响应到达时，包含身份验证代码流，让我们 msal4j 获取令牌。
- 最终从令牌终结点接收令牌时（在重定向 URI 上），用户已登录。

有关详细信息， `doFilter()`请参阅[AuthFilter](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)中的方法

> [!NOTE]
> 的代码`doFilter()`以略有不同的顺序编写，但流是所述的。

有关此方法触发的授权代码流的详细信息，请参阅[Microsoft 标识平台和 OAuth 2.0 授权代码流](v2-oauth2-auth-code-flow.md)

# <a name="pythontabpython"></a>[Python](#tab/python)

Python 示例使用 Flask。 Flask 和 MSAL 的初始化。Python 在[py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)中完成。

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>后续步骤

在下一篇文章中，你将学习如何触发登录和注销。

> [!div class="nextstepaction"]
> [登录和注销](scenario-web-app-sign-user-sign-in.md)
