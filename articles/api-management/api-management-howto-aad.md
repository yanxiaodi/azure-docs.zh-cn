---
title: 使用 Azure Active Directory 授权开发人员帐户 - Azure API 管理 | Microsoft Docs
description: 了解如何在 API 管理中使用 Azure Active Directory 授权用户。
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 6116ece6035b326440108c1f2b2effd1b6bb9258
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072550"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>在 Azure API 管理中使用 Azure Active Directory 授权开发人员帐户

本文介绍如何为 Azure Active Directory (Azure AD) 中的用户启用对开发人员门户的访问。 本指南还介绍如何通过添加包含用户的外部组管理 Azure AD 用户组。

## <a name="prerequisites"></a>先决条件

- 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)。
- 导入并发布 Azure API 管理实例。 有关详细信息，请参阅[导入和发布](import-and-publish.md)。

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>使用 Azure AD 为开发人员帐户授权

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 选择 ![箭头](./media/api-management-howto-aad/arrow.png).
3. 在搜索框中键入 **api**。
4. 选择“API 管理服务”。
5. 选择自己的 API 管理服务实例。
6. 在“安全性”下，选择“标识”。
7. 在顶部选择“+添加”。

    此时将在右侧显示“添加标识提供者”窗格。
8. 在“提供者类型”下，选择 **Azure Active Directory**。

    此时将在窗格中显示用于输入其他必要信息的控件。 控件包括“客户端 ID”和“客户端机密”。 （本文稍后将介绍有关这些控件的信息。）
9. 记下“重定向 URL”的内容。
    
   ![在 Azure 门户中添加标识提供者的步骤](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. 在浏览器中，打开另一个标签页。 
11. 导航到 [Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)，在 Active Directory 中注册应用。
12. 在“管理”下，选择“应用注册”。
13. 选择“新注册”。 在“注册应用程序”页上，将值设置如下：
    
* 将“名称”设置为一个有意义的名称。 例如，*developer-portal*
* 将“支持的帐户类型”设置为“仅限此组织目录中的帐户”。 
* 将“重定向 URI”设置为从步骤 9 获取的值。 
* 选择“注册”。 

14.  注册应用程序以后，从“概览”页复制“应用程序(客户端) ID”。 
15. 回到你的 API 管理实例。 在“添加标识提供者”窗口中，将“应用程序(客户端) ID”值粘贴到“客户端 ID”框中。
16. 切换回 Azure AD 配置，在“管理”下选择“证书和机密”。 选择“新建客户端机密”按钮。 在“说明”中输入一个值，针对“过期”选择任意选项，然后选择“添加”。 在离开此页之前复制客户端机密值。 在下一步中将要使用它。 
17. 在“管理”下选择“身份验证”，然后在“隐式授权”下选择“ID 令牌”
18. 回到你的 API 管理实例，将机密粘贴到“客户端机密”框中。

    > [!IMPORTANT]
    > 请确保在密钥过期前更新“客户端机密”。 
    >  
    >

19. “添加标识提供者”窗口还包含“允许的租户”文本框。 可在此框中指定要授予 API 管理服务实例的 API 对哪些 Azure AD 实例域的访问权限。 可使用换行符、空格或逗号分隔多个域。

> [!NOTE]
> 可在“允许的租户”部分中指定多个域。 在任何用户可以从注册应用程序的原始域以外的其他域登录之前，不同域的全局管理员必须先授予权限以使应用程序访问目录数据。 若要授予权限，全局管理员应执行以下操作：a. 转到 `https://<URL of your developer portal>/aadadminconsent`（例如， https://contoso.portal.azure-api.net/aadadminconsent) 。
> b. 键入他们想要授权访问的 Azure AD 租户域名。
> c. 选择**提交**。 

20.  指定所需配置后，选择“添加”。

保存更改后，指定的 Azure AD 实例中的用户便可按照[使用 Azure AD 帐户登录开发人员门户](#log_in_to_dev_portal)中的步骤登录到开发人员门户。

## <a name="add-an-external-azure-ad-group"></a>添加外部 Azure AD 组

在为 Azure AD 实例中的用户启用访问之后，可将 Azure AD 组添加到 API 管理中。 然后，可以更轻松地管理具有所需产品的组中的开发人员关联。

 > [!IMPORTANT]
 > 若要添加外部 Azure AD 组，必须先按照之前部分中的过程在“标识”选项卡中配置 Azure AD 实例。 另外，必须通过 `Directory.Read.All` 权限为应用程序授予范围 Azure AD 图形 API 的权限。 

可从 API 管理实例的“组”选项卡添加外部 Azure AD 组。

1. 选择“组”选项卡。
2. 选择“添加 AAD 组”按钮。
   ![“添加 AAD 组”按钮](./media/api-management-howto-aad/api-management-with-aad008.png)
3. 选择要添加的组。
4. 按“选择”按钮。

创建外部 Azure AD 组之后，可以查看和配置其属性。 从“组”选项卡中选择该组的名称。在此处，可以编辑该组的“名称”和“说明”信息。
 
配置的 Azure AD 实例中的用户现在可以登录开发人员门户。 他们可以查看和订阅可见的任何组。

## <a name="a-idlog_in_to_dev_portalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>使用 Azure AD 帐户登录开发人员门户

使用前面部分中配置的 Azure AD 帐户登录开发人员门户：

1. 使用 Active Directory 应用程序配置中的登录 URL 打开新的浏览器窗口，并选择“Azure Active Directory”。

   ![登录页][api-management-dev-portal-signin]

1. 输入 Azure Active Directory 中某个用户的凭据，并选择“登录”。

   ![使用用户名和密码登录][api-management-aad-signin]

1. 如果需要其他信息，可能出现注册表单的提示。 完成注册表单并选择“登录”。

   ![注册窗体中的“注册”按钮][api-management-complete-registration]

用户现已登录到 API 管理服务实例的开发人员门户。

![完成注册后的开发人员门户][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

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
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
