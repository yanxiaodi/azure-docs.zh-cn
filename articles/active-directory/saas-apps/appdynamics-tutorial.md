---
title: 教程：Azure Active Directory 与 AppDynamics 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 AppDynamics 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3d0a8151610658d8302d90f9a3cb3159a877925
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106866"
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>教程：Azure Active Directory 与 AppDynamics 集成

在本教程中，了解如何将 AppDynamics 与 Azure Active Directory (Azure AD) 集成。
将 AppDynamics 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 AppDynamics。
* 可以让用户使用其 Azure AD 帐户自动登录到 AppDynamics（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 AppDynamics 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 AppDynamics 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* AppDynamics 支持 SP 发起的 SSO 

* AppDynamics 支持实时用户预配 

## <a name="adding-appdynamics-from-the-gallery"></a>从库中添加 AppDynamics

若要配置 AppDynamics 与 Azure AD 的集成，需要从库中将 AppDynamics 添加到托管 SaaS 应用列表。

**若要从库中添加 AppDynamics，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“AppDynamics”，在结果面板中选择“AppDynamics”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 AppDynamics](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们使用名为“Britta Simon”的测试用户 配置和测试 AppDynamics 的 Azure AD 单一登录  。
若要使单一登录有效，需要在 Azure AD 用户与 AppDynamics 相关用户之间建立链接关系。

若要配置和测试 AppDynamics 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 AppDynamics 单一登录](#configure-appdynamics-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 AppDynamics 测试用户](#create-appdynamics-test-user)  - 在 AppDynamics 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 AppDynamics 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 AppDynamics 应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![AppDynamics 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.saas.appdynamics.com?accountName=<companyname>` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<companyname>.saas.appdynamics.com/controller` 

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [AppDynamics 客户端支持团队](https://www.appdynamics.com/support/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 AppDynamics”部分，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-appdynamics-single-sign-on"></a>配置 AppDynamics 单一登录

1. 在其他 Web 浏览器窗口中，以管理员身份登录 AppDynamics 公司站点。

1. 在顶部工具栏中，单击“设置”  ，并单击“管理”  。

    ![管理](./media/appdynamics-tutorial/ic790216.png "Administration")

1. 单击“身份验证提供程序”  选项卡。

    ![身份验证提供程序](./media/appdynamics-tutorial/ic790224.png "Authentication Provider")

1. 在“身份验证提供程序”  部分中，执行以下步骤：

   ![SAML 配置](./media/appdynamics-tutorial/ic790225.png "SAML 配置")

   a. 对于“身份验证提供程序”  ，选择“SAML”  。

   b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

   c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

   d. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“证书”  文本框中

   e. 单击“ **保存**”。

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

在本部分中，通过向 Britta Simon 授予 AppDynamics 的访问权限支持使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“AppDynamics”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“AppDynamics”  。

    ![应用程序列表中的 AppDynamics 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-appdynamics-test-user"></a>创建 AppDynamics 测试用户

本部分将在 AppDynamics 中创建名为“Britta Simon”的用户。 AppDynamics 支持实时预配（在默认情况下为启用状态）。 此部分不存在任何操作项。 在尝试访问 AppDynamics 期间，如果该用户不存在，则会创建一个新用户。

>[!Note]
>如果需要手动创建用户，请联系  [AppDynamics 客户端支持团队](https://www.appdynamics.com/support/)。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 AppDynamics 磁贴时，应会自动登录到为其设置了 SSO 的 AppDynamics。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

