---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 SKYSITE 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 SKYSITE 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 04c6a47a-1730-4acf-bc5c-a04daccff9b3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: adbf57e0446820959113ba4f27cc2f93fd20fdd5
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70174293"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-skysite"></a>教程：Azure Active Directory 单一登录 (SSO) 与 SKYSITE 的集成

本教程介绍如何将 SKYSITE 与 Azure Active Directory (Azure AD) 集成。 将 SKYSITE 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SKYSITE。
* 让用户使用其 Azure AD 帐户自动登录到 SKYSITE。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 SKYSITE 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SKYSITE 支持 **IDP** 发起的 SSO

* SKYSITE 支持**实时**用户预配

## <a name="adding-skysite-from-the-gallery"></a>从库中添加 SKYSITE

若要配置 SKYSITE 与 Azure AD 的集成，需要从库中将 SKYSITE 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **SKYSITE**。 
1. 在结果面板中选择“SKYSITE”，然后添加该应用。  在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-skysite"></a>配置并测试 SKYSITE 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 SKYSITE 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 SKYSITE 中的相关用户之间建立链接关系。

若要配置并测试 SKYSITE 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 SKYSITE SSO](#configure-skysite-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 SKYSITE 测试用户](#create-skysite-test-user)** - 在 SKYSITE 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“SKYSITE”应用程序集成页上，单击“属性”选项卡并执行以下步骤：   

    ![单一登录属性](./media/skysite-tutorial/config05.png)

    * 复制“用户访问 URL”并将其粘贴到“配置 SKYSITE SSO”部分，本教程稍后将对此进行说明。  

1. 在“SKYSITE”应用程序集成页上，导航到“单一登录”。  
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，应用程序已预配置为采用“IDP”发起模式，并且已在 Azure 中预先填充了所需的 URL。 ****   ****   用户需要单击“保存”按钮来保存配置。 ****  

1. SKYSITE 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标以打开“用户属性”对话框 ****  。

    ![image](common/edit-attribute.png)

1. 除上述属性以外，SKYSITE 应用程序还要求在 SAML 响应中传回其他几个属性。 在“组声明(预览)”对话框中的“用户属性和声明”部分，执行以下步骤 ****   ****  ：

    a. 单击“声明中返回的组”旁边的**笔**。 

    ![image](./media/skysite-tutorial/config01.png)

    ![图像](./media/skysite-tutorial/config02.png)

    b. 从单选列表中选择“所有组”。 

    c. 选择“组 ID”作为“源属性”  。 

    d. 单击“ **保存**”。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 SKYSITE”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 SKYSITE 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“SKYSITE”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="configure-skysite-sso"></a>配置 SKYSITE SSO

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 SKYSITE 公司站点并执行以下步骤：

4. 单击页面右上方的“设置”，然后导航到“帐户设置”。  

    ![配置](./media/skysite-tutorial/config03.png)

5. 切换到“单一登录(SSO)”选项卡并执行以下步骤： 

    ![配置](./media/skysite-tutorial/config04.png)

    a. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户中的“属性”选项卡复制的“用户访问 URL”值。   

    b. 单击“上传证书”，以上传从 Azure 门户中下载的 Base64 编码证书。 

    c. 单击“ **保存**”。

### <a name="create-skysite-test-user"></a>创建 SKYSITE 测试用户

在本部分，我们将在 SKYSITE 中创建一个名为 Britta Simon 的用户。 SKYSITE 支持默认已启用的实时用户预配。 此部分不存在任何操作项。 如果 SKYSITE 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“SKYSITE”磁贴时，应会自动登录到设置了 SSO 的 SKYSITE。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 SKYSITE](https://aad.portal.azure.com/)

