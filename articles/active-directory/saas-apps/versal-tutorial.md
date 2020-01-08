---
title: 教程：Azure Active Directory 与 Versal 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Versal 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 837ba4043fe27c15f75c4d401878dfdb3328b82f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087595"
---
# <a name="tutorial-azure-active-directory-integration-with-versal"></a>教程：Azure Active Directory 与 Versal 集成

在本教程中，了解如何将 Versal 与 Azure Active Directory (Azure AD) 集成。
将 Versal 与 Azure AD 集成可提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Versal。
* 可以让用户使用其 Azure AD 帐户自动登录到 Versal（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Versal 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 启用了单一登录的 Versal 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Versal 支持 IDP发起的 SSO 

## <a name="adding-versal-from-the-gallery"></a>从库中添加 Versal

若要配置 Versal 与 Azure AD 的集成，需要从库中将 Versal 添加到托管 SaaS 应用的列表。

**若要从库中添加 Versal，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“Versal”  ，在结果面板中选择“Versal”  ，然后单击“添加”  按钮添加该应用程序。

     ![结果列表中的 Versal](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，我们基于名为“Britta Simon”的测试用户为 Versal 配置和测试 Azure AD 单一登录。 
若要使单一登录有效，需要在 Azure AD 用户与 Versal 相关用户之间建立关联。

若要配置并测试 Versal 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Versal 单一登录](#configure-versal-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Versal 测试用户](#create-versal-test-user)** - 在 Versal 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

要为 Versal 配置 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的 **Versal** 应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    ![Versal 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中键入 URL：`VERSAL` 

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://versal.com/sso/saml/orgs/<organization_id>`

    > [!NOTE]
    > 答复 URL 值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [Versal 客户端支持团队](https://support.versal.com/hc/)，获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. Versal 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 Versal 应用程序要求通过 **user.mail** 对 **nameidentifier** 进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射。 

    ![image](common/edit-attribute.png)

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Versal”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-versal-single-sign-on"></a>配置 Versal 单一登录

若要在  Versal 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [Versal 支持团队](https://support.versal.com/hc/)。  他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension。  例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Versal 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Versal”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Versal”  。

    ![应用程序列表中的 Versal 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-versal-test-user"></a>创建 Versal 测试用户

在本部分中，将在 Versal 中创建一个名为“Britta Simon”的用户。 请按照[创建 SAML 测试用户](https://support.versal.com/hc/articles/115011672887-Creating-a-SAML-test-user)支持指南，在组织内创建用户 Britta Simon。 使用单一登录前，必须先在 Versal 中创建并激活用户。 

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用嵌入在网站内的 Versal 课程测试 Azure AD 单一登录配置。
请参阅[嵌入组织课程](https://support.versal.com/hc/en-us/articles/203271866-Embedding-organizational-courses) **SAML 单一登录**支持指南，获取有关如何嵌入支持 Azure AD 单一登录的 Versal 课程的说明。 

你将需要创建一个课程，与组织进行共享，并发布它以测试课程嵌入。 有关详细信息，请参阅[创建课程](https://support.versal.com/hc/en-us/articles/203722528-Create-a-course)、[发布课程](https://support.versal.com/hc/en-us/articles/203753398-Publishing-a-course)，以及[课程和学员管理](https://support.versal.com/hc/en-us/articles/206029467-Course-and-learner-management)。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

