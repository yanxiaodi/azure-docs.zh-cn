---
title: 教程：Azure Active Directory 与 Palo Alto Networks - Aperture 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Palo Alto Networks - Aperture 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: fd498dc1c37ed6e9518fcefbdb237153504b5e98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095064"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>教程：Azure Active Directory 与 Palo Alto Networks - Aperture 的集成

在本教程中，了解如何将 Palo Alto Networks - Aperture 与 Azure Active Directory (Azure AD) 进行集成。
将 Palo Alto Networks - Aperture 与 Azure AD 集成可提供以下好处：

* 可在 Azure AD 中控制谁有权访问 Palo Alto Networks - Aperture。
* 可以让用户使用其 Azure AD 帐户自动登录到 Palo Alto Networks - Aperture（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Palo Alto Networks - Aperture 的集成，需具有以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Palo Alto Networks - Aperture 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Palo Alto Networks - Aperture 支持 **SP** 和 **IDP** 发起的 SSO

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>从库添加 Palo Alto Networks - Aperture

若要配置 Palo Alto Networks - Aperture 与 Azure AD 的集成，需要从库中将 Palo Alto Networks - Aperture 添加到托管的 SaaS 应用列表。

若要从库中添加 Palo Alto Networks - Aperture，请执行以下步骤： 

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Palo Alto Networks - Aperture”  ，从结果面板选择“Palo Alto Networks - Aperture”  ，然后单击“添加”  按钮来添加应用程序。

     ![结果列表中的 Palo Alto Networks - Aperture](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户，配置和测试 Palo Alto Networks - Aperture 的 Azure AD 单一登录。 
若要运行单一登录，需要在 Azure AD 用户与 Palo Alto Networks - Aperture 中相关用户之间建立链接关系。

若要配置和测试 Palo Alto Networks - Aperture 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Palo Alto Networks - Aperture 单一登录](#configure-palo-alto-networks---aperture-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Palo Alto Networks - Aperture 测试用户](#create-palo-alto-networks---aperture-test-user)** - 在 Palo Alto Networks - Aperture 中创建 Britta Simon 的对应用户，以链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Palo Alto Networks - Aperture 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 **Palo Alto Networks - Aperture** 应用程序集成页上，选择“单一登录”。 

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    ![Palo Alto Networks - Aperture 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![Palo Alto Networks - Aperture 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请联系 [Palo Alto Networks - Aperture 客户端支持团队](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Palo Alto Networks - Aperture”  部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>配置 Palo Alto Networks - Aperture 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Palo Alto Networks - Aperture。

2. 单击顶部菜单中的“设置”  。

    ![“设置”选项卡](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. 导航到“应用程序”部分，单击菜单左侧的“身份验证”窗体。  

    ![“身份验证”选项卡](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. 在“身份验证”  页上，执行以下步骤：
    
    ![身份验证选项卡](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. 在“单一登录”字段中选中“启用单一登录(支持的 SSP 提供者为 Okta, One login)”。  

    b. 在“标识提供者 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    c. 在“标识提供者证书”字段中单击“选择文件”  ，上传从 Azure AD 下载的证书。 

    d. 在“标识提供者 SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    e. 查看“Aperture 信息”部分中的 IdP 信息，并从“Aperture 密钥”字段下载证书。  

    f. 单击“ **保存**”。

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

在本部分中，通过授予 Britta Simon 访问 Palo Alto Networks - Aperture 的权限，使她能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Palo Alto Networks - Aperture”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Palo Alto Networks - Aperture”  。

    ![应用程序列表中的 Palo Alto Networks - Aperture 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-palo-alto-networks---aperture-test-user"></a>创建 Palo Alto Networks - Aperture 测试用户

在本部分中，在 Palo Alto Networks - Aperture 中创建名为 Britta Simon 的用户。 与 [Palo Alto Networks - Aperture 客户支持团队](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support)协作，在 Palo Alto Networks - Aperture 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Palo Alto Networks - Aperture 磁贴时，应当会自动登录到你为其设置了 SSO 的 Palo Alto Networks - Aperture。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

