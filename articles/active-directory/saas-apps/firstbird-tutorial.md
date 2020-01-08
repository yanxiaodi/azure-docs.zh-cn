---
title: 教程：Azure Active Directory 与 Firstbird 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Firstbird 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: acab1200-32d3-4f4b-953f-f2a7e812b6a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f192b402aebcaeb87e068a4248c0794484e6c313
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102438"
---
# <a name="tutorial-azure-active-directory-integration-with-firstbird"></a>教程：Azure Active Directory 与 Firstbird 集成

本教程介绍如何将 Firstbird 与 Azure Active Directory (Azure AD) 集成。
将 Firstbird 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 Firstbird。
* 可以让用户使用其 Azure AD 帐户自动登录到 Firstbird（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Firstbird 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Firstbird 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Firstbird 支持 **SP 和 IDP** 发起的 SSO
* Firstbird 支持恰时用户预配 

## <a name="adding-firstbird-from-the-gallery"></a>从库中添加 Firstbird

若要配置 Firstbird 与 Azure AD 的集成，需要从库中将 Firstbird 添加到托管 SaaS 应用列表。

**若要从库添加 Firstbird，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select_azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise_applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add_new_app.png)

4. 在搜索框中键入“Firstbird”，在结果面板中选择“Firstbird”，然后单击“添加”按钮添加应用程序。   

     ![结果列表中的 Firstbird](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为 **Britta Simon** 的测试用户的指示配置和测试 [应用程序名称] 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 [应用程序名称] 相关用户之间建立链接关系。

若要配置和测试 [应用程序名称] 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Firstbird 单一登录](#configure-firstbird-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Firstbird 测试用户](#create-firstbird-test-user) - 在 Firstbird 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式  。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 [应用程序名称] 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的 **Firstbird** 应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select_sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select_saml_option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit_urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Firstbird 域和 URL 单一登录信息](common/idp_intiated.png)

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<company-domain>.auth.1brd.com/saml/sp`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<company-domain>.auth.1brd.com/saml/callback`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![Firstbird 域和 URL 单一登录信息](common/metadata_upload_additional_signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<company-domain>.1brd.com/login`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Firstbird 客户端支持团队](mailto:support@firstbird.com)以获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. Firstbird 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![图像](common/edit_attribute.png)

7. 在“用户属性”  对话框的“用户声明”  部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | 名称 | 源属性|
    | ---------------| --------- |
    | first_name | `user.givenname` |
    | last_name | `user.surname` |
    | 电子邮件 | `user.mail` |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new_save_attribute.png)

    ![图像](common/new_attribute_details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以下载“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

### <a name="configure-firstbird-single-sign-on"></a>配置 Firstbird 单一登录

完成这些步骤后，请通过电子邮件将 Firstbird 的联合身份验证元数据 XML 以支持请求的形式发送至 [support@firstbird.com](mailto:support@firstbird.com)，主题为“SSO configuration”。

然后，Firstbird 就会将配置相应地存储在系统中，并为你的帐户激活 SSO。 然后，支持团队的成员会与你联系，对配置进行验证。

> [!NOTE]
> 需在合同中包括 SSO 选项。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new_user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user_properties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”  字段中，输入 BrittaSimon  。

    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension    
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Firstbird 的访问权限支持她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”  、“所有应用程序”  和“Firstbird”  。

    ![“企业应用程序”边栏选项卡](common/enterprise_applications.png)

2. 在应用程序列表中，键入并选择“Firstbird”。 

    ![应用程序列表中的 Firstbird 链接](common/all_applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users_groups_blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add_assign_user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-firstbird-test-user"></a>创建 Firstbird 测试用户

本部分将在 Firstbird 中创建一个名为 Britta Simon 的用户。 Firstbird 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 尝试访问 Firstbird 时，如果 Firstbird 中尚不存在用户，系统会创建一个新用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Firstbird 磁贴时，应当会自动登录到你为其设置了 SSO 的 Firstbird。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
