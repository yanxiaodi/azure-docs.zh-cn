---
title: 教程：Azure Active Directory 与 Work.com 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Work.com 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7a6dc16eef1bb36a5bd6cbf0502a83481230bc0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087079"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>教程：Azure Active Directory 与 Work.com 的集成

本教程介绍如何将 Work.com 与 Azure Active Directory (Azure AD) 集成。
将 Work.com 与 Azure AD 集成提供以下优势：

* 可以在 Azure AD 中控制谁有权访问 Work.com。
* 可让用户使用其 Azure AD 帐户自动登录到 Work.com（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Work.com 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 Work.com 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Work.com 支持 **SP** 发起的 SSO

## <a name="adding-workcom-from-the-gallery"></a>从库中添加 Work.com

若要配置 Work.com 与 Azure AD 的集成，需要从库中将 Work.com 添加到托管 SaaS 应用列表。

**若要从库添加 Work.com，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **Work.com**，在结果面板中选择“Work.com”，然后单击“添加”按钮添加该应用程序。  

    ![结果列表中的“Work.com”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Work.com 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Work.com 相关用户之间建立链接关系。

若要配置和测试 Work.com 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Work.com 单一登录](#configure-workcom-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Work.com 测试用户](#create-workcom-test-user)** - 在 Work.com 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

>[!NOTE]
>若要配置单一登录，需要设置一个自定义 Work.com 域名。 需要至少定义一个域名，测试该域名，并将其部署到整个组织。

若要配置 Work.com 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Work.com”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Work.com 域和 URL 单一登录信息](common/sp-signonurl.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Work.com 客户端支持团队](https://help.salesforce.com/articleView?id=000159855&type=3)获取该值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Work.com”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-workcom-single-sign-on"></a>配置 Work.com 单一登录

1. 以管理员身份登录到 Work.com 租户。

2. 转到“设置”  。
   
    ![设置](./media/work-com-tutorial/ic794108.png "设置")

3. 在左侧导航窗格中的“管理”  部分中，单击“域管理”  以展开相关部分，并单击“我的域”  ，打开“我的域”  页。 
   
    ![我的域](./media/work-com-tutorial/ic767825.png "我的域")

4. 要验证域是否已正确设置，请确保它在“步骤 4 部署到用户”  中，并复查“我的域设置”  。
   
    ![部署到用户的域](./media/work-com-tutorial/ic784377.png "部署到用户的域")

5. 登录到 Work.com 租户。

6. 转到“设置”  。
    
    ![设置](./media/work-com-tutorial/ic794108.png "设置")

7. 展开“安全控件”  菜单，并单击“单一登录设置”  。
    
    ![单一登录设置](./media/work-com-tutorial/ic794113.png "Single Sign-On Settings")

8. 在“单一登录设置”  对话框页上，执行以下步骤：
    
    ![已启用 SAML](./media/work-com-tutorial/ic781026.png "已启用 SAML")
    
    a. 选择“已启用 SAML”  。
    
    b. 单击“新建”  。

9. 在“SAML 单一登录设置”  部分中，执行以下步骤：
    
    ![SAML 单一登录设置](./media/work-com-tutorial/ic794114.png "SAML Single Sign-On Settings")
    
    a. 在“名称”  文本框中，键入配置名称。  
       
    > [!NOTE]
    > 提供“名称”  值时会自动填充“API 名称”  文本框。
    
    b. 在“颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。
    
    c. 若要从 Azure 门户上传已下载的证书，请单击“浏览”。 
    
    d. 在“实体 ID”  文本框中，键入 `https://salesforce-work.com`。
    
    e. 对于“SAML 标识类型”  ，选择“断言包含用户对象的联合 ID”  。
    
    f. 对于“SAML 标识位置”，选择“标识位于 Subject 语句的 NameIdentifier 元素中”   。
    
    g. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    h. 在“标识提供者注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。
    
    i. 对于“服务提供程序发起的请求绑定”  ，请选择“HTTP Post”  。
    
    j. 单击“ **保存**”。

10. 在 Work.com 经典门户内，从左侧导航窗格中，单击“域管理”  以展开相关部分，并单击“我的域”  ，打开“我的域”  页。 
    
    ![我的域](./media/work-com-tutorial/ic794115.png "我的域")

11. 在“我的域”  页上的“登录页品牌打造”  部分中，单击“编辑”  。
    
    ![登录页品牌打造](./media/work-com-tutorial/ic767826.png "登录页品牌打造")

12. 在“登录页品牌打造”  页上的“身份验证服务”  部分中，会显示 **SAML SSO 设置**的名称。 选择它，并单击“保存”  。
    
    ![登录页品牌打造](./media/work-com-tutorial/ic784366.png "登录页品牌打造")

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

在本部分中，通过授予 Britta Simon 访问 Work.com 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Work.com”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Work.com”  。

    ![“应用程序”列表中的“Work.com”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-workcom-test-user"></a>创建 Work.com 测试用户

要使 Azure Active Directory 用户能够登录，必须将这些用户预配到 Work.com 中。 对于 Work.com，需要手动执行预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：

1. 以管理员身份登录到 Work.com 公司站点。

2. 转到“设置”  。
   
    ![设置](./media/work-com-tutorial/IC794108.png "设置")

3. 转到“管理用户”\>“用户”  。
   
    ![管理用户](./media/work-com-tutorial/IC784369.png "管理用户")

4. 单击“新建用户”  。
   
    ![所有用户](./media/work-com-tutorial/IC794117.png "所有用户")

5. 在“编辑用户”部分，在要预配到相关文本框的有效 Azure AD 帐户的属性中执行以下步骤：
   
    ![用户编辑](./media/work-com-tutorial/ic794118.png "用户编辑")
   
    a. 在“名字”文本框中，键入用户的**名字** (**Britta**)  。
    
    b. 在“姓氏”文本框中，键入用户的**姓氏** (**Simon**)  。
    
    c. 在“别名”文本框中，键入用户的**别名** (**BrittaS**)  。
    
    d. 在“电子邮件”文本框中，键入用户的**电子邮件地址** Brittasimon@contoso.com。 
    
    e. 在“用户名”  文本框中，键入用户的用户名（例如 Brittasimon@contoso.com）。
    
    f. 在“昵称”  文本框中，键入用户的**昵称** (**Simon**)。
    
    g. 选择“角色”  、“用户许可证”  和“配置文件”  。
    
    h. 单击“ **保存**”。  
      
    > [!NOTE]
    > Azure AD 帐户持有者会收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
    > 

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Work.com”磁贴时，应会自动登录到设置了 SSO 的 Work.com。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

