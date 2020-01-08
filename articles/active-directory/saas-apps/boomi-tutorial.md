---
title: 教程：Azure Active Directory 与 Boomi 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Boomi 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 114d29d11e51209d5ac788ae25413b5b989b84f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106103"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>教程：Azure Active Directory 与 Boomi 集成

在本教程中，了解如何将 Boomi 与 Azure Active Directory (Azure AD) 集成。
将 Boomi 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Boomi。
* 可以让用户使用其 Azure AD 帐户自动登录到 Boomi（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Boomi 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 Boomi 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Boomi 支持 **IDP** 发起的 SSO

## <a name="adding-boomi-from-the-gallery"></a>从库中添加 Boomi

要配置 Boomi 与 Azure AD 的集成，需要从库中将 Boomi 添加到托管 SaaS 应用列表。

**若要从库中添加 Boomi，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Boomi”，在结果面板中选择“Boomi”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 Boomi](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”  的测试用户配置和测试 Boomi 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 Boomi 相关用户之间建立链接关系。

若要配置和测试 Boomi 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Boomi 单一登录](#configure-boomi-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Boomi 测试用户](#create-boomi-test-user)** - 在 Boomi 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Boomi 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Boomi 应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”  页上，单击“编辑”  按钮，以打开“基本 SAML 配置”  对话框。

    ![Boomi 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中键入 URL：`https://platform.boomi.com/` 

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > 答复 URL 值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [Boomi 客户端支持团队](https://boomi.com/company/contact/)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. Boomi 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![图像](common/edit-attribute.png)

6. 在“用户属性”  对话框的“用户声明”  部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | 名称 |  源属性|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

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

8. 在“设置 Boomi”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-boomi-single-sign-on"></a>配置 Boomi 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Boomi 公司站点。 

2. 导航到“公司名称”  ，并转到“设置”  。

3. 单击“SSO 选项”  选项卡，并执行以下步骤。

    ![在应用端配置单一登录](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. 选中“启用 SAML 单一登录”  复选框。

    b. 单击“导入”  ，将已下载的证书从 Azure AD 上传到“标识提供者证书”  。

    c. 在“标识提供者登录 URL”  文本框中，放置 Azure AD 应用程序配置窗口中**登录 URL** 的值。

    d. 对于“联合 ID 位置”，选择“联合 ID 位于 FEDERATION_ID 属性元素中”单选按钮。  

    e. 单击“保存”按钮  。

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

在本部分中，通过授予 Britta Simon 访问 Boomi 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Boomi”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Boomi”  。

    ![应用程序列表中的 Boomi 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-boomi-test-user"></a>创建 Boomi 测试用户

为使 Azure AD 用户能够登录到 Boomi，必须将其预配到 Boomi 中。 就 Boomi 来说，预配任务需要手动完成。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：

1. 以管理员身份登录 Boomi 公司站点。

2. 登录后，导航到“用户管理”  并转到“用户”  。

    ![用户](./media/boomi-tutorial/tutorial_boomi_001.png "用户")

3. 单击 **+** 图标，此时会打开“添加/维护用户角色”  对话框。

    ![用户](./media/boomi-tutorial/tutorial_boomi_002.png "用户")

    ![用户](./media/boomi-tutorial/tutorial_boomi_003.png "用户")

    a. 在“用户电子邮件地址”文本框中  ，键入用户的电子邮件地址（例如 BrittaSimon@contoso.com）。

    b. 在“名字”文本框中，键入用户的名字（如“Britta”）  。

    c. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）  。

    d. 输入用户的**联合 ID**。 每个用户都必须具有在帐户中唯一标识用户的联合 ID。

    e. 向用户分配“标准用户”  角色。 不要分配管理员角色，因为这会向用户提供标准 Atmosphere 访问权限以及单一登录访问权限。

    f. 单击“确定”。 

    > [!NOTE]
    > 用户不会收到包含可用于登录 AtomSphere 帐户的密码的欢迎通知电子邮件，因为其密码是通过标识提供者进行管理的。 可以使用任何其他 Boomi 用户帐户创建工具或 Boomi 提供的 API 来预配 AAD 用户帐户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Boomi 磁贴时，应当会自动登录到已为其设置了 SSO 的 Boomi。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

