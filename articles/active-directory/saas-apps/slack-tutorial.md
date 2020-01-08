---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Slack 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Slack 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c2d877a1dc611e02e9fbc245df230ca669a2ae4
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70171445"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Slack 集成

本教程介绍如何将 Slack 与 Azure Active Directory (Azure AD) 集成。 将 Slack 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Slack。
* 让用户使用其 Azure AD 帐户自动登录到 Slack。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Slack 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Slack 支持 SP 发起的 SSO 
* Slack 支持实时用户预配 
* Slack 支持[自动用户预配](https://docs.microsoft.com/en-gb/azure/active-directory/saas-apps/slack-provisioning-tutorial) 

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-slack-from-the-gallery"></a>从库中添加 Slack

要配置 Slack 与 Azure AD 的集成，需要从库中将 Slack 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Slack”   。
1. 从结果面板中选择“Slack”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>配置和测试 Slack 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 Slack 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Slack 相关用户之间建立链接关系。

若要配置和测试 Slack 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Slack SSO](#configure-slack-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Slack 测试用户](#create-slack-test-user)** - 在 Slack 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Slack”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.slack.com` 

    b. 在“标识符(实体 ID)”文本框中，键入 URL：`https://slack.com` 

    > [!NOTE]
    > “登录 URL”值不是实际值。 请使用实际的登录 URL 更新此值。 请联系 [Slack 客户端支持团队](https://slack.com/help/contact)来获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Slack”部分中，根据要求复制相应的 URL  。

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

在本部分中，将通过授予 B.Simon 访问 Slack 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Slack”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-slack-sso"></a>配置 Slack SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Slack 公司站点。

2. 导航到“Microsoft Azure AD”  ，并转到“团队设置”  。

     ![在应用端配置单一登录](./media/slack-tutorial/tutorial_slack_001.png)

3. 在“团队设置”  部分中，单击“身份验证”  选项卡，并单击“更改设置”  。

    ![在应用端配置单一登录](./media/slack-tutorial/tutorial_slack_002.png)

4. 在“SAML 身份验证设置”  对话框中，执行以下步骤：

    ![在应用端配置单一登录](./media/slack-tutorial/tutorial_slack_003.png)

    a.  在“SAML 2.0 终结点(HTTP)”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    b.  在“标识提供者颁发者”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    c.  在记事本中打开下载的证书，将其内容复制到剪贴板，并将其粘贴到“公共证书”  文本框中。

    d. 根据 Slack 团队的需要配置上述三个设置。 有关设置的详细信息，请在此处查看 **Slack 的 SSO 配置指南**。 `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  单击“保存配置”  。

### <a name="create-slack-test-user"></a>创建 Slack 测试用户

本部分要在 Slack 中创建名为“B.Simon”的用户。 Slack 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 尝试访问 Slack 期间，如果尚不存在用户，则会创建一个新用户。 Slack 还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](slack-provisioning-tutorial.md)。

> [!NOTE]
> 如果需要手动创建用户，则需联系 [Slack 支持团队](https://slack.com/help/contact)。

> [!NOTE]
> Azure AD Connect 是同步工具，可以将本地 Active Directory 标识同步到 Azure AD，然后这些同步的用户也可以像其他云用户一样使用应用程序。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Slack 磁贴时，应会自动登录到为其设置了 SSO 的 Slack。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Slack](https://aad.portal.azure.com/)