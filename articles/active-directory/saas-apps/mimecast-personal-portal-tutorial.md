---
title: 教程：Azure Active Directory 与 Mimecast Personal Portal 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Mimecast Personal Portal 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfafa1157619e151f97fcf9c8a410a0644354b80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097384"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>教程：Azure Active Directory 与 Mimecast Personal Portal 的集成

本教程介绍如何将 Mimecast Personal Portal 与 Azure Active Directory (Azure AD) 集成。
将 Mimecast Personal Portal 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Mimecast Personal Portal。
* 可以让用户使用其 Azure AD 帐户自动登录到 Mimecast Personal Portal（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Mimecast Personal Portal 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Mimecast Personal Portal 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Mimecast Personal Portal 支持 **SP** 发起的 SSO

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>从库中添加 Mimecast Personal Portal

要配置 Mimecast Personal Portal 与 Azure AD 的集成，需要从库中将 Mimecast Personal Portal 添加到托管 SaaS 应用列表。

若要从库中添加 Mimecast Personal Portal，请执行以下步骤： 

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 Mimecast Personal Portal  ，在结果面板中选择“Mimecast Personal Portal”  ，并单击“添加”  按钮添加该应用程序。

     ![结果列表中的 Mimecast Personal Portal](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”  的测试用户配置和测试 Mimecast Personal Portal 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Mimecast Personal Portal 相关用户之间建立链接关系。

若要配置和测试 Mimecast Personal Portal 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Mimecast Personal Portal 单一登录](#configure-mimecast-personal-portal-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Mimecast Personal Portal 测试用户](#create-mimecast-personal-portal-test-user)  - 在 Mimecast Personal Portal 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Mimecast Personal Portal 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Mimecast Personal Portal”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![Mimecast Personal Portal 域和 URL 单一登录信息](common/sp-identifier-reply.png)

    a. 在“登录 URL”  文本框中，键入 URL： 

    | 区域  |  值 | 
    | --------------- | --------------- | 
    | 欧洲          | `https://eu-api.mimecast.com/login/saml`|
    | 美国   | `https://us-api.mimecast.com/login/saml`|
    | 南非    | `https://za-api.mimecast.com/login/saml`|
    | 澳大利亚       | `https://au-api.mimecast.com/login/saml`|
    | 海外        | `https://jer-api.mimecast.com/login/saml`|

    b. 在“标识符”文本框中，使用以下模式键入 URL  ：

    | 区域  |  值 | 
    | --------------- | --------------- |
    | 欧洲          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | 美国   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | 南非    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | 澳大利亚       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | 海外        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. 在“回复 URL”  文本框中键入 URL： 

    | 区域  |  值 | 
    | --------------- | --------------- | 
    | 欧洲          | `https://eu-api.mimecast.com/login/saml`|
    | 美国   | `https://us-api.mimecast.com/login/saml`|
    | 南非    | `https://za-api.mimecast.com/login/saml`|
    | 澳大利亚       | `https://au-api.mimecast.com/login/saml`|
    | 海外        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > 标识符非实际值。 请使用实际标识符更新此值。 请联系 [Mimecast Personal Portal 客户端支持团队](https://www.mimecast.com/customer-success/technical-support/)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

4. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Mimecast Personal Portal”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>配置 Mimecast Personal Portal 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Mimecast Personal Portal。

2. 转到“服务”\>“应用程序”  。
   
    ![应用程序](./media/mimecast-personal-portal-tutorial/ic794998.png "应用程序")

3. 单击“身份验证配置文件”。 
   
    ![身份验证配置文件](./media/mimecast-personal-portal-tutorial/ic794999.png "身份验证配置文件")

4. 单击“新建身份验证配置文件”。 
   
    ![新建身份验证配置文件](./media/mimecast-personal-portal-tutorial/ic795000.png "新建身份验证配置文件")

5. 在“身份验证配置文件”  部分中，执行以下步骤：
   
    ![身份验证配置文件](./media/mimecast-personal-portal-tutorial/ic795001.png "身份验证配置文件")
   
    a. 在“描述”文本框中，键入配置名称。 
   
    b. 选择“对 Mimecast Personal Portal 强制实施 SAML 身份验证”。 
   
    c. 对于“提供程序”，选择“Azure Active Directory”。  
   
    d. 在“颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。
   
    e. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。
   
    f. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    g. 在记事本中打开从 Azure 门户下载的“base-64”编码证书，将其内容复制到剪贴板，然后再粘贴到“标识提供者证书（元数据）”文本框   。

    h. 选择“允许单一登录”。 
   
    i. 单击“ **保存**”。

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

在本部分中，通过授予 Britta Simon 访问 Mimecast Personal Portal 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Mimecast Personal Portal”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“Mimecast Personal Portal”  。

    ![应用程序列表中的“Mimecast Personal Portal”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-mimecast-personal-portal-test-user"></a>创建 Mimecast Personal Portal 测试用户

为了使 Azure AD 用户能够登录到 Mimecast Personal Portal，必须将其预配到 Mimecast Personal Portal 中。 对于 Mimecast Personal Portal，需要手动执行预配。

需要注册一个域，才能创建用户。

**若要配置用户预配，请执行以下步骤：**

1. 以管理员身份登录到 **Mimecast Personal Portal**。

2. 转到“目录”\>“内部”。 
   
    ![目录](./media/mimecast-personal-portal-tutorial/ic795003.png "目录")

3. 单击“注册新域”。 
   
    ![注册新域](./media/mimecast-personal-portal-tutorial/ic795004.png "注册新域")

4. 在创建新域后，单击“新建地址”。 
   
    ![新建地址](./media/mimecast-personal-portal-tutorial/ic795005.png "新建地址")

5. 在“新建地址”对话框中，对要预配的有效 Azure AD 帐户执行下列步骤：
   
    ![保存](./media/mimecast-personal-portal-tutorial/ic795006.png "保存")
   
    a. 在“电子邮件地址”文本框中，键入用户的电子邮件地址（如 **BrittaSimon\@contoso.com**）。  
    
    b. 在“全局名称”  文本框中，将“用户名”  键入为“BrittaSimon”  。

    c. 在“密码”  和“确认密码”  文本框中，键入用户的“密码”  。
   
    b. 单击“ **保存**”。

>[!NOTE]
>可以使用 Mimecast Personal Portal 提供的任何其他 Mimecast Personal Portal 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Mimecast Personal Portal 磁贴时，应当会自动登录到已为其设置了 SSO 的 Mimecast Personal Portal。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

