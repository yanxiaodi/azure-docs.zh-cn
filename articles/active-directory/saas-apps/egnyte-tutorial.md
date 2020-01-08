---
title: 教程：Azure Active Directory 与 Egnyte 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Egnyte 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 808564f291328450b17db8eb7ea299c194c66400
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103520"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>教程：Azure Active Directory 与 Egnyte 集成

本教程介绍如何将 Egnyte 与 Azure Active Directory (Azure AD) 集成。
将 Egnyte 与 Azure AD 集成可提供以下优势：

* 你可以在 Azure AD 中控制谁有权访问 Egnyte。
* 可让用户使用其 Azure AD 帐户自动登录到 Egnyte（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Egnyte 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Egnyte 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Egnyte 支持 SP 发起的 SSO 

## <a name="adding-egnyte-from-the-gallery"></a>从库中添加 Egnyte

若要配置 Egnyte 与 Azure AD 的集成，需要从库中将 Egnyte 添加到托管 SaaS 应用列表。

**若要从库中添加 Egnyte，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Egnyte”，从结果面板中选择“Egnyte”，然后单击“添加”按钮添加应用程序    。

     ![结果列表中的 Egnyte](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Egnyte 的 Azure AD 单一登录。 
若要运行单一登录，需要在 Azure AD 用户与 Egnyte 相关用户之间建立链接关系。

若要配置并测试 Egnyte 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Egnyte 单一登录](#configure-egnyte-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Egnyte 测试用户](#create-egnyte-test-user) - 在 Egnyte 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式  。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Egnyte 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Egnyte”  应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Egnyte 域和 URL 单一登录信息](common/sp-signonurl.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<companyname>.egnyte.com`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Egnyte 客户端支持团队](https://www.egnyte.com/corp/contact_egnyte.html)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

5. 在“设置 Egnyte”部分中，根据需求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-egnyte-single-sign-on"></a>配置 Egnyte 单一登录

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Egnyte 公司站点。

2. 单击“设置”  。
   
    ![设置](./media/egnyte-tutorial/ic787819.png "设置")

3. 在菜单中，单击“设置”。 

    ![设置](./media/egnyte-tutorial/ic787820.png "设置")

4. 单击“配置”选项卡，并单击“安全性”。  

    ![安全](./media/egnyte-tutorial/ic787821.png "安全")

5. 在“单一登录身份验证”  部分中，执行以下步骤：

    ![单一登录身份验证](./media/egnyte-tutorial/ic787822.png "Single Sign On Authentication")   
    
    a. 对于“单一登录身份验证”，选择“SAML 2.0”。  
   
    b. 对于“标识提供者”，选择“AzureAD”。  
   
    c. 将从 Azure 门户复制的“登录 URL”粘贴到“标识提供者登录 URL”文本框   。
   
    d. 将从 Azure 门户复制的“Azure AD 标识符”粘贴到“标识提供者实体 ID”文本框   。
      
    e. 在记事本中打开从 Azure 门户下载的 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“标识提供者证书”文本框  。
   
    f. 对于“默认用户映射”，选择“电子邮件地址”。  
   
    g. 对于“使用特定于域的颁发者值”，选择“禁用”。  
   
    h. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension    
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Egnyte 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Egnyte”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Egnyte”  。

    ![应用程序列表中的 Egnyte 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-egnyte-test-user"></a>创建 Egnyte 测试用户

若要使 Azure AD 用户能够登录到 Egnyte，必须将这些用户预配到 Egnyte 中。 对于 Egnyte，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Egnyte 公司站点  。

2. 转到“设置”\>“用户和组”。 

3. 单击“添加新用户”，并选择要添加的用户的类型。 
   
    ![用户](./media/egnyte-tutorial/ic787824.png "用户")

4. 在“新建 Power User”部分中，执行以下步骤  ：
    
    ![新建标准用户](./media/egnyte-tutorial/ic787825.png "New Standard User")   

    a. 在“电子邮件”文本框中，输入用户的电子邮件，例如 Brittasimon\@contoso.com   。

    b. 在“用户名”文本框中输入用户的用户名，例如“Brittasimon”   。

    c. 选择“单一登录”作为“身份验证类型”   。
   
    d. 单击“ **保存**”。
    
    >[!NOTE]
    >Azure Active Directory 帐户持有者将收到一封通知电子邮件。
    >

>[!NOTE]
>可以使用 Egnyte 提供的任何其他 Egnyte 用户帐户创建工具或 API 来预配 AAD 用户帐户。
>

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Egnyte 磁贴时，应当会自动登录到你为其设置了 SSO 的 Egnyte。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

