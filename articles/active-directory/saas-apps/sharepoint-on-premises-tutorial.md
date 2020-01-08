---
title: 教程：Azure Active Directory 与本地 SharePoint 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与本地 SharePoint 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 9c956f89d890f93a887d2412c74c906095acf4db
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164360"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>教程：Azure Active Directory 与本地 SharePoint 的集成

本教程介绍如何将本地 SharePoint 与 Azure Active Directory (Azure AD) 集成。
将本地 SharePoint 与 Azure AD 集成可获得以下优势：

* 可在 Azure AD 中控制谁有权访问本地 SharePoint。
* 可让用户使用其 Azure AD 帐户自动登录到本地 SharePoint（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与本地 SharePoint 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用本地 SharePoint 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* 本地 SharePoint 支持 SP 发起的 SSO 

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>从库中添加本地 SharePoint

若要配置本地 SharePoint 与 Azure AD 的集成，需要从库中将本地 SharePoint 添加到托管 SaaS 应用列表。

**若要从库中添加本地 SharePoint，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

    > [!NOTE]   
    > 如果元素应不可用，也可以通过左侧导航面板顶部的固定“所有服务”  链接将其打开。 在以下概述中，“Azure Active Directory”  链接位于“标识”  部分中，或者可以使用筛选器文本框搜索它。

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“本地 SharePoint”，在结果面板中选择“本地 SharePoint”，然后单击“添加”按钮添加该应用程序。   

    ![结果列表中的“本地 SharePoint”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们将基于名为“Britta Simon”的测试用户配置和测试本地 SharePoint 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与本地 SharePoint 相关用户之间建立链接关系。

若要配置和测试本地 SharePoint 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置本地 SharePoint 单一登录](#configure-sharepoint-on-premises-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[在 Azure 门户中创建 Azure AD 安全组](#create-an-azure-ad-security-group-in-the-azure-portal)** - 在 Azure AD 中启用新的安全组用于单一登录。
5. **[授予对 SharePoint 本地安全组的访问权限](#grant-access-to-sharepoint-on-premises-security-group)** - 授予 Azure AD 对特定组的访问权限。
6. **[在 Azure 门户中分配 Azure AD 安全组](#assign-the-azure-ad-security-group-in-the-azure-portal)** - 将特定的组分配到 Azure AD 用于身份验证。
7. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置本地 SharePoint 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“本地 SharePoint”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![本地 SharePoint 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<YourSharePointServerURL>/_trust/default.aspx`。

    b. 在“标识符”框中，使用以下模式键入 URL：`urn:sharepoint:federation` 

    c. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系[本地 SharePoint 客户端支持团队](https://support.office.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

    > [!Note]
    > 请记下你将证书文件下载到的文件路径，因为稍后需要在用于配置的 PowerShell 脚本中使用它。

6. 在“设置本地 SharePoint”部分，根据要求复制相应 URL  。 对于“单一登录服务 URL”  ，请使用采用以下模式的值：`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ 是 Azure AD 订阅的租户 ID。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    > [!NOTE]
    > 本地 SharePoint 应用程序使用 SAML 1.1 令牌，因此，Azure AD 预期 WS 联合身份验证请求来自 SharePoint 服务器；身份验证后，它会颁发 SAML 1.1 令牌。

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>配置本地 SharePoint 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到本地 SharePoint 公司站点。

2. **在 SharePoint Server 2016 中配置新的受信任标识提供者**

    登录到 SharePoint Server 2016 服务器并打开 SharePoint 2016 Management Shell。 从 Azure 门户中填写 $realm（Azure 门户中“SharePoint 本地域和 URL”部分中的标识符值）、$wsfedurl（单一登录服务 URL）和 $filepath（你将证书文件下载到的文件路径），并运行以下命令来配置新的可信标识提供者。

    > [!TIP]
    > 如果不熟悉 PowerShell 的用法，或想要详细了解 PowerShell 的工作原理，请参阅 [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps)。

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    接下来，遵循以下步骤为应用程序启用受信任的标识提供者：

    a. 在管理中心，导航到“管理 Web 应用程序”并选择要使用 Azure AD 保护的 Web 应用程序。 

    b. 在功能区中单击“身份验证提供程序”，然后选择要使用的区域。 

    c. 选择“受信任的标识提供者”，然后选择刚刚注册的名为 *AzureAD* 的标识提供者。 

    d. 在登录页上的 URL 设置中，选择“自定义登录页”并提供“/_trust/”值。 

    e. 单击“确定”。 

    ![配置身份验证提供程序](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > 某些外部用户将不能使用此单一登录集成，因为其 UPN 将具有扭曲的值，例如 `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`。 不久，我们将允许客户应用配置如何根据用户类型来处理 UPN。 在那之后，所有来宾用户应当都能够与组织员工一样无缝地使用 SSO。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 `brittasimon@yourcompanydomain.extension`   
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 门户中创建 Azure AD 安全组

1. 单击“Azure Active Directory”>“所有组”。 

    ![创建 Azure AD 安全组](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. 单击“新建组”： 

    ![创建 Azure AD 安全组](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. 填写“组类型”、“组名称”、“组说明”和“成员资格类型”。     单击箭头选择成员，然后搜索或单击要添加到该组的成员。 单击“选择”以添加选定的成员，然后单击“创建”。  

    ![创建 Azure AD 安全组](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > 若要将 Azure Active Directory 安全组分配到本地 SharePoint，需要在本地 SharePoint 场中安装并配置 [AzureCP](https://yvand.github.io/AzureCP/)，或者开发并配置 SharePoint 的替代自定义声明提供程序。  如果不使用 AzureCP，请参阅本文档末尾的“更多信息”部分来了解如何创建自己的自定义声明提供程序。

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>向 SharePoint 本地安全组授予访问权限

**在应用注册中配置安全组和权限**

1. 在 Azure 门户中，依次选择“Azure Active Directory”、“应用注册”   。

    ![“企业应用程序”边栏选项卡](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. 在搜索框中，键入并选择“本地 SharePoint”  。

    ![结果列表中的“本地 SharePoint”](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. 单击“清单”。 

    ![清单选项](./media/sharepoint-on-premises-tutorial/manifest.png)

4. 将“`groupMembershipClaims`: `NULL`”修改为“`groupMembershipClaims`: `SecurityGroup`”。 然后单击“保存”

    ![编辑清单](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. 依次单击“设置”、“所需的权限”   。

    ![所需的权限](./media/sharepoint-on-premises-tutorial/settings.png)

6. 依次单击“添加”、“选择 API”。  

    ![API 访问](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. 添加“Windows Azure Active Directory”和“Microsoft Graph API”，但每次只能选择一个。  

    ![API 选择](./media/sharepoint-on-premises-tutorial/permissions.png)

8. 选择“Windows Azure Active Directory”，选中“读取目录数据”，然后单击“选择”。 返回并添加“Microsoft Graph”，同样，为其选择“读取目录数据”。  依次单击“选择”、“完成”。

    ![启用访问权限](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. 现在，在“所需的设置”下单击“授予权限”，然后单击“是，授予权限”。 

    ![授予权限](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > 查看通知，确定是否已成功授予权限。  如果未成功授权，则 AzureCP 无法正常工作，并且无法使用 Azure Active Directory 安全组配置本地 SharePoint。

10. 在本地 SharePoint 场或替代的自定义声明提供程序解决方案中配置 AzureCP。  本示例使用 AzureCP。

    > [!NOTE]
    > 请注意，AzureCP 不是 Microsoft 产品，Microsoft 技术支持部门不会为其提供支持。 根据 https://yvand.github.io/AzureCP/ 中所述，在本地 SharePoint 场中下载、安装并配置 AzureCP 

11. **在本地 SharePoint 中向 Azure Active Directory 安全组授予访问权限**：- 必须向这些组授予对本地 SharePoint 中的应用程序的访问权限。  使用以下步骤设置访问 Web 应用程序的权限。

12. 在管理中心依次单击“应用程序管理”、“管理 Web 应用程序”，选择该 Web 应用程序以激活功能区，然后单击“用户策略”。

    ![管理中心](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. 在“Web 应用程序的策略”下，单击“添加用户”，选择区域，然后单击“下一步”。  单击“通讯簿”。

    ![Web 应用程序的策略](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. 搜索并添加“Azure Active Directory 安全组”，然后单击“确定”。

    ![添加安全组](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. 选择“权限”，然后单击“完成”。

    ![添加安全组](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. 在“Web 应用程序的策略”下，可以看到已添加“Azure Active Directory 组”。  组声明显示了“用户名”的“Azure Active Directory 安全组对象 ID”。

    ![添加安全组](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. 浏览到 SharePoint 站点集合，并同样在此处添加该组。 单击“站点设置”，然后单击“站点权限”和“授予权限”。  搜索“组角色声明”，分配权限级别，然后单击“共享”。

    ![添加安全组](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>为多个 Web 应用程序配置一个受信任标识提供者

该配置适用于单个 Web 应用程序，但如果你打算对多个 Web 应用程序使用相同的受信任标识提供者，则需要其他配置。 例如，假设我们已扩展了一个 Web 应用程序以使用 URL `https://portal.contoso.local`，现在也想要向 `https://sales.contoso.local` 进行用户身份验证。 为此，我们需要更新标识提供者以采用 WReply 参数并更新 Azure AD 中的应用程序注册以添加回复 URL。

1. 在 Azure 门户中，打开 Azure AD 目录。 单击“应用注册”  ，然后单击“查看所有应用程序”  。 单击之前创建的应用程序 (SharePoint SAML Integration)。

2. 单击“设置”  。

3. 在“设置”边栏选项卡中，单击“回复 URL”。 

4. 为附加的 Web 应用程序添加 URL 并将 `/_trust/default.aspx` 追加到 URL 后面（如 `https://sales.contoso.local/_trust/default.aspx`），并单击“保存”  。

5. 在 SharePoint Server 上打开 **SharePoint 2016 Management Shell**，并使用先前使用的受信任标识令牌颁发者的名称执行以下命令。

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. 在管理中心内，转到该 Web 应用程序并启用现有的受信任标识提供者。 记住还将登录页 URL 配置为自定义登录页 `/_trust/`。

7. 在管理中心内，单击该 Web 应用程序并选择“用户策略”  。 添加具有相应权限的用户，如本文中前面所示。

### <a name="fixing-people-picker"></a>固定人员选取器

现在，用户可以使用 Azure AD 中的标识登录到 SharePoint 2016，但仍有机会改进用户体验。 例如，搜索某个用户会在人员选取器中显示多个搜索结果。 声明映射中创建的 3 个声明类型各有一个搜索结果。 若要使用人员选取器选择某个用户，必须准确键入其用户名，并选择“名称”声明结果。 

![声明搜索结果](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

系统不会验证要搜索的值，因此，可能会出现拼写错误，或者用户意外选择分配错误的声明类型，例如 **SurName** 声明。 这可能会导致用户无法成功访问资源。

为了帮助解决这种情况，名为 [AzureCP](https://yvand.github.io/AzureCP/) 的开源解决方案为 SharePoint 2016 提供自定义的声明提供程序。 它使用 Azure AD Graph 来解析进入并执行验证功能的用户。 详细了解 [AzureCP](https://yvand.github.io/AzureCP/)。

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 门户中分配 Azure AD 安全组

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“本地 SharePoint”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“本地 SharePoint”  。

    ![应用程序列表中的本地 SharePoint](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”。 

    ![“添加分配”窗格](common/add-assign-user.png)

5. 搜索要使用的安全组，然后单击该组将其添加到“选择成员”部分。 依次单击“选择”、“分配”   。

    ![搜索安全组](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > 检查菜单栏中的通知，确定是否已在 Azure 门户中将该组成功分配到企业应用程序。

### <a name="create-sharepoint-on-premises-test-user"></a>创建本地 SharePoint 测试用户

在本部分，我们将在本地 SharePoint 中创建一个名为 Britta Simon 的用户。 与 [本地 SharePoint 支持团队](https://support.office.com/)协作，在本地 SharePoint 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的本地 SharePoint 磁贴时，应会自动登录到为其设置了 SSO 的本地 SharePoint。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
