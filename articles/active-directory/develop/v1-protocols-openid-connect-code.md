---
title: 了解 Azure AD 中的 OpenID Connect 授权代码流 | Microsoft Docs
description: 本文介绍如何使用 Azure Active Directory 和 OpenID Connect，通过 HTTP 消息来授权访问租户中的 Web 应用程序和 Web API。
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 29142f7e-d862-4076-9a1a-ecae5bcd9d9b
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c2e80f80ea5d7e7d5ee26eee8b26506386a6e2f
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389792"
---
# <a name="authorize-access-to-web-applications-using-openid-connect-and-azure-active-directory"></a>使用 OpenID Connect 和 Azure Active Directory 来授权访问 Web 应用程序

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) 是构建在 OAuth 2.0 协议顶层的简单标识层。 OAuth 2.0 定义了一些机制用于获取和使用[**访问令牌**](access-tokens.md)来访问受保护资源，但未定义用于提供标识信息的标准方法。 OpenID Connect 实现身份验证，作为对 OAuth 2.0 授权过程的扩展。 它以 [`id_token`](id-tokens.md) 的形式提供有关最终用户的信息，可验证用户的标识，并提供有关用户的基本配置文件信息。

如果要构建的 Web 应用程序托管在服务器中并通过浏览器访问，我们建议使用 OpenID Connect。


[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)] 

## <a name="authentication-flow-using-openid-connect"></a>使用 OpenID Connect 的身份验证流

最基本的登录流包含以下步骤 - 下面详细描述了每个步骤。

![OpenID Connect 身份验证流](./media/v1-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)

## <a name="openid-connect-metadata-document"></a>OpenID Connect 元数据文档

OpenID Connect 描述了元数据文档，该文档包含了应用执行登录所需的大部分信息。 这包括要使用的 URL 和服务的公共签名密钥的位置等信息。 OpenID Connect 元数据文档可以在以下位置找到：

```
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration
```
元数据是简单的 JavaScript 对象表示法 (JSON) 文档。 有关示例，请参阅下面的代码段。 [OpenID Connect 规范](https://openid.net)对该代码段的内容进行了完整描述。 请注意，提供租户 ID 而不是用 `common` 代替上面的 {tenant} 将导致在 JSON 对象中返回特定于租户的 URI。

```
{
    "authorization_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/{tenant}/oauth2/token",
    "token_endpoint_auth_methods_supported":
    [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys"
    "userinfo_endpoint":"https://login.microsoftonline.com/{tenant}/openid/userinfo",
    ...
}
```

如果应用因使用[声明映射](active-directory-claims-mapping.md)功能而具有自定义签名密钥，则必须追加包含应用 ID 的 `appid` 查询参数，以获取指向应用的签名密钥信息的 `jwks_uri`。 例如：`https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 包含 `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e` 的 `jwks_uri`。

## <a name="send-the-sign-in-request"></a>发送登录请求

当 Web 应用程序需要对用户进行身份验证时，必须将用户定向到 `/authorize` 终结点。 此请求类似于 [OAuth 2.0 授权代码流](v1-protocols-oauth-code.md)的第一个阶段，不过有几个重要的区别：

* 该请求必须在 `scope` 参数中包含范围 `openid`。
* `response_type` 参数必须包含 `id_token`。
* 请求必须包含 `nonce` 参数。

下面是一个示例请求：

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%3a12345
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| 参数 |  | 描述 |
| --- | --- | --- |
| 租户 |必需 |请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 独立于租户的令牌的允许值为租户标识符，例如 `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`、`contoso.onmicrosoft.com` 或 `common` |
| client_id |必需 |将应用注册到 Azure AD 时，分配给应用的应用程序 ID。 可以在 Azure 门户中找到该值。 依次单击“Azure Active Directory”和“应用注册”，选择应用程序并在应用程序页上找到应用程序 ID。 |
| response_type |必需 |必须包含 OpenID Connect 登录的 `id_token`。 还可以包含其他 response_type，例如 `code` 或 `token`。 |
| 范围 | 建议 | OpenID Connect 规范要求范围 `openid`，该范围在许可 UI 中会转换为“将你登录”权限。 在 v1.0 终结点上，此范围和其他 OIDC 范围会被忽略，但对符合标准的客户端而言仍是最佳做法。 |
| nonce |必需 |由应用程序生成且包含在请求中的值，以声明方式包含在生成的 `id_token` 中。 应用程序接着便可确认此值，以减少令牌重新执行攻击。 此值通常是随机的唯一字符串或 GUID，可用以识别请求的来源。 |
| redirect_uri | 建议 |应用的 redirect_uri，应用可向其发送及从其接收身份验证响应。 其必须完全符合在门户中注册的其中一个 redirect_uris，否则必须是编码的 url。 如果缺失，则会将用户代理随机发送回某个为应用注册的重定向 URI。 最大长度为 255 字节 |
| response_mode |可选 |指定将生成的 authorization_code 送回到应用程序所应该使用的方法。 HTTP 窗体发布支持的值为 `form_post`，URL 片段支持的值为 `fragment`。 对于 Web 应用程序，建议使用 `response_mode=form_post`，确保以最安全的方式将令牌传输到应用程序。 包含 id_token 的任何流的默认值为 `fragment`。|
| 省/自治区/直辖市 |建议 |随令牌响应返回的请求中所包含的值。 可以是想要的任何内容的字符串。 随机生成的唯一值通常用于[防止跨站点请求伪造攻击](https://tools.ietf.org/html/rfc6749#section-10.12)。 该 state 也用于在身份验证请求出现之前，于应用中编码用户的状态信息，例如之前所在的网页或视图。 |
| prompt |可选 |表示需要的用户交互类型。 当前唯一有效的值为“login”、“none”和“consent”。 `prompt=login` 强制用户在该请求上输入其凭据，从而使单一登录无效。 `prompt=none` 完全相反，它会确保无论如何都不会向用户显示任何交互提示。 如果请求无法通过单一登录静默完成，则终结点将返回一个错误。 `prompt=consent` 在用户登录后触发 OAuth 同意对话框，要求用户向应用授予权限。 |
| login_hint |可选 |如果事先知道其用户名称，可用于预先填充用户登录页面的用户名称/电子邮件地址字段。 通常，应用在重新身份验证期间使用此参数，并且已经使用 `preferred_username` 声明从前次登录提取用户名。 |

此时，系统会要求用户输入凭据并完成身份验证。

### <a name="sample-response"></a>示例响应

在用户进行身份验证后发送到登录请求中指定的 `redirect_uri` 的示例响应可能如下所示：

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| 参数 | 描述 |
| --- | --- |
| id_token |应用请求的 `id_token`。 可以使用 `id_token` 验证用户的标识，并以用户身份开始会话。 |
| 省/自治区/直辖市 |同时随令牌响应返回的请求中所包含的值。 随机生成的唯一值通常用于 [防止跨站点请求伪造攻击](https://tools.ietf.org/html/rfc6749#section-10.12)。 该状态也用于在身份验证请求出现之前，于应用程序中编码用户的状态信息，例如之前所在的网页或视图。 |

### <a name="error-response"></a>错误响应

错误响应可能也发送到 `redirect_uri`，让应用可以适当地处理：

```
POST / HTTP/1.1
Host: localhost:12345
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 参数 | 描述 |
| --- | --- |
| 错误 |用于分类发生的错误类型与响应错误的错误码字符串。 |
| error_description |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>授权终结点错误的错误代码

下表描述了可在错误响应的 `error` 参数中返回的各个错误代码。

| 错误代码 | 描述 | 客户端操作 |
| --- | --- | --- |
| invalid_request |协议错误，例如，缺少必需的参数。 |修复并重新提交请求。 这通常是在初始测试期间捕获的开发错误。 |
| unauthorized_client |不允许客户端应用程序请求授权代码。 |客户端应用程序未注册到 Azure AD 中或者未添加到用户的 Azure AD 租户时，通常会出现这种情况。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| access_denied |资源所有者拒绝了许可 |客户端应用程序可以通知用户除非用户许可，否则无法继续。 |
| unsupported_response_type |授权服务器不支持请求中的响应类型。 |修复并重新提交请求。 这通常是在初始测试期间捕获的开发错误。 |
| server_error |服务器遇到意外的错误。 |重试请求。 这些错误可能是临时状况导致的。 客户端应用程序可能向用户说明，其响应由于临时错误而延迟。 |
| temporarily_unavailable |服务器暂时繁忙，无法处理请求。 |重试请求。 客户端应用程序可能向用户说明，其响应由于临时状况而延迟。 |
| invalid_resource |目标资源无效，原因是它不存在，Azure AD 找不到它，或者未正确配置。 |这表示未在租户中配置该资源（如果存在）。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |

## <a name="validate-the-id_token"></a>验证 id_token

仅接收 `id_token` 不足以对用户进行身份验证，必须验证签名，并按照应用的要求验证 `id_token` 中的声明。 Azure AD 终结点使用 JSON Web 令牌 (JWT) 和公钥加密对令牌进行签名并验证其是否有效。

可以选择验证客户端代码中的 `id_token`，但是常见的做法是将 `id_token` 发送到后端服务器，并在那里执行验证。 

可能还希望根据自己的方案验证其他声明。 一些常见的验证包括：

* 确保用户/组织已注册应用。
* 使用 `wids` 或 `roles` 声明，确保用户拥有正确的授权/权限。 
* 确保身份验证具有一定的强度，例如多重身份验证。

验证 `id_token` 后，即可开始与用户的会话，并使用 `id_token` 中的声明来获取应用中的用户相关信息。 此信息可用于显示、记录和个性化等。有关 `id_tokens` 和声明的详细信息，请阅读 [AAD id_tokens](id-tokens.md)。

## <a name="send-a-sign-out-request"></a>发送注销请求

如果希望用户从应用中注销，仅仅是清除应用的 Cookie 或结束用户会话并不足够。 还必须将用户重定向到 `end_session_endpoint` 才能注销。如果不这样做，用户可能不需要再次输入凭据就能重新通过应用的身份验证，因为他们与 Azure AD 终结点之间仍然存在有效的单一登录会话。

只需将用户重定向到 OpenID Connect 元数据文档中所列的 `end_session_endpoint` ：

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| 参数 |  | 描述 |
| --- | --- | --- |
| post_logout_redirect_uri |建议 |用户在成功注销后应重定向到的 URL。此 URL 必须与在应用注册门户中为应用程序注册的重定向 URI 之一匹配。  如果不包含*post_logout_redirect_uri* ，则会向用户显示一般消息。 |

## <a name="single-sign-out"></a>单一登录

将用户重定向到 `end_session_endpoint` 时，Azure AD 将从浏览器中清除用户的会话。 但是，用户可能仍登录到其他使用 Azure AD 进行身份验证的应用程序。 要使这些应用程序能够同时注销用户，Azure AD 会将 HTTP GET 请求发送到用户当前登录到的所有应用程序的已注册 `LogoutUrl`。 应用程序必须通过清除任何标识用户的会话并返回 `200` 响应来响应此请求。 如果要在应用程序中支持单一注销，必须在应用程序代码中实现此类 `LogoutUrl`。 可以从 Azure 门户设置 `LogoutUrl`：

1. 导航到 [Azure 门户](https://portal.azure.com)。
2. 通过单击页面右上角的帐户选择 Active Directory。
3. 从左侧导航面板中，选择“Azure Active Directory”，选择“应用注册”，并选择应用程序。
4. 单击“设置”和“属性”，并查找“注销 URL”文本框。 

## <a name="token-acquisition"></a>令牌获取
许多 Web 应用不仅需要将用户登录，而且还要代表该用户使用 OAuth 来访问 Web 服务。 此方案合并了用于对用户进行身份验证的 OpenID Connect，同时将获取 `authorization_code`，可用于通过 [OAuth 授权代码流](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)来获取 `access_tokens`。

## <a name="get-access-tokens"></a>获取访问令牌
若要获取访问令牌，需要修改上述登录请求：

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e        // Your registered Application ID
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%3a12345          // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // `form_post' or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F        // The identifier of the protected resource (web API) that your application needs access to
&state=12345                                          // Any value, provided by your app
&nonce=678910                                         // Any value, provided by your app
```

通过在请求中包含权限范围并使用 `response_type=code+id_token`，`authorize` 终结点可确保用户已经同意 `scope` 查询参数中指示的权限，并且将授权代码返回到应用以交换访问令牌。

### <a name="successful-response"></a>成功的响应

使用 `response_mode=form_post` 发送到 `redirect_uri` 的成功响应如下所示：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| 参数 | 描述 |
| --- | --- |
| id_token |应用请求的 `id_token`。 可以使用 `id_token` 验证用户的标识，并以用户身份开始会话。 |
| code |应用程序请求的 authorization_code。 应用程序可以使用授权代码请求目标资源的访问令牌。 Authorization_codes 的生存期较短，通常约 10 分钟后即过期。 |
| 省/自治区/直辖市 |如果请求中包含状态参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的 state 值是否完全相同。 |

### <a name="error-response"></a>错误响应

错误响应可能也发送到 `redirect_uri`，让应用可以适当地处理：

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| 参数 | 描述 |
| --- | --- |
| 错误 |用于分类发生的错误类型与响应错误的错误码字符串。 |
| error_description |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

有关可能的错误代码的描述及其建议的客户端操作，请参阅[授权终结点错误的错误代码](#error-codes-for-authorization-endpoint-errors)。

获取授权 `code` 和 `id_token` 之后，可以将用户登录，并代表他们获取[访问令牌](access-tokens.md)。 要将用户登录，必须确切地按上面所述验证 `id_token`。 若要获取访问令牌，可以遵循 [OAuth 代码流文档](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token)的“使用授权代码请求访问令牌”部分中所述的步骤。

## <a name="next-steps"></a>后续步骤

* 详细了解[访问令牌](access-tokens.md)。
* 详细了解 [`id_token` 和声明](id-tokens.md)。
