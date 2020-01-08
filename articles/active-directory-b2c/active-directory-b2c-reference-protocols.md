---
title: Azure Active Directory B2C 中的身份验证协议 | Microsoft Docs
description: 如何使用 Azure Active Directory B2C 支持的协议直接构建应用。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 083fd6b6027c78e956c133d7801a03fd9042e88d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835754"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C：身份验证协议
通过支持两种行业标准协议（OpenID Connect 和 OAuth 2.0），Azure Active Directory B2C (Azure AD B2C) 为应用提供标识即服务。 这是符合标的服务，但这些协议的任意两个实现之间仍然存在微妙的差异。

如果是通过直接发送和处理 HTTP 请求而不是使用开放源库来编写代码，本指南中的信息才有所帮助。 建议先阅读此页面，再深入了解每个特定协议的详细信息。 但如果已熟悉 Azure AD B2C，则可以直接转到[协议参考指南](#protocols)。

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>基础知识
每个使用 Azure AD B2C 的应用都需要在 [Azure 门户](https://portal.azure.com)的 B2C 目录中注册。 应用注册过程将收集一些值并将其分配给应用：

* 用于唯一标识应用的 **应用程序 ID** 。
* 用于将响应定向回应用的**重定向 URI** 或**包标识符**。
* 其他一些特定于方案的值。 有关详细信息，请了解[如何注册应用程序](active-directory-b2c-app-registration.md)。

注册应用后，应用将通过向终结点发送请求来与 Azure Active Directory (Azure AD) 通信：

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

几乎在所有的 OAuth 和 OpenID Connect 流中，都有四个参与交换的对象：

![显示四个 OAuth 2.0 角色的关系图](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* 授权服务器  是 Azure AD 终结点。 它可安全处理与用户信息和访问相关的任何内容。 还可以处理流中参与方之间的信任关系。 它负责验证用户的标识、授予和吊销对资源的访问权限，以及颁发令牌。 也被称作标识提供者。

* **资源所有者**通常是最终用户。 它是拥有数据的一方，并且有权允许第三方访问该数据或资源。

* **OAuth 客户端**是应用。 它由其应用程序 ID 标识。 通常作为最终用户与之交互的一方。 还会从授权服务器请求令牌。 资源所有者必须授予客户端授权才能访问资源。

* **资源服务器**是资源或数据所在的位置。 它信任授权服务器，以安全地对 OAuth 客户端进行验证和授权。 它还会使用持有者访问令牌，确保可以授予对资源的访问权限。

## <a name="policies-and-user-flows"></a>策略和用户流
可以说，Azure AD B2C 策略是服务最重要的功能。 Azure AD B2C 通过引入策略来扩展标准 OAuth 2.0 和 OpenID Connect 协议。 这些协议允许 Azure AD B2C 执行简单身份验证和授权以外的更多功能。

若要帮助设置最常见的标识任务，Azure AD B2C 门户应包括名为“用户流”  的预定义且可配置的策略。 用户流充分描述了使用者标识体验，包括注册、登录和配置文件编辑。 可以在管理 UI 中定义用户流。 通过在 HTTP 认证请求中使用特殊的查询参数来执行策略。

策略和用户流不是 OAuth 2.0 和 OpenID Connect 的标准功能，因此应该花时间去了解它们。 有关详细信息，请参阅 [Azure AD B2C 用户流参考指南](active-directory-b2c-reference-policies.md)。

## <a name="tokens"></a>令牌
OAuth 2.0 和 OpenID Connect 的 Azure AD B2C 实现广泛使用了持有者令牌，包括表示为 JSON Web 令牌 (JWT) 的持有者令牌。 持有者令牌是一种轻型安全令牌，它授予对受保护资源的“持有者”访问权限。

持有者是可以提供令牌的任何一方。 参与方必须先经 Azure AD 验证才能接收持有者令牌。 但如果不采取必要的步骤在传输过程和存储中对令牌进行保护，令牌可能会被意外的某一方拦截并使用。

某些安全令牌具有防止未授权方使用令牌的内置机制，但持有者令牌不具有这种机制。 它们必须在安全通道中传输，例如传输层安全性 (HTTPS)。

如果持有者令牌在安全通道外传输，则恶意方就可以利用中间人攻击来获得令牌，并使用它对受保护资源进行未经授权的访问。 存储或缓存持有者令牌以供以后使用时，也应遵循同样的安全原则。 请始终确保应用以安全的方式传输和存储持有者令牌。

有关持有者令牌的更多安全注意事项，请参阅 [RFC 6750 第 5 部分](https://tools.ietf.org/html/rfc6750)。

若要深入了解 Azure AD B2C 中使用的不同类型令牌，请参阅 [Azure AD 令牌参考](active-directory-b2c-reference-tokens.md)。

## <a name="protocols"></a>协议
准备好查看一些示例请求时，可以从以下教程中的其中一篇开始。 每篇教程对应于特定的身份验证方案。 如果在确定适当的流时需要帮助，请查看[使用 Azure AD B2C 可以构建的应用类型](active-directory-b2c-apps.md)。

* [使用 OAuth 2.0 构建移动和本机应用程序](active-directory-b2c-reference-oauth-code.md)
* [使用 OpenID Connect 构建 Web 应用](active-directory-b2c-reference-oidc.md)
* [使用 OAuth 2.0 隐式流构建单页应用](active-directory-b2c-reference-spa.md)

