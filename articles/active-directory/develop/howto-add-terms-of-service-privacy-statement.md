---
title: 应用的服务条款和隐私声明 | Azure
description: 了解如何为注册为使用 Azure AD 的应用配置服务条款和隐私声明。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a01b50573405964b09339d03e84c62dbdd8582
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482855"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>如何：配置应用的服务条款和隐私声明

构建和管理与 Azure Active Directory (Azure AD) 和 Microsoft 帐户集成的应用的开发人员应随附指向应用的服务条款和隐私声明的链接。 服务条款和隐私声明通过用户同意体验展示给用户。 它们可以帮助用户认识到他们可以信任你的应用。 对于面向用户的多租户应用（由多个目录使用的应用或面向所有 Microsoft 帐户提供的应用）来说，服务条款和隐私声明至关重要。

你负责为你的应用创建服务条款和隐私声明文档，并提供指向这些文档的 URL。 对于未能提供这些链接的多租户应用，你的应用的用户同意体验将显示一条警报，可能阻碍用户同意使用你的应用。

> [!NOTE]
> * 单租户应用不会显示警报。
> * 如果缺少一个或两个两个链接，应用将显示警报。

## <a name="user-consent-experience"></a>用户同意体验

下面的示例分别展示配置了服务条款和隐私声明以及未配置服务条款和隐私声明情况下的用户同意体验。

![提供了含有和不含隐私声明和服务条款的屏幕截图](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>设置链接格式指向服务条款和隐私声明文档

添加指向应用的服务条款和隐私声明的文档之前，请确保 URL 遵循以下准则。

| 准则     | 描述                           |
|---------------|---------------------------------------|
| 格式        | 有效的 URL                             |
| 有效的架构 | HTTP 和 HTTPS<br/>建议使用 HTTPS |
| 最大长度    | 2048 个字符                       |

示例：`https://myapp.com/terms-of-service` 和 `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>添加指向服务条款和隐私声明的链接

服务条款和隐私声明准备就绪后，可以在应用中使用这些方法之一添加指向这些文档的链接：

* [通过 Azure 门户 添加](#azure-portal)
* [使用应用对象 JSON](#app-object-json)
* [使用 MSGraph beta REST API](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>使用 Azure 门户
请按照下列步骤在 Azure 门户中。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 导航到“应用注册”部分并选择应用  。
3. 打开**品牌**窗格。
4. 填写“服务条款 URL”和“隐私声明 URL”字段   。
5. 保存所做更改。

    ![应用属性包含服务和隐私语句 Url 中的条款](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>使用应用对象 JSON

如果想要直接修改应用对象 JSON，可以使用 Azure 门户或应用注册门户中的清单编辑器来包含指向应用的服务条款和隐私声明的链接。

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>使用 MSGraph beta REST API

若要以编程方式更新所有应用，可以使用 MSGraph beta REST API 更新所有应用，以包含指向服务条款和隐私声明文档的链接。

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * 请注意不要覆盖已分配给以下任何字段的任何预先存在的值：`supportUrl``marketingUrl` 和 `logoUrl`
> * 仅当使用 Azure AD 帐户登录时，MSGraph beta REST API 才会正常工作。 不支持 Microsoft 个人帐户。
