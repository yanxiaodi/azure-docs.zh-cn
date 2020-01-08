---
title: 教程：Azure Active Directory 与 Folloze 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Folloze 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b6265bbf-8216-4229-bda7-8dd1023a5f80
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d7d9115ba7b5d60c6fbf61590aebd47a00c4b2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102164"
---
# <a name="tutorial-azure-active-directory-integration-with-folloze"></a>教程：Azure Active Directory 与 Folloze 集成

在本教程中，了解如何将 Folloze 与 Azure Active Directory (Azure AD) 集成。
将 Folloze 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Folloze。
* 可以让用户使用其 Azure AD 帐户自动登录到 Folloze（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Folloze 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Folloze 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Folloze 支持 **IDP** 发起的 SSO
* Folloze 支持实时用户预配。 

## <a name="adding-folloze-from-the-gallery"></a>从库中添加 Folloze

若要配置 Folloze 与 Azure AD 的集成，需要从库中将 Folloze 添加到托管 SaaS 应用列表。

**若要从库中添加 Folloze，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select_azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise_applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add_new_app.png)

4. 在搜索框中键入“Folloze”，在结果面板中选择“Folloze”，然后单击“添加”按钮以添加应用程序。   

     ![结果列表中的 Folloze](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为 **Britta Simon** 的测试用户使用 Folloze 配置和测试 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Folloze 相关用户之间建立链接关系。

若要配置和测试 Folloze 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Folloze 单一登录](#configure-folloze-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Folloze 测试用户](#create-folloze-test-user) - 在 Folloze 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式  。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Folloze 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Folloze  应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select_sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select_saml_option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit_urls.png)

4. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成  。

    ![Folloze 域和 URL 单一登录信息](common/preintegrated.png)

5. Folloze 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![图像](common/edit_attribute.png)

6. 在“用户属性”  对话框的“用户声明”  部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | 名称 |  源属性|
    | ---------------| --------- |
    | 电子邮件 | user.othermail |
    | Nameasemail | user.userprincipalname |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new_save_attribute.png)

    ![图像](common/new_attribute_details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“ **保存**”。

7. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

8. 在“设置 Folloze”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy_configuration_urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-folloze-single-sign-on"></a>配置 Folloze 单一登录

若要在 **Folloze** 端配置单一登录，需要将下载的**证书(Base64)** 以及从 Azure 门户复制的相应 URL 发送给 [Folloze 支持团队](mailto:support@folloze.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new_user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user_properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension    
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Folloze 的访问权限支持她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”  、“所有应用程序”  和“Folloze”  。

    ![“企业应用程序”边栏选项卡](common/enterprise_applications.png)

2. 在应用程序列表中，键入并选择“Folloze”。 

    ![应用程序列表中的 Folloze 链接](common/all_applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users_groups_blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add_assign_user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-folloze-test-user"></a>创建 Folloze 测试用户

在本部分，我们会在 Folloze 中创建一个名为 Britta Simon 的用户。 Folloze 支持在默认情况下保持启用的恰时用户预配。 此部分不存在任何操作项。 如果 Folloze 中不存在用户，则会在身份验证后创建一个新用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Folloze 磁贴时，应当会自动登录到你为其设置了 SSO 的 Folloze。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
