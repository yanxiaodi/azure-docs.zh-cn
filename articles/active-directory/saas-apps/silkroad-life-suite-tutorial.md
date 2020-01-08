---
title: 教程：Azure Active Directory 与 SilkRoad Life Suite 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SilkRoad Life Suite 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 63165da69815c77afb8692e1e68c1710beb8df8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090825"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>教程：Azure Active Directory 与 SilkRoad Life Suite 集成

在本教程中，了解如何将 SilkRoad Life Suite 与 Azure Active Directory (Azure AD) 集成。
将 SilkRoad Life Suite 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 SilkRoad Life Suite。
* 可以让用户使用其 Azure AD 帐户自动登录到 SilkRoad Life Suite（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SilkRoad Life Suite 的集成，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 启用了单一登录的 SilkRoad Life Suite 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SilkRoad Life Suite 支持 **SP** 发起的 SSO

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>从库中添加 SilkRoad Life Suite

要配置 SilkRoad Life Suite 与 Azure AD 的集成，需要从库中将 SilkRoad Life Suite 添加到托管 SaaS 应用列表。

**若要从库中添加 SilkRoad Life Suite，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“SilkRoad Life Suite”，在结果面板中选择“SilkRoad Life Suite”，然后单击“添加”按钮添加该应用程序。   

    ![结果列表中的 SilkRoad Life Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 **Britta Simon** 的测试用户配置和测试 SilkRoad Life Suite 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 SilkRoad Life Suite 中相关用户之间建立链接关系。

若要配置和测试 SilkRoad Life Suite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 SilkRoad Life Suite 单一登录](#configure-silkroad-life-suite-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 SilkRoad Life Suite 测试用户](#create-silkroad-life-suite-test-user)** - 在 SilkRoad Life Suite 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 SilkRoad Life Suite 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **SilkRoad Life Suite** 应用程序集成页上，选择“单一登录”。 

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分，如果有**服务提供程序元数据文件**，请执行以下步骤：

    > [!NOTE]
    > 你将获取本教程中稍后介绍的**服务提供程序元数据文件**。

    a. 单击“上传元数据文件”  。

    ![image](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![image](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”  和“回复 URL”  值会自动填充在“基本 SAML 配置”部分：

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > 如果**标识符**和**回复 URL** 值未自动填充，则请按要求手动填充这些值。

    d. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.silkroad-eng.com/Authentication/`。

5. 在“基本 SAML 配置”  部分中，如果你没有**服务提供程序元数据文件**，请执行以下步骤：

    ![SilkRoad Life Suite 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.silkroad-eng.com/Authentication/`。

    b. 在“标识符”框中，使用以下模式键入 URL： 

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. 在“回复 URL”文本框中，使用以下模式键入 URL： 

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [SilkRoad Life Suite 客户端支持团队](https://www.silkroad.com/locations/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 SilkRoad Life Suite”  部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>配置 SilkRoad Life Suite 单一登录

1. 以管理员身份登录到你的 SilkRoad 公司站点。

    > [!NOTE]
    > 若要获取对 SilkRoad Life Suite 身份验证应用程序的访问权限以配置 Microsoft Azure AD 的联合身份验证，请联系 SilkRoad 支持或 SilkRoad 服务代表。

1. 转到“服务提供商”  ，并单击“联合身份验证详细信息”  。

    ![Azure AD 单一登录](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. 单击“下载联合元数据”  ，并在计算机上保存该元数据文件。 在 Azure 门户的“基本 SAML 配置”  部分中，使用所下载的联合元数据作为**服务提供程序元数据文件**。

    ![Azure AD 单一登录](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. 在 **SilkRoad** 应用程序中，单击“身份验证源”  。

    ![Azure AD 单一登录](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. 单击“添加身份验证源”  。

    ![Azure AD 单一登录](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. 在“添加身份验证源”  部分中，执行以下步骤：

    ![Azure AD 单一登录](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. 在“选项 2 - 元数据文件”  下，单击“浏览”  上传从 Azure 门户下载的元数据文件。
  
    b. 单击“使用文件数据创建标识提供者”  。

1. 在“身份验证源”  部分中，单击“编辑”  。

    ![Azure AD 单一登录](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. 在“编辑身份验证源”  对话框中，执行以下步骤：

    ![Azure AD 单一登录](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. 对于“启用”  ，请选择“是”  。

    b. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。  

    c. 在“IdP 说明”  文本框中，键入配置说明（例如：“Azure AD SSO”  ）。

    d. 在“元数据文件”  文本框中，上传从 Azure 门户下载的**元数据**文件。
  
    e. 在“IdP 名称”  文本框中，键入特定于配置的名称（例如：“Azure SP”  ）。
  
    f. 在“注销服务 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    g. 在“登录服务 URL”  文本框中，粘贴从 Azure 门户复制的“登录 URL”  值。

    h. 单击“ **保存**”。

1. 禁用所有其他身份验证源。

    ![Azure AD 单一登录](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

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

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SilkRoad Life Suite 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“SilkRoad Life Suite”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“SilkRoad Life Suite”  。

    ![应用程序列表中的 SilkRoad Life Suite 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-silkroad-life-suite-test-user"></a>创建 SilkRoad Life Suite 测试用户

在本部分中，将在 SilkRoad Life Suite 中创建一个名为 Britta Simon 的用户。 与 [SilkRoad Life Suite 客户端支持团队](https://www.silkroad.com/locations/)协作，在 SilkRoad Life Suite 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 SilkRoad Life Suite 磁贴时，应当会自动登录到你为其设置了 SSO 的 SilkRoad Life Suite。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
