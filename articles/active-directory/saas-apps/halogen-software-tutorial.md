---
title: 教程：Azure Active Directory 与 Halogen Software 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Halogen Software 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8e1f4f0f2bd3521a312523fa9e36dcf14492862
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101377"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>教程：Azure Active Directory 与 Halogen Software 集成

本教程介绍如何将 Halogen Software 与 Azure Active Directory (Azure AD) 集成。
将 Halogen Software 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 Halogen Software。
* 可以让用户使用其 Azure AD 帐户自动登录到 Halogen Software（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Halogen Software 的集成，需要具有以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Halogen Software 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Halogen Software 支持 **SP** 发起的 SSO

## <a name="adding-halogen-software-from-the-gallery"></a>从库中添加 Halogen Software

要配置 Halogen Software 与 Azure AD 的集成，需要从库中将 Halogen Software 添加到托管 SaaS 应用列表。

**若要从库中添加 Halogen Software，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Halogen Software”，在结果面板中选择“Halogen Software”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 Halogen Software](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分将基于名为“Britta Simon”  的测试用户配置和测试 Halogen Software 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Halogen Software 相关用户之间建立链接关系。

若要配置并测试 Halogen Software 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Halogen Software 单一登录](#configure-halogen-software-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Halogen Software 测试用户](#create-halogen-software-test-user)** - 在 Halogen Software 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Halogen Software 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Halogen Software”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Halogen Software 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://global.hgncloud.com/<companyname>` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL  ：

    | |
    |--|
    | `https://global.halogensoftware.com/<companyname>`|
    | `https://global.hgncloud.com/<companyname>`|
    | |

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Halogen Software 客户端支持团队](https://support.halogensoftware.com/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Halogen Software”部分中，根据需要复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-halogen-software-single-sign-on"></a>配置 Halogen Software 单一登录

1. 在另一个浏览器窗口中，以管理员身份登录到 **Halogen Software** 应用程序。

2. 单击“**选项**”选项卡。
  
    ![什么是 Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_12.png)

3. 在左侧导航窗格中，单击“SAML 配置”。 
  
    ![什么是 Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_13.png)

4. 在“SAML 配置”  页上，执行以下步骤：

    ![什么是 Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_14.png)

    a. 选择 **NameID** 作为**唯一标识符**。

    b. 对于“唯一标识符映射到”，请选择“用户名”  。 
  
    c. 要上载已下载的元数据文件，请单击“浏览”选择该文件，并单击“上载文件”。  

    d. 若要测试配置，请单击“运行测试”。 

    > [!NOTE]
    > 需要等待消息“*SAML 测试已完成。请关闭此窗口*”。 然后，关闭打开的浏览器窗口。 仅在测试完成的情况下，才会启用“启用 SAML”复选框。 

    e. 选择“启用 SAML”  。

    f. 单击“保存更改”  。

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

在本部分中，通过授予 Britta Simon 访问 Halogen Software 的权限，以允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Halogen Software”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Halogen Software”。 

    ![应用程序列表中的 Halogen Software 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-halogen-software-test-user"></a>创建 Halogen Software 测试用户

本部分的目的是在 Halogen Software 中创建名为“Britta Simon”的用户。

**若要在 Halogen Software 中创建名为“Britta Simon”的用户，请执行以下步骤：**

1. 以管理员身份登录到 **Halogen Software** 应用程序。

2. 单击“用户中心”选项卡，并单击“创建用户”。  

    ![什么是 Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_300.png)  

3. 在“新建用户”  对话框页上，执行以下步骤：

    ![什么是 Azure AD Connect](./media/halogen-software-tutorial/tutorial_halogen_301.png)

    a. 在“名字”文本框中，键入用户的名字（如“Britta”）   。

    b. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）   。

    c. 在 Azure 门户的“用户名”文本框中，键入用户名“Britta Simon”   。

    d. 在“密码”文本框中，键入 Britta 的密码。 

    e. 单击“ **保存**”。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Halogen Software”磁贴时，应会自动登录到已为其设置了 SSO 的 Halogen Software。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)