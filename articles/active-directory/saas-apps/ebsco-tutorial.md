---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 EBSCO 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 EBSCO 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ebaf3a1d877025cafe8829bc937ef032a3c95d03
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163461"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>教程：Azure Active Directory 单一登录 (SSO) 与 EBSCO 集成

本教程介绍如何将 EBSCO 与 Azure Active Directory (Azure AD) 集成。 将 EBSCO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 EBSCO。
* 让用户使用其 Azure AD 帐户自动登录到 EBSCO。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 EBSCO 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* EBSCO 支持 **SP 和 IDP** 发起的 SSO
* EBSCO 支持**实时**用户预配

> [!NOTE]
> 由于此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-ebsco-from-the-gallery"></a>从库中添加 EBSCO

若要配置 EBSCO 与 Azure AD 的集成，需要从库中将 EBSCO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“EBSCO”   。
1. 从结果面板中选择“EBSCO”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>配置和测试 EBSCO 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 EBSCO 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 EBSCO 相关用户之间建立链接关系。

若要配置和测试 EBSCO 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 EBSCO SSO](#configure-ebsco-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 EBSCO 测试用户](#create-ebsco-test-user)** - 在 EBSCO 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“EBSCO”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    在“标识符”  文本框中，键入一个 URL：`pingsso.ebscohost.com`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > 登录 URL 值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [EBSCO 客户端支持团队](mailto:support@ebsco.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

    o   **唯一元素：**  

    o   **Custid** = 输入唯一的 EBSCO 客户 ID 

    o   **Profile** = 客户端可以定制链接，以将用户定向到特定的配置文件（具体取决于从 EBSCO 购买的产品）。 可以输入特定的配置文件 ID。 主 ID 是 eds (EBSCO Discovery Service) 和 ehost（EBSOCOhost 数据库）。 [此处](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)提供了相同操作的说明。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 EBSCO”部分中，根据要求复制相应的 URL  。

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

在本部分中，将通过授予 B.Simon 访问 EBSCO 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“EBSCO”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-ebsco-sso"></a>配置 EBSCO SSO

若要在 **EBSCO** 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [EBSCO 支持团队](mailto:support@ebsco.com)。  他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-ebsco-test-user"></a>创建 EBSCO 测试用户

在 EBSCO 中，用户预配可自动完成。

**若要预配用户帐户，请执行以下步骤：**

Azure AD 将所需的数据传递给 EBSCO 应用程序。 EBSCO 的用户预配可以自动完成，或要求填写表单一次。 具体的过程取决于客户端是否包含大量保存了个人设置的现有 EBSCOhost 帐户。 在实施过程中，可与 [EBSCO 支持团队](mailto:support@ebsco.com)讨论上述过程。 不管采用哪种方法，客户端都不需要在测试之前创建任何 EBSCOhost 帐户。

   >[!Note]
   >可以自动执行 EBSCOhost 用户预配/个性化。 有关实时用户预配过程，请联系 [EBSCO 支持团队](mailto:support@ebsco.com)。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

1. 在访问面板中单击“EBSCO”磁贴时，应会自动登录到 EBSCO 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

1. 登录到该应用程序后，单击右上角的“登录”按钮。 

    ![“应用程序”列表中的 EBSCO 登录按钮](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. 此时会出现一次性提示“现在请将现有的 MyEBSCOhost 帐户链接到机构帐户”或“创建新的 MyEBSCOhost 帐户并将其链接到机构帐户”，以便配对机构/SAML 登录名。   该帐户用于在 EBSCOhost 应用程序中进行个性化。 选择选项“创建新帐户”，将会看到，用于个性化的窗体中预先填充了 SAML 响应中的值，如以下屏幕截图所示。  单击“继续”保存所做的选择。 
    
     ![“应用程序”列表中的 EBSCO 用户](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. 完成上述设置后，清除 Cookie/缓存并再次登录。 这一次不再需要手动登录，因为系统已记住个性化设置。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 EBSCO](https://aad.portal.azure.com/)

