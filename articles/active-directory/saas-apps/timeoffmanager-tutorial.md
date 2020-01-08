---
title: 教程：Azure Active Directory 与 TimeOffManager 的 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 TimeOffManager 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 69c5d30632e187efe36655a17a91c9e373062955
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943192"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>教程：Azure Active Directory 与 TimeOffManager 的集成

本教程介绍了如何将 TimeOffManager 与 Azure Active Directory (Azure AD) 集成。
将 TimeOffManager 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 TimeOffManager。
* 可让用户使用其 Azure AD 帐户自动登录到 TimeOffManager（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 TimeOffManager 的集成，需要以下各项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 TimeOffManager 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* TimeOffManager 支持 **IDP** 发起的 SSO

* TimeOffManager 支持**实时**用户预配

## <a name="adding-timeoffmanager-from-the-gallery"></a>从库中添加 TimeOffManager

若要配置 TimeOffManager 与 Azure AD 的集成，需要将库中的 TimeOffManager 添加到托管的 SaaS 应用列表。

**若要从库中添加 TimeOffManager，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **TimeOffManager**，在结果面板中选择“TimeOffManager”，然后单击“添加”按钮添加该应用程序。  

     ![结果列表中的“TimeOffManager”](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 TimeOffManager 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 TimeOffManager 相关用户之间建立链接关系。

若要配置和测试 TimeOffManager 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 TimeOffManager 单一登录](#configure-timeoffmanager-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 TimeOffManager 测试用户](#create-timeoffmanager-test-user)** - 在 TimeOffManager 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 TimeOffManager 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“TimeOffManager”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![TimeOffManager 域和 URL 单一登录信息](common/idp-reply.png)

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>` 

    > [!NOTE]
    > 此值不是真实值。 请使用实际回复 URL 更新此值。 可以从教程后面介绍的“单一登录设置页面”中获取此值，也可以联系 [TimeOffManager 支持团队](https://www.purelyhr.com/contact-us)。  还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. TimeOffManager 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标打开“用户属性”对话框。  

    ![image](common/edit-attribute.png)

6. 除上述属性以外，TimeOffManager 应用程序还要求在 SAML 响应中传回其他几个属性。 在“用户属性”  对话框的“用户声明”  部分执行以下步骤，以便添加 SAML 令牌属性，如下表所示： 

    | Name | 源属性|
    | --- | --- |
    | 名 |User.givenname |
    | Lastname |User.surname |
    | 电子邮件 |User.mail |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“ **保存**”。

7. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

8. 在“设置 TimeOffManager”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-timeoffmanager-single-sign-on"></a>配置 TimeOffManager 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 TimeOffManager 公司站点。

2. 转到“帐户”\>“帐户选项”\>“单一登录设置”  。
   
    ![单一登录设置](./media/timeoffmanager-tutorial/ic795917.png "Single Sign-On Settings")

3. 在“单一登录设置”  部分中，执行以下步骤：
   
    ![单一登录设置](./media/timeoffmanager-tutorial/ic795918.png "Single Sign-On Settings")
   
    a. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将整个证书粘贴到“X.509 证书”文本框中。 
   
    b. 在“IdP 颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。
   
    c. 在“IdP 终结点 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。  
   
    d. 对于“强制实施 SAML”  ，选择“否”  。
   
    e. 对于“自动创建用户”  ，选择“是”  。
   
    f. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。
   
    g. 单击“保存更改”。 

4. 在“单一登录设置”页中，复制“断言使用者服务 URL”的值，并将其粘贴到 Azure 门户中“基本 SAML 配置”部分下的“回复 URL”文本框中。     

      ![单一登录设置](./media/timeoffmanager-tutorial/ic795915.png "Single Sign-On Settings")

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

在本部分中，通过授予 Britta Simon 访问 TimeOffManager 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“TimeOffManager”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“TimeOffManager”。 

    ![“应用程序”列表中的“TimeOffManager”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-timeoffmanager-test-user"></a>创建 TimeOffManager 测试用户

在本部分，我们将在 TimeOffManager 中创建名为 Britta Simon 的用户。 TimeOffManager 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 TimeOffManager 中尚不存在用户，身份验证后会创建一个新用户。

>[!NOTE]
>可以使用任何其他 TimeOffManager 用户帐户创建工具或 TimeOffManager 提供的 API 来预配 Azure AD 用户帐户。
> 

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“TimeOffManager”磁贴时，应会自动登录到设置了 SSO 的 TimeOffManager。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

