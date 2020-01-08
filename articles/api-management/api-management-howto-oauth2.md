---
title: 在 Azure API 管理中使用 OAuth 2.0 为开发人员帐户授权 | Microsoft 文档
description: 了解如何在 API 管理中使用 OAuth 2.0 为用户授权。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: e6b5c8c2f734a12fe246a82ce1aa1dc53893ab64
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072381"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>如何在 Azure API 管理中使用 OAuth 2.0 为开发人员帐户授权

许多 API 支持使用 [OAuth 2.0](https://oauth.net/2/) 维护 API 的安全，并确保仅有效用户具有访问权限且只能访问有权访问的资源。 要将 Azure API 管理的交互式开发人员门户与此类 API 配合使用，需通过该服务对服务实例进行配置，使之适用于支持 OAuth 2.0 的 API。

## <a name="prerequisites"> </a>先决条件

本指南介绍如何配置 API 管理服务实例，以便针对开发人员帐户使用 OAuth 2.0 授权，但不介绍如何配置 OAuth 2.0 提供程序。 每个 OAuth 2.0 提供程序的配置均不相同，虽然步骤类似，不过在 API 管理服务实例中配置 OAuth 2.0 时使用的必需信息是相同的。 本主题介绍的示例使用 Azure Active Directory 作为 OAuth 2.0 提供程序。

> [!NOTE]
> 有关使用 Azure Active Directory 配置 OAuth 2.0 的详细信息，请参阅 [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] 示例。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>在 API 管理中配置 OAuth 2.0 授权服务器

> [!NOTE]
> 如果尚未创建 API 管理服务实例，请参阅[创建 API 管理服务实例][Create an API Management service instance]。

1. 单击左侧菜单中的 OAuth 2.0 标签，然后单击“+添加”。

    ![OAuth 2.0 菜单](./media/api-management-howto-oauth2/oauth-01.png)

2. 在“名称”和“说明”字段中输入名称和可选说明。

    > [!NOTE]
    > 这些字段用于标识当前 API 管理服务实例中的 OAuth 2.0 授权服务器，其值不来自 OAuth 2.0 服务器。

3. 输入“客户端注册页 URL”。 此页是供用户创建和管理其帐户的地方，因所使用的 OAuth 2.0 提供程序而异。 “客户端注册页 URL”指向供用户针对 OAuth 2.0 提供程序创建和配置自己帐户的页面，这些提供程序支持用户管理帐户。 某些组织不配置或使用此功能，即使 OAuth 2.0 提供程序支持此功能。 如果 OAuth 2.0 提供程序尚未配置用户管理帐户功能，请在此处输入一个占位符 URL，例如公司的 URL，或 `https://placeholder.contoso.com` 之类的 URL。

    ![OAuth 2.0 新服务器](./media/api-management-howto-oauth2/oauth-02.png)

4. 此窗体的下一部分包含“授权的授权类型”、“授权终结点 URL”和“授权请求方法”设置。

    选中所需类型即可指定“授权的授权类型”。 “授权代码”是默认指定的。

    输入“授权终结点 URL”。 对于 Azure Active Directory, 此 url 将类似于以下 url, 其中`<tenant_id>`将替换为你的 Azure AD 租户的 ID。

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    “授权请求方法”指定如何向 OAuth 2.0 服务器发送授权请求。 默认选择 **GET**。

5. 然后，需要指定“令牌终结点 URL”、“客户端身份验证方法”、“访问令牌发送方法”和“默认范围”。

    ![OAuth 2.0 新服务器](./media/api-management-howto-oauth2/oauth-03.png)

    对于 Azure Active Directory OAuth 2.0 服务器，“令牌终结点 URL”将具有如下格式，其中 `<TenantID>` 的格式为 `yourapp.onmicrosoft.com`。

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    “客户端身份验证方法”的默认设置为“基本”，“访问令牌发送方法”为“Authorization 标头”。 这些值以及“默认范围”在窗体的此部分配置。

6. “客户端凭据”部分包含“客户端 ID”和“客户端密钥”，在创建和配置 OAuth 2.0 服务器的过程中获取。 指定“客户端 ID”和“客户端密钥”以后，会生成“授权代码”的“redirect_uri”。 该 URI 用于在 OAuth 2.0 服务器配置中配置回复 URL。

    ![OAuth 2.0 新服务器](./media/api-management-howto-oauth2/oauth-04.png)

    如果“授权的授权类型”设置为“资源所有者密码”，则可使用“资源所有者密码凭据”部分指定这些凭据；否则可将其留空。

    完成窗体的操作后，单击“创建”保存 API 管理 OAuth 2.0 授权服务器配置。 保存服务器配置后，可将 API 配置为使用此配置，如下一部分所示。

## <a name="step2"> </a>将 API 配置为使用 OAuth 2.0 用户授权

1. 在左侧的“API 管理”菜单中单击“API”。

    ![OAuth 2.0 API](./media/api-management-howto-oauth2/oauth-05.png)

2. 单击所需 API 的名称，然后单击“设置”。 滚动至“安全性”部分，然后选中 **OAuth 2.0** 框。

    ![OAuth 2.0 设置](./media/api-management-howto-oauth2/oauth-06.png)

3. 从下拉列表中选择所需的“授权服务器”，并单击“保存”。

    ![OAuth 2.0 设置](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"> </a>在开发人员门户中测试 OAuth 2.0 用户授权

配置 OAuth 2.0 授权服务器并将 API 配置为使用该服务器以后，即可转到开发人员门户并调用 API 对其进行测试。  单击 Azure API 管理实例**概述**页顶部菜单中的“开发人员门户”。

![开发人员门户][api-management-developer-portal-menu]

单击顶部菜单中的“API”，并选择“Echo API”。

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> 如果只配置了一个 API 或者只有一个 API 对你的帐户可见，则单击 API 会直接进入该 API 的操作。

选择“GET 资源”操作，单击“打开控制台”，并从下拉列表中选择“授权代码”。

![打开控制台][api-management-open-console]

选中“授权代码”后，会显示一个弹出窗口，其中包含 OAuth 2.0 提供程序的登录窗体。 在此示例中，登录窗体由 Azure Active Directory 提供。

> [!NOTE]
> 如果已禁用弹出窗口，浏览器会提示用户启用该功能。 启用该功能后，再次选中“授权代码”，此时就会显示登录窗体。

![登录][api-management-oauth2-signin]

登录后，“请求标头”中会填充用于对请求授权的 `Authorization : Bearer` 标头。

![请求标头令牌][api-management-request-header-token]

此时可以配置剩余参数的所需值，并提交请求。

## <a name="next-steps"></a>后续步骤

有关如何使用 OAuth 2.0 和 API 管理的详细信息，请观看以下视频并查看随附的[文章](api-management-howto-protect-backend-with-aad.md)。

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

