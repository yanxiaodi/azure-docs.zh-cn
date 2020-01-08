---
title: 访问面板中基于密码的单一登录 (SSO) |Microsoft Docs
description: 讨论了用于解决与登录到为密码单一登录配置的 Azure AD 库应用程序相关的问题的问题区域。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca192c28757df189e531aee0ba2d8da288ba7e6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381240"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>登录到配置为密码单一登录的 Azure AD 库应用程序时出现的问题

访问面板是一个基于 Web 的门户，在 Azure Active Directory (Azure AD) 中拥有工作或学校帐户的用户可以使用它来查看和启动 Azure AD 管理员已授权他们访问的基于云的应用程序。 拥有 Azure AD 版本的用户还可以通过访问面板使用自助服务组和应用管理功能。 访问面板不同于 Azure 门户，它不要求用户拥有 Azure 订阅。

若要在访问面板中使用基于密码的单一登录 (SSO)，必须在用户的浏览器中安装访问面板扩展。 当用户选择某个已配置基于密码的 SSO 的应用程序时，会自动下载此扩展。

## <a name="meeting-browser-requirements-for-the-access-panel"></a>满足访问面板的浏览器要求

访问面板要求浏览器支持 JavaScript 并且已启用 CSS。 若要在访问面板中使用基于密码的单一登录 (SSO)，必须在用户的浏览器中安装访问面板扩展。 当用户选择某个已配置基于密码的 SSO 的应用程序时，会自动下载此扩展。

对于基于密码的 SSO，最终用户的浏览器可以是：

-   Internet Explorer 8、9、10、11 - 在 Windows 7 或更高版本上

-   Chrome -- 在 Windows 7 或更高版本上，以及在 MacOS X 或更高版本上

-   Firefox 26.0 或更高版本 -- 在 Windows XP SP2 或更高版本上，以及在 Mac OS X 10.6 或更高版本上

>[!NOTE]
>如果浏览器扩展支持 Microsoft Edge，则基于密码的 SSO 扩展可供 Windows 10 中的 Microsoft Edge 使用。
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>如何安装访问面板浏览器扩展

若要安装访问面板浏览器扩展，请按照以下步骤操作：

1.  在某个支持的浏览器中打开[访问面板](https://myapps.microsoft.com)，并在 Azure AD 中以“用户”身份登录。

2.  在访问面板中单击“密码-SSO 应用程序”。

3.  在出现询问是否安装该软件的提示时，选择“立即安装”。

4.  根据浏览器的情况，用户将定向到下载链接。 将扩展“添加”到浏览器中。

5.  如果浏览器出现提示，选择“启用”或“允许”扩展。

6.  安装完成后，“重启”浏览器会话。

7.  登录到访问面板，并查看是否可以**启动**密码 - SSO 应用程序

也可以通过下面的直接链接下载适用于 Chrome 和 Firefox 的扩展：

-   [Chrome 访问面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 访问面板扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>设置 Internet Explorer 的组策略

可以设置组策略，以便在用户的计算机上远程安装 Internet Explorer 的访问面板扩展。

先决条件包括：

-   已设置 [Active Directory 域服务](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)，并且已将用户的计算机加入域。

-   必须拥有“编辑设置”权限才能编辑组策略对象 (GPO)。 默认情况下，以下安全组的成员具有此权限：域管理员、企业管理员和组策略创建者所有者。 [了解详细信息](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

有关如何配置组策略并将其部署到用户的分步说明，请按照教程[如何使用组策略部署 Internet Explorer 的访问面板扩展](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)操作。

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>对 Internet Explorer 中的访问面板进行故障排除

有关访问诊断工具以及为 IE 配置扩展的分步说明，请按照[对 Internet Explorer 的访问面板扩展进行故障排除](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)指南操作。

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>如何配置非库应用程序的密码单一登录

若要从 Azure AD 库配置应用程序，需要：

-   [添加非库应用程序](#add-a-non-gallery-application)

-   [将应用程序配置为密码单一登录](#configure-the-application-for-password-single-sign-on)

-   [将用户分配到应用程序](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>添加非库应用程序

若要从 Azure AD 库添加应用程序，请按照以下步骤操作：

1.  打开 [Azure 门户](https://portal.azure.com)，并以“全局管理员”或“共同管理员”身份登录

2.  在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  在“企业应用程序”窗格的右上角，单击“添加”按钮。

6.  单击“非库应用程序”。

7.  在“名称”文本框中输入应用程序的名称。 选择“添加”。

稍等片刻，便可看到应用程序的配置窗格。

### <a name="configure-the-application-for-password-single-sign-on"></a>将应用程序配置为密码单一登录

若要为应用程序配置单一登录，请按照以下步骤操作：

1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2. 在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4. 在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5. 单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6. 选择要配置单一登录的应用程序

7. 在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8. 选择“基于密码的登录”模式。

9. 输入“登录 URL”。 这就是用户在其中输入用户名和密码进行登录时的 URL。 确保登录字段在 URL 中可见。

10. 将用户分配到应用程序。

11. 此外，还可以通过下列步骤代表用户提供凭据：选择用户对应的行，单击“更新凭据”，并代表用户输入用户名和密码。 否则，会在启动时提示用户输入凭据。

### <a name="assign-users-to-the-application"></a>将用户分配到应用程序

要直接将一个或多个用户分配到应用程序，请按照以下步骤操作：

1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2. 在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。

3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。

4. 在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5. 单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6. 从列表中选择要向其分配用户的应用程序。

7. 在应用程序加载后，在应用程序的左侧导航菜单中单击“用户和组”。

8. 单击“用户和组”列表顶部的“添加”按钮，以打开“添加分配”窗格。

9. 在“添加分配”窗格中，单击“用户和组”选择器。

10. 在“按名称或电子邮件地址搜索”搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的“用户”上方以显示“复选框”。 单击用户个人资料头像或徽标旁边的复选框，将用户添加到“已选择”列表。

12. **可选：** 如果要“添加多个用户”，请在“按名称或电子邮件地址搜索”搜索框中键入其他“全名”或“电子邮件地址”，然后单击复选框以将此用户添加到“已选择”列表。

13. 在完成用户的选择后，单击“选择”按钮将他们添加到要分配给应用程序的用户和组列表。

14. **可选：** 单击“添加分配”窗格中的“选择角色”选择器，选择一个角色来分配给所选用户。

15. 单击“分配”按钮，将应用程序分配给选定用户。

稍等片刻，所选用户就能使用解决方案描述部分所述的方法来启动这些应用程序了。

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>如果这些故障排除步骤未解决此问题

打开支持票证，并提供以下信息（如有）：

-   相关错误 ID

-   UPN（用户电子邮件地址）

-   TenantID

-   浏览器类型

-   错误发生的时区和时间/时间段

-   Fiddler 跟踪

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](application-proxy-configure-single-sign-on-with-kcd.md)

