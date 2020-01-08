---
title: 教程：Azure Active Directory 与 Zoho One 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zoho One 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086232"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>教程：Azure Active Directory 与 Zoho One 集成

本教程介绍了如何将 Zoho One 与 Azure Active Directory (Azure AD) 进行集成。
将 Zoho One 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Zoho One。
* 可让用户使用其 Azure AD 帐户自动登录到 Zoho One（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zoho One 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Zoho One 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Zoho One 支持 **SP** 和 **IDP** 发起的 SSO

## <a name="adding-zoho-one-from-the-gallery"></a>从库中添加 Zoho One

若要配置 Zoho One 与 Azure AD 的集成，需要从库中将 Zoho One 添加到托管 SaaS 应用列表。

**若要从库中添加 Zoho One，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Zoho One”，在结果面板中选择“Zoho One”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 Zoho One](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Zoho One 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Zoho One 相关用户之间建立链接关系。

若要配置和测试 Zoho One 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Zoho One 单一登录](#configure-zoho-one-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Zoho One 测试用户](#create-zoho-one-test-user)** - 在 Zoho One 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Zoho One 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Zoho One”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    ![Zoho One 域和 URL 单一登录信息](common/idp-relay.png)

    a. 在“标识符”文本框中键入 URL：`one.zoho.com` 

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > 上面的“回复 URL”值不是实际值。  执行本教程稍后所述的“配置 Zoho One 单一登录”部分的步骤 4 时，我们将获取 `<saml-identifier>` 值。 

    c. 单击“设置其他 URL”  。

    d. 在“中继状态”文本框中键入 URL：`https://one.zoho.com` 

5. 如果要在“SP”发起的模式下配置应用程序，请执行以下步骤  ：


    ![Zoho One 域和 URL 单一登录信息](common/both-signonurl.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > 上面的“登录 URL”值不是实际值。  在本教程稍后所述的“配置 Zoho One 单一登录”部分，我们将使用实际“登录 URL”更新该值。  

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Zoho One”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-zoho-one-single-sign-on"></a>配置 Zoho One 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Zoho One 公司站点。

2. 在“组织”  选项卡上，单击“SAML 身份验证”  下的“设置”。 

    ![Zoho One 组织](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. 在弹出页面上执行以下步骤：

    ![Zoho One 登录](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    b. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    c. 单击“浏览”  来上传从 Azure 门户下载的**证书 (Base64)** 。

    d. 单击“ **保存**”。

4. 保存 SAML 身份验证设置后，复制“SAML 标识符”值并在其后追加“回复 URL”（替代 `<saml-identifier>`，例如 `https://accounts.zoho.com/samlresponse/one.zoho.com`），然后在“基本 SAML 配置”部分的“回复 URL”文本框中粘贴生成的值。    

    ![Zoho One SAML](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. 转到“域”  选项卡，然后单击“添加域”  。

    ![Zoho One 域](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. 在“添加域”  页面上，执行以下步骤：

    ![Zoho One 添加域](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. 在“域名”文本框中键入域，例如 contoso.com。 

    b. 单击“添加”  。

    >[!Note]
    >在添加域后，按照[这些](https://www.zoho.com/one/help/admin-guide/domain-verification.html)步骤验证域。 验证域后，请在 Azure 门户上“基本 SAML 配置”部分的“登录 URL”中使用该域名。  

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。  例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 Zoho One 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Zoho One”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Zoho One”  。

    ![应用程序列表中的 Zoho One 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-zoho-one-test-user"></a>创建 Zoho One 测试用户

若要使 Azure AD 用户能够登录到 Zoho One，必须将其预配到 Zoho One 中。 在 Zoho One 中，需要手动执行预配任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 Zoho One。

2. 在“用户”  选项卡上，单击“用户登录”  。

    ![Zoho One 用户](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. 在“添加用户”页上执行以下步骤： 

    ![Zoho One 添加用户](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. 在“姓名”文本框中，输入用户名，例如 Britta Simon   。
    
    b. 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如 brittasimon@contoso.com  。

    >[!Note]
    >从域列表中选择已验证的域。

    c. 单击“添加”  。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Zoho One”磁贴时，应会自动登录到设置了 SSO 的 Zoho One。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

