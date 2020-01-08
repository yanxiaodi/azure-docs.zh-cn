---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Onit 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Onit 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e908cb76a57f027494230edc648b69da0730ac27
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164244"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Onit 集成

本教程介绍如何将 Onit 与 Azure Active Directory (Azure AD) 集成。 将 Onit 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Onit。
* 让用户使用其 Azure AD 帐户自动登录到 Onit。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Onit 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Onit 支持 SP 发起的 SSO 

## <a name="adding-onit-from-the-gallery"></a>从库中添加 Onit

若要配置 Onit 与 Azure AD 的集成，需要从库中将 Onit 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Onit”   。
1. 从结果面板中选择“Onit”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-onit"></a>配置和测试 Onit 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 Onit 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Onit 相关用户之间建立链接关系。

若要配置和测试 Onit 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Onit SSO](#configure-onit-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Onit 测试用户](#create-onit-test-user)** - 在 Onit 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Onit”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<sub-domain>.onit.com` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<sub-domain>.onit.com` 

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Onit 客户端支持团队](https://www.onit.com/support)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

1. 在“SAML 签名证书”部分中，复制**指纹值**并将其保存在计算机上。 

    ![复制指纹值](common/copy-thumbprint.png)

1. 在“设置 Onit”部分中，根据要求复制相应的 URL  。

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

在本部分中，将通过授予 B.Simon 访问 Onit 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Onit”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-onit-sso"></a>配置 Onit SSO

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Onit 公司站点。

2. 在顶部菜单中，单击“管理”。 
   
    ![管理](./media/onit-tutorial/IC791174.png "Administration")

3. 单击“编辑公司”。 
   
    ![编辑公司](./media/onit-tutorial/IC791175.png "编辑公司")
   
4. 单击“安全”选项卡。 
    
    ![编辑公司信息](./media/onit-tutorial/IC791176.png "编辑公司信息")

5. 在“安全”选项卡中，执行以下步骤： 

    ![单一登录](./media/onit-tutorial/IC791177.png "单一登录")

    a. 选择“单一登录和密码”作为“身份验证策略”。  
    
    b. 在“Idp 目标 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 在“Idp 注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值   。

    d. 在“Idp 证书指纹(SHA1)”文本框中，粘贴从 Azure 门户复制的证书“指纹”值。  

### <a name="create-onit-test-user"></a>创建 Onit 测试用户

要让 Azure AD 用户登录 Onit，必须将其预配到 Onit 中。 使用 Onit 时，预配属手动任务。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到 **Onit** 公司站点。

2. 单击“添加用户”  。

    ![管理](./media/onit-tutorial/IC791180.png "Administration")

3. 在“添加用户”对话框页上，执行以下步骤： 

    ![添加用户](./media/onit-tutorial/IC791181.png "添加用户")

    a. 在相关文本框中键入要预配的有效 Azure AD 帐户的“名称”和“电子邮件地址”。  

    b. 单击“创建”。 

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Onit 磁贴时，应当会自动登录到已为其设置了 SSO 的 Onit。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Onit](https://aad.portal.azure.com/)