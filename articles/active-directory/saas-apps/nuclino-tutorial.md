---
title: 教程：Azure Active Directory 与 Nuclino 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Nuclino 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 74bbab82-5581-4dcf-8806-78f77c746968
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: cce4857dd270f6868b8ae4d0de2117e46c20ce7c
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826301"
---
# <a name="tutorial-azure-active-directory-integration-with-nuclino"></a>教程：Azure Active Directory 与 Nuclino 的集成

本教程介绍如何将 Nuclino 与 Azure Active Directory (Azure AD) 集成。
将 Nuclino 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Nuclino。
* 可以让用户使用其 Azure AD 帐户自动登录到 Nuclino（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Nuclino 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 Nuclino 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Nuclino 支持 **SP** 和 **IDP** 发起的 SSO

* Nuclino 支持**恰时**用户预配

## <a name="adding-nuclino-from-the-gallery"></a>从库中添加 Nuclino

若要配置 Nuclino 与 Azure AD 的集成，需要从库中将 Nuclino 添加到托管 SaaS 应用列表。

**若要从库中添加 Nuclino，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Nuclino”，在结果面板中选择“Nuclino”，然后单击“添加”按钮添加该应用程序。   

     ![结果列表中的 Nuclino](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于一个名为“Britta Simon”的测试用户使用 Nuclino 配置和测试 Azure AD 单一登录。 
若要运行单一登录，需要在 Azure AD 用户与 Nuclino 相关用户之间建立链接关系。

若要配置并测试 Nuclino 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Nuclino 单一登录](#configure-nuclino-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Nuclino 测试用户](#create-nuclino-test-user)** - 在 Nuclino 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Nuclino 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Nuclino”  应用程序集成页上，选择“单一登录”  。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”  部分中执行以下步骤：

    ![Nuclino 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > 这些不是实际值。 使用“身份验证”部分中的实际“标识符”和“回复 URL”更新这些值，本教程稍后将提供相关的介绍。 

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![Nuclino 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > 此值不是真实值。 使用实际登录 URL 更新此值。 请联系 [Nuclino 客户端支持团队](mailto:contact@nuclino.com)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. Nuclino 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标打开“用户属性”对话框。  

    ![image](common/edit-attribute.png)

7. 除了上述属性，Nuclino 应用程序还要求在 SAML 响应中传递回更多的属性。 在“用户属性”  对话框的“用户声明”  部分执行以下步骤，以便添加 SAML 令牌属性，如下表所示：

    | Name |  源属性|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”  列表中，键入为该行显示的属性值。

    f. 单击“确定” 

    g. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

9. 在“设置 Nuclino”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-nuclino-single-sign-on"></a>配置 Nuclino 单一登录

1. 在另一个 Web 浏览器窗口中，以管理员身份登录 Nuclino 公司站点。

2. 单击“ICON”  。

    ![Nuclino 配置](./media/nuclino-tutorial/configure1.png)

3. 单击“Azure AD SSO”，然后从下拉列表中选择“团队设置”。  

    ![Nuclino 配置](./media/nuclino-tutorial/configure2.png)

4. 从左侧导航窗格中选择“身份验证”。 

    ![Nuclino 配置](./media/nuclino-tutorial/configure3.png)

5. 在“身份验证”  部分执行以下步骤：

    ![Nuclino 配置](./media/nuclino-tutorial/configure4.png)

    a. 选择“基于 SAML 的单一登录(SSO)”。 

    b. 复制“ACS URL”值（需将其复制并粘贴到 SSO 提供程序），然后将其粘贴到 Azure 门户的“基本 SAML 配置”部分的“回复 URL”文本框中。   

    c. 复制“实体 ID”值（需将其复制并粘贴到 SSO 提供程序），然后将其粘贴到 Azure 门户的“基本 SAML 配置”部分的“标识符”文本框中。   

    d. 在“SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    e. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    f. 在记事本中打开已下载的 **Certificate(Base64)** 文件。 将其内容复制到剪贴板，然后将其粘贴到“公共证书”  文本框中。

    g. 单击“保存更改”。 

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

在本部分中，通过向 Britta Simon 授予 Nuclino 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Nuclino”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Nuclino”。 

    ![应用程序列表中的 Nuclino 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-nuclino-test-user"></a>创建 Nuclino 测试用户

本部分将在 Nuclino 中创建一个名为 Britta Simon 的用户。 Nuclino 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 Nuclino 中尚不存在用户，身份验证后会创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系 [Nuclino 支持团队](mailto:contact@nuclino.com)。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Nuclino 磁贴时，应当会自动登录到已为其设置了 SSO 的 Nuclino。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

