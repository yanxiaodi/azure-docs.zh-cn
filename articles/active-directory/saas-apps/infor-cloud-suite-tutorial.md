---
title: 教程：Azure Active Directory 与 Infor CloudSuite 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Infor CloudSuite 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a2f4f843-00d2-4522-a29d-6496cc5a781a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9bb3d260b808e61f15ec85752c299bfeb3ab6fd
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588563"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-cloudsuite"></a>教程：Azure Active Directory 与 Infor CloudSuite 集成

在本教程中，了解如何将 Infor CloudSuite 与 Azure Active Directory (Azure AD) 集成。
将 Infor CloudSuite 与 Azure AD 集成可提供以下优势：

* 可以在 Azure AD 中控制谁有权限访问 Infor CloudSuite.
* 可让用户使用其 Azure AD 帐户自动登录到 Infor CloudSuite（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Infor CloudSuite 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 启用了 Infor CloudSuite 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Infor CloudSuite 支持启用了 **SP 和 IDP** 的 SSO
* Infor CloudSuite 支持恰时用户预配 

## <a name="adding-infor-cloudsuite-from-the-gallery"></a>从库添加 Infor CloudSuite

若要配置 Infor CloudSuite 与 Azure AD 的集成，需要从库中将 Infor CloudSuite 添加到托管 SaaS 应用列表。

若要从库中添加 Infor CloudSuite，请执行以下步骤： 

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“Infor CloudSuite”，在结果面板中选择“Infor CloudSuite”，然后单击“添加”按钮添加该应用程序    。

    ![结果列表中的 Infor CloudSuite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Infor CloudSuite 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 Infor CloudSuite 相关用户之间建立链接关系。

若要配置和测试 Infor CloudSuite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Infor CloudSuite 单一登录](#configure-infor-cloudsuite-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Infor CloudSuite 测试用户](#create-infor-cloudsuite-test-user)** - 在 Infor CloudSuite 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

要配置 Infor CloudSuite 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Infor CloudSuite”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤： 

    ![Infor CloudSuite 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL： 

    | | |
    |-|-|
    | `http://mingle-sso.inforcloudsuite.com`|
    | `http://mingle-sso.se1.inforcloudsuite.com`|
    | `http://mingle-sso.eu1.inforcloudsuite.com`|
    | `http://mingle-sso.se2.inforcloudsuite.com`|
    | |

    b. 在“回复 URL”文本框中，使用以下模式键入 URL： 

    | | |
    |-|-|
    | `https://mingle-sso.inforcloudsuite.com:443/sp/ACS.saml2`|
    | `https://mingle-sso.se1.inforcloudsuite.com:443/sp/ACS.saml2`|
    | `https://mingle-sso.se2.inforcloudsuite.com:443/sp/ACS.saml2`|
    | `https://mingle-sso.eu1.inforcloudsuite.com:443/sp/ACS.saml2`|
    | |

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![Infor CloudSuite 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，使用以下模式键入 URL： 

    | | |
    |-|-|
    | `https://mingle-portal.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.eu1.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.se1.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.se2.inforcloudsuite.com/Tenant-Name/`|

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 要获取这些值，请联系 [Infor CloudSuite 客户端支持团队](mailto:support@infor.com)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 Infor CloudSuite”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-infor-cloudsuite-single-sign-on"></a>配置 Infor CloudSuite 单一登录

要在“Infor CloudSuite”端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [Infor CloudSuite 支持团队](mailto:support@infor.com)   。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。  例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Infor CloudSuite 的权限，以支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Infor CloudSuite”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Infor CloudSuite”。 

    ![应用程序列表中的 Infor CloudSuite](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-infor-cloudsuite-test-user"></a>创建 Infor CloudSuite 测试用户

在本部分，我们会在 Infor CloudSuite 中创建一个名为 Britta Simon 的用户。 Infor CloudSuite 支持在默认情况下保持启用的实时用户预配。 此部分不存在任何操作项。 如果 Infor CloudSuite 中不存在用户，则会在身份验证后创建一个新用户。 如果需要手动创建用户，请联系  [Infor CloudSuite 支持团队](mailto:support@infor.com)。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Infor CloudSuite 磁贴时，应当会自动登录到为其设置了 SSO 的 Infor CloudSuite。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)