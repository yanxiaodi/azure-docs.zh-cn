---
title: 教程：Azure Active Directory 与 Jive 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Jive 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 7af47cf02d52abf8783eb1eb5da171b208ed07c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67099374"
---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>教程：Azure Active Directory 与 Jive 集成

在本教程中，了解如何将 Jive 与 Azure Active Directory (Azure AD) 集成。
将 Jive 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Jive。
* 可以让用户使用其 Azure AD 帐户自动登录到 Jive（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Jive 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Jive 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Jive 支持 SP 发起的 SSO 
* Jive 支持[**自动**用户预配](jive-provisioning-tutorial.md)

## <a name="adding-jive-from-the-gallery"></a>从库中添加 Jive

要配置 Jive 与 Azure AD 的集成，需要从库中将 Jive 添加到托管 SaaS 应用列表。

**若要从库中添加 Jive，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Jive”，在结果面板中选择“Jive”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 Jive](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于一个名为“Britta Simon”的测试用户配置并测试 Jive 的 Azure AD 单一登录。 
若要运行单一登录，需要在 Azure AD 用户与 Jive 相关用户之间建立链接关系。

若要使用 Jive 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Jive 单一登录](#configure-jive-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Jive 测试用户](#create-jive-test-user) - 在 Jive 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户  。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要使用 Jive 配置 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Jive”  应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Jive 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<instance name>.jivecustom.com` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<instance name>.jiveon.com` 

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Jive 客户端支持团队](https://www.jivesoftware.com/services-support/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Jive”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-jive-single-sign-on"></a>配置 Jive 单一登录

1. 若要在“Jive”  端配置单一登录，请以管理员身份登录到 Jive 租户。

1. 在顶部菜单中，单击“SAML”  。

    ![在应用端配置单一登录](./media/jive-tutorial/tutorial_jive_002.png)

    a. 在“常规”  选项卡下选择“已启用”  。

    b. 单击“保存所有 SAML 设置”  按钮。

1. 导航到“IDP 元数据”  选项卡。

    ![在应用端配置单一登录](./media/jive-tutorial/tutorial_jive_003.png)

    a. 复制下载的元数据 XML 文件的内容，并将其粘贴到“标识提供者(IDP)元数据”  文本框中。

    b. 单击“保存所有 SAML 设置”  按钮。

1. 选择“用户属性映射”  选项卡。

    ![在应用端配置单一登录](./media/jive-tutorial/tutorial_jive_004.png)

    a. 在“电子邮件”  文本框中，复制并粘贴“mail”  值的属性名称。

    b. 在“名字”  文本框中，复制并粘贴“givenname”  值的属性名称。

    c. 在“姓氏”  文本框中，复制并粘贴“surname”  值的属性名称。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”  字段中键入 brittasimon@yourcompanydomain.extension   
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Jive 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Jive”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Jive”  。

    ![应用程序列表中的 Jive 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-jive-test-user"></a>创建 Jive 测试用户

本部分要在 Jive 中创建名为“Britta Simon”的用户。 Jive 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](jive-provisioning-tutorial.md)。

如果需要手动创建用户，请与 [Jive 客户端支持团队](https://www.jivesoftware.com/services-support/)协作，将用户添加到 Jive 平台中。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Jive 磁贴时，应当会自动登录到为其设置了 SSO 的 Jive。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

[配置用户预配](jive-provisioning-tutorial.md)