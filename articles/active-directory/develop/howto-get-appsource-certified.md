---
title: 如何使 AppSource 通过 Azure Active Directory 的认证 | Microsoft Docs
description: 详细说明如何使应用程序 AppSource 通过 Azure Active Directory 的认证。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 034c02c89c6e720311b3dc36428035e8cbdd2b3b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835216"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>如何使 AppSource 通过 Azure Active Directory 的认证

[Microsoft AppSource](https://appsource.microsoft.com/) 是一个可供业务用户发现、尝试和管理业务线 SaaS 应用程序（独立 SaaS 和现有 Microsoft SaaS 产品的加载项）的目的地。

若要在 AppSource 上列出独立 SaaS 应用程序，你的应用程序必须接受来自拥有 Azure Active Directory (Azure AD) 的任何公司或组织的工作帐户的单一登录。 单一登录过程必须使用 [OpenID Connect](v1-protocols-openid-connect-code.md) 或 [OAuth 2.0](v1-protocols-oauth-code.md) 协议。 AppSource 认证不接受 SAML 集成。

## <a name="guides-and-code-samples"></a>指南和代码示例

如果要了解如何使用 OpenID 连接将应用程序与 Azure AD 集成，请遵循 [Azure Active Directory 开发人员指南](v1-overview.md#get-started "Azure AD 开发人员入门")中的指南和代码示例。

## <a name="multi-tenant-applications"></a>多租户应用程序

多租户应用程序是可接受来自拥有 Azure AD 的任何公司或组织的用户的登录，而无需单独实例、配置或部署的应用程序。 AppSource 建议应用程序实施多租户来启用单击免费试用体验。

若要在应用程序中启用多租户，请执行以下操作：
1. 在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)中，将应用程序注册信息中的 `Multi-Tenanted` 属性设置为 `Yes`。 默认情况下，在 Azure 门户中创建的应用程序将配置为[单租户](#single-tenant-applications)。
1. 将代码更新为向 `common` 终结点发送请求。 为此，请将终结点从 `https://login.microsoftonline.com/{yourtenant}` 更新为 `https://login.microsoftonline.com/common*`。
1. 对于某些平台（如 ASP .NET），还需更新代码以接受多个证书颁发者。

有关多租户的详细信息，请参阅：[如何使用多租户应用程序模式登录任意 Azure Active Directory (Azure AD) 用户](howto-convert-app-to-be-multi-tenant.md)。

### <a name="single-tenant-applications"></a>单租户应用程序

单租户应用程序是仅接受来自定义的 Azure AD 实例的用户登录的应用程序。 将每个用户作为来宾帐户添加到应用程序注册的 Azure AD 实例后，外部用户（包括来自其他组织的工作或学校帐户，或个人帐户）可以登录到单租户应用程序。 

可以通过 [Azure AD B2B 协作](../b2b/what-is-b2b.md)将用户作为来宾帐户添加到 Azure AD，或者[以编程方式](../../active-directory-b2c/code-samples.md)执行此操作。 使用 B2B 时，用户可以创建自助服务门户，而无需接收登录邀请。 有关详细信息，请参阅 [Azure AD B2B 协作注册的自助服务门户](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)。

单租户应用程序可启用“与我联系”体验，但若要启用 AppSource 建议的单击/免费试用体验，则请改为在应用程序上启用多租户。

## <a name="appsource-trial-experiences"></a>AppSource 试用体验

### <a name="free-trial-customer-led-trial-experience"></a>免费试用（客户导向型试用体验）

客户导向型试用是 AppSource 所推荐的体验，因为它提供对应用程序的单击访问。 以下示例显示了此体验的外观:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>用户在 AppSource 网站中查找你的应用程序</li><li>选择“免费试用”选项</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource 将用户重定向到你网站中的 URL</li><li>网站将自动开始<i>单一登录</i>过程（页面加载中）</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>用户被重定向到 Microsoft 登录页</li><li>用户提供凭据以登录</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>用户对你的应用程序授权</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>登录完成，且用户被重定向回你的网站</li><li>用户开始进行免费试用</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>与我联系（合作伙伴导向型试用体验）

需要执行手动或长期操作来预配用户/公司（例如，应用程序需要预配虚拟机、数据库实例或耗费大量时间才可完成的操作）时，可以使用合作伙伴试用体验。 在这种情况下，用户选择“请求试用”按钮并填写表单后，AppSource 将向你发送用户的联系信息。 收到此信息后，需预配环境并就如何访问试用体验向用户发送说明：<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>用户在 AppSource 网站中查找你的应用程序</li><li>选择“与我联系”选项</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>在表单中填写联系信息</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>你接收到用户信息</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>设置环境</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>就试用信息与用户联系</td>
        </tr>
        </table><br/><br/>
        <ul><li>你接收到用户信息并设置试用实例</li><li>向用户发送用于访问你的应用程序的超链接</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>用户访问你的应用程序并完成单一登录过程</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>用户对你的应用程序授权</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>登录完成，且用户被重定向回你的网站</li><li>用户开始进行免费试用</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>详细信息

有关 AppSource 试用体验的详细信息，请参阅[此视频](https://aka.ms/trialexperienceforwebapps)。 

## <a name="next-steps"></a>后续步骤

- 有关构建支持 Azure AD 登录的应用程序的详细信息，请参阅 [Azure AD 的身份验证方案](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)。
- 有关如何在 AppSource 中列出你的 SaaS 应用程序的信息，请参阅 [AppSource 合作伙伴信息](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>获取支持

对于 Azure AD 集成，我们使用 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) 和社区来提供支持。

强烈建议先在 Stack Overflow 上提问并浏览现有问题，查看是否已有人提出过相同疑问。 请务必将提问或评论用 [`[azure-active-directory]` 和 `[appsource]`](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) 标记。

使用以下评论部分提供反馈，帮助我们改进内容。

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
