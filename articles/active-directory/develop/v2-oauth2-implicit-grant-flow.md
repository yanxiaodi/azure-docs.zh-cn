---
title: 使用 Microsoft 标识平台隐式流保护单页应用程序 | Azure
description: 使用 Microsoft 标识平台隐式流实现为单页面应用生成 Web 应用。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 3605931f-dc24-4910-bb50-5375defec6a8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab5ba4fde7469854954ed19d2e643f2b8a23f34f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193255"
---
# <a name="microsoft-identity-platform-and-implicit-grant-flow"></a>Microsoft 标识平台和隐式授权流

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

使用 Microsoft 标识平台终结点, 你可以使用 Microsoft 的个人和工作或学校帐户, 将用户登录到单页应用。 主要在浏览器上运行的单页面和其他 JavaScript 应用程序在身份验证时面临一些有趣的挑战：

* 这些应用的安全特征与传统的基于服务器的 Web 应用程序大不相同。
* 许多授权服务器与标识提供者不支持 CORS 请求。
* 重定向离开应用程序的完整网页浏览器变得对用户经验特别有侵略性。

对于这些应用程序（AngularJS、Ember.js、React.js 等），Microsoft 标识平台支持 OAuth 2.0 隐式授权流。 有关隐式流的说明，请参阅 [OAuth 2.0 规范](https://tools.ietf.org/html/rfc6749#section-4.2)。 其主要优点是它可让应用程序从 Microsoft 标识平台获取令牌，无需要执行后端服务器凭据交换。 这可让应用登录用户、维护会话，并获取客户端 JavaScript 代码中所有其他 Web API 的令牌。 使用隐式流时有几个重要的安全注意事项，具体而言，是关于[客户端](https://tools.ietf.org/html/rfc6749#section-10.3)和[用户模拟](https://tools.ietf.org/html/rfc6749#section-10.3)的注意事项。

如果要使用隐式流和 Microsoft 标识平台将身份验证添加到 JavaScript 应用，建议使用开放源代码 JavaScript 库 [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)。

但是，如果不想在单页面应用中使用库，而是要自行发送协议消息，请遵循下面的常规步骤。

> [!NOTE]
> Microsoft 标识平台终结点并非支持所有 Azure Active Directory (Azure AD) 方案和功能。 若要确定是否应使用 Microsoft 标识平台终结点，请阅读 [Microsoft 标识平台限制](active-directory-v2-limitations.md)。

## <a name="protocol-diagram"></a>协议图

下图显示了整个隐式登录流的样子，后续各部分更详细地介绍了每个步骤。

![显示隐式登录流的关系图](./media/v2-oauth2-implicit-grant-flow/convergence-scenarios-implicit.svg)

## <a name="send-the-sign-in-request"></a>发送登录请求

最初将用户登录到应用时，可以发送 [OpenID Connect](v2-protocols-oidc.md) 身份验证请求，并从 Microsoft 标识平台终结点获取 `id_token`。

> [!IMPORTANT]
> 若要成功请求 ID 令牌，必须通过在“隐式授权”部分下选择“访问令牌”和“ID 令牌”，为 [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页中的应用注册正确启用隐式授权流。 如果未启用，将返回 `unsupported_response` 错误：为输入参数“response_type”提供的值不允许用于此客户端。预期值为“code””

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid
&response_mode=fragment
&state=12345
&nonce=678910
```

> [!TIP]
> 若要使用隐式流测试登录，请单击 <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=fragment&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize..</a>在登录之后，浏览器应重定向到 `https://localhost/myapp/` ，并且地址栏中有一个 `id_token` 。
>

| 参数 |  | 描述 |
| --- | --- | --- |
| `tenant` | 必需 |请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关更多详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| `client_id` | 必需 | [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页分配给应用的应用程序（客户端）ID。 |
| `response_type` | 必需 |必须包含 OpenID Connect 登录的 `id_token`。 也可以包含 response_type `token`。 此处使用 `token`，让应用能够立即从授权终结点接收访问令牌，而无需向授权终结点发出第二次请求。 如果使用 `token` response_type，`scope` 参数必须包含范围，以指出要对哪个资源发出令牌。 |
| `redirect_uri` | 建议 |应用的 redirect_uri，应用可向其发送及从其接收身份验证响应。 其必须完全符合在门户中注册的其中一个 redirect_uris，否则必须是编码的 url。 |
| `scope` | 必需 |[范围](v2-permissions-and-consent.md)的空格分隔列表。 针对 OpenID Connect，即必须包含范围 `openid`，其在同意 UI 中转换为“你将登录”权限。 （可选）可能需要包含 `email` 或 `profile` 范围，以获取对其他用户数据的访问权限。 也可以在此请求中包含其他范围，以请求同意各种资源。 |
| `response_mode` | 可选 |指定将生成的令牌送回到应用程序所应该使用的方法。 默认为查询访问令牌，但如果请求包括 id_token，则会进行分段。 |
| `state` | 建议 |同样随令牌响应返回的请求中所包含的值。 它可以是你想要的任何内容的字符串。 随机生成的唯一值通常用于[防止跨站点请求伪造攻击](https://tools.ietf.org/html/rfc6749#section-10.12)。 该状态也用于在身份验证请求出现之前，于应用程序中编码用户的状态信息，例如之前所在的网页或视图。 |
| `nonce` | 必需 |由应用程序生成且包含在请求中的值，以声明方式包含在生成的 id_token 中。 应用程序接着便可确认此值，以减少令牌重新执行攻击。 此值通常是随机的唯一字符串，可用以识别请求的来源。 只有请求 id_token 时才是必需的。 |
| `prompt` | 可选 |表示需要的用户交互类型。 目前的有效值为“login”、“none”、“select_account”和“consent”。 `prompt=login` 将强制用户在该请求上输入其凭据，从而使单一登录无效。 `prompt=none` 则相反 - 它确保不对用户显示任何交互式提示。 如果请求无法通过单一登录静默完成，则 Microsoft 标识平台终结点将返回一个错误。 `prompt=select_account` 将用户发送到一个帐户选取器，其中将显示在会话中记住的所有帐户。 `prompt=consent` 在用户登录后将触发 OAuth 同意对话框，要求用户向应用授予权限。 |
| `login_hint`  |可选 |如果事先知道其用户名称，可用于预先填充用户登录页面的用户名称/电子邮件地址字段。 通常，应用会在重新身份验证期间使用此参数，并且已经使用 `preferred_username` 声明从前次登录提取用户名。|
| `domain_hint` | 可选 |可以是 `consumers` 或 `organizations` 之一。 如果包含，它跳过用户在登录页上经历的基于电子邮件的发现过程，导致稍微更加流畅的用户体验。 通常，应用会在重新身份验证期间使用此参数，方法是从 id_token 提取 `tid` 声明。 如果 `tid` 声明值为 `9188040d-6c67-4c5b-b112-36a304b66dad`（Microsoft 帐户使用者租户），则应使用 `domain_hint=consumers`。 否则，可以在重新进行身份验证期间使用 `domain_hint=organizations`。 |

此时，请求用户输入其凭据并完成身份验证。 Microsoft 标识平台终结点还会确保用户已许可 `scope` 查询参数中指定的权限。 如果用户未曾同意这些权限的任何一项，就请求用户同意请求的权限。 有关详细信息，请参阅[权限、同意和多租户应用](v2-permissions-and-consent.md)。

用户经过身份验证并授予许可后，Microsoft 标识平台终结点将使用 `response_mode` 参数中指定的方法，将响应返回到位于所指示的 `redirect_uri` 的应用。

#### <a name="successful-response"></a>成功的响应

使用 `response_mode=fragment` 和 `response_type=id_token+token` 的成功响应如下所示（为方便阅读，包含了换行符）：

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fuser.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| 参数 | 描述 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token`，则包含该参数。 应用请求的访问令牌，在本例中为 Microsoft Graph 的访问令牌。 访问令牌不得进行解码或检查，应当作为不透明字符串对待。 |
| `token_type` |如果 `response_type` 包含 `token`，则包含该参数。 始终为 `Bearer`。 |
| `expires_in`|如果 `response_type` 包含 `token`，则包含该参数。 表示令牌有效的秒数（针对缓存目的）。 |
| `scope` |如果 `response_type` 包含 `token`，则包含该参数。 表示 access_token 的有效范围。 如果不适用于用户, 则不能包括请求的所有范围 (如果在使用个人帐户登录时请求仅限 Azure AD 作用域)。 |
| `id_token` | 有符号 JSON Web 令牌 (JWT)。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值来获取任何授权或安全边界。 有关 id_tokens 的详细信息，请参阅 [`id_token reference`](id-tokens.md)。 <br> **注意：** 仅当已请求 `openid` 作用域时提供。 |
| `state` |如果请求中包含状态参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的 state 值是否完全相同。 |

#### <a name="error-response"></a>错误响应

错误响应可能也发送到 `redirect_uri`，让应用可以适当地处理：

```
GET https://localhost/myapp/#
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 参数 | 描述 |
| --- | --- |
| `error` |用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

## <a name="validate-the-id_token"></a>验证 id_token

仅接收 id_token 不足以对用户进行身份验证，还必须验证 id_token 的签名，并根据应用的要求验证令牌中的声明。 Microsoft 标识平台终结点使用 [JSON Web 令牌 (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) 和公钥加密对令牌进行签名并验证其是否有效。

可以选择验证客户端代码中的 `id_token`，但是常见的做法是将 `id_token` 发送到后端服务器，并在那里执行验证。 验证 id_token 的签名后，需要验证一些声明。 有关详细信息，请参阅[`id_token`参考](id-tokens.md)，其中包括[验证令牌](id-tokens.md#validating-an-id_token)和[有关签名密钥滚动更新的重要信息](active-directory-signing-key-rollover.md)。 我们建议利用库来分析和验证令牌 - 对于大多数语言和平台至少有一个可用。

可能还希望根据自己的方案验证其他声明。 一些常见的验证包括：

* 确保用户/组织已注册应用。
* 确保用户拥有正确的授权/权限。
* 确保身份验证具有一定的强度，例如多重身份验证。

验证 id_token 后，可以开始与用户的会话，并使用 id_token 中的声明来获取应用中的用户相关信息。 此信息可用于显示、记录和个性化等。

## <a name="get-access-tokens"></a>获取访问令牌

已经将用户登录到单页面应用，现在可以获取访问令牌以调用受到 Microsoft 标识平台保护的 Web API，例如 [Microsoft Graph](https://developer.microsoft.com/graph)。 即使你已使用 `token` response_type 收到令牌，也仍可以使用此方法获取其他资源的令牌，而无需再次将用户重定向到登录页。

在正常的 OpenID Connect/OAuth 流中，可以通过对 Microsoft 标识平台 `/token` 终结点进行请求来实现此目的。 但是，Microsoft 标识平台终结点不支持 CORS 请求，因此进行 AJAX 调用以获取和刷新令牌是不可能的。 相反，可以在隐藏的 iframe 中使用隐式流，以获取其他 Web API 的新令牌： 

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fuser.read 
&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

有关 URL 中的查询参数的详细信息，请参阅[发送登录请求](#send-the-sign-in-request)。

> [!TIP]
> 请尝试将以下请求复制并粘贴到浏览器选项卡中！ （不要忘记使用适用于用户的正确值替换 `login_hint` 值）
>
>`https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2user.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&login_hint=your-username`
>

借助 `prompt=none` 参数，此请求将立即成功或立即失败，并返回应用程序。 成功的响应会通过 `response_mode` 参数中指定的方法，发送到位于所指示的 `redirect_uri` 的应用。

#### <a name="successful-response"></a>成功的响应

使用 `response_mode=fragment` 的成功响应如下所示：

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| 参数 | 描述 |
| --- | --- |
| `access_token` |如果 `response_type` 包含 `token`，则包含该参数。 应用请求的访问令牌，在本例中为 Microsoft Graph 的访问令牌。 访问令牌不得进行解码或检查，应当作为不透明字符串对待。 |
| `token_type` | 始终为 `Bearer`。 |
| `expires_in` | 表示令牌有效的秒数（针对缓存目的）。 |
| `scope` | 表示 access_token 的有效范围。 如果不适用于用户, 则不能包括请求的所有范围 (如果在使用个人帐户登录时请求仅限 Azure AD 作用域)。 |
| `id_token` | 有符号 JSON Web 令牌 (JWT)。 如果 `response_type` 包含 `id_token`，则包含该参数。 应用可以解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值来获取任何授权或安全边界。 有关 id_tokens 的详细信息，请参阅[`id_token`参考](id-tokens.md)。 <br> **注意：** 仅当已请求 `openid` 作用域时提供。 |
| `state` |如果请求中包含状态参数，响应中就应该出现相同的值。 应用应该验证请求和响应中的 state 值是否完全相同。 |

#### <a name="error-response"></a>错误响应

错误响应还可能发送到 `redirect_uri`，因此应用可以适当地对其进行处理。 如果是 `prompt=none`，预期的错误为：

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| 参数 | 描述 |
| --- | --- |
| `error` |用于分类发生的错误类型与响应错误的错误码字符串。 |
| `error_description` |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

如果在 iframe 请求中收到此错误，用户必须再次以交互方式登录以检索新令牌。 可以选择对应用程序合理的任何方式处理这种情况。

## <a name="refreshing-tokens"></a>刷新令牌

隐式授权不提供刷新令牌。 `id_token` 和 `access_token` 很快就会过期，因此应用必须准备好定期刷新这些令牌。 若要刷新任一类型的令牌，可以通过使用 `prompt=none` 参数控制 Microsoft 标识平台的行为，来执行上述同一隐藏的 iframe 请求。 如果想要接收新的 `id_token`，请务必使用 `response_type=id_token` 和 `scope=openid`，以及 `nonce` 参数。

## <a name="send-a-sign-out-request"></a>发送注销请求

OpenID Connect `end_session_endpoint` 允许应用向 Microsoft 标识平台终结点发送请求以结束用户的会话，并清除 Microsoft 标识平台终结点设置的 Cookie。 要完全将用户从 Web 应用程序注销，应用应结束自己与用户的会话（通常通过清除令牌缓存或删除 Cookie 实现），然后将浏览器重定向到：

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/logout?post_logout_redirect_uri=https://localhost/myapp/
```

| 参数 |  | 描述 |
| --- | --- | --- |
| `tenant` |必需 |请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。 可以使用的值包括 `common`、`organizations`、`consumers` 和租户标识符。 有关更多详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| `post_logout_redirect_uri` | 建议 | 注销完成后用户应返回到的 URL。 此值必须与为应用程序注册的重定向 URI 之一匹配。 如果未包含，Microsoft 标识平台终结点会向用户显示一条常规消息。 |

## <a name="next-steps"></a>后续步骤

* 重温 [MSAL JS 示例](sample-v2-code.md)以开始编码。
