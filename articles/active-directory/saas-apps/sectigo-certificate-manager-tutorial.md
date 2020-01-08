---
title: 教程：Azure Active Directory 与 Sectigo Certificate Manager 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Sectigo Certificate Manager 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588240"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>教程：Azure Active Directory 与 Sectigo Certificate Manager 的集成

本教程介绍如何将 Sectigo Certificate Manager 与 Azure Active Directory (Azure AD) 集成。

将 Sectigo Certificate Manager 与 Azure AD 集成可提供以下优势：

* 可以使用 Azure AD 控制有权访问 Sectigo Certificate Manager 的人员。
* 用户可以使用其 Azure AD 帐户自动登录到 Sectigo Certificate Manager（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

有关服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Sectigo Certificate Manager 的集成，需准备好以下各项：

* 一个 Azure AD 订阅。 如果还没有 Azure AD 订阅，可在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录的 Sectigo Certificate Manager 订阅。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中配置和测试 Azure AD 单一登录，并将 Sectigo Certificate Manager 与 Azure AD 集成。

Sectigo Certificate Manager 支持以下功能：

* **SP 发起的单一登录**
* **IDP 发起的单一登录**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>在 Azure 门户中添加 Sectigo Certificate Manager

若要将 Sectigo Certificate Manager 与 Azure AD 集成，必须将 Sectigo Certificate Manager 添加到托管 SaaS 应用列表。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左侧菜单中，选择“Azure Active Directory”  。

    ![“Azure Active Directory”选项](common/select-azuread.png)

1. 选择“企业应用程序” > “所有应用程序”   。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 若要添加应用程序，请选择“新建应用程序”  。

    ![“新建应用程序”选项](common/add-new-app.png)

1. 在搜索框中，输入“Sectigo Certificate Manager”  。 在搜索结果中，选择“Sectigo Certificate Manager”  ，然后选择“添加”  。

    ![结果列表中的 Sectigo Certificate Manager](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于一个名为 Britta Simon 的测试用户配置和测试 Sectigo Certificate Manager 的 Azure AD 单一登录  。 若要运行单一登录，必须在 Azure AD 用户与 Sectigo Certificate Manager 中的相关用户之间建立链接关系。

若要配置和测试 Sectigo Certificate Manager 的 Azure AD 单一登录，必须完成以下构建基块：

| 任务 | 说明 |
| --- | --- |
| **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** | 使用户能够使用此功能。 |
| **[配置 Sectigo Certificate Manager 单一登录](#configure-sectigo-certificate-manager-single-sign-on)** | 在应用程序中配置单一登录设置。 |
| **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** | 测试名为 Britta Simon 的用户的 Azure AD 单一登录。 |
| **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** | 使 Britta Simon 能够使用 Azure AD 单一登录。 |
| **[创建 Sectigo Certificate Manager 测试用户](#create-a-sectigo-certificate-manager-test-user)** | 在 Sectigo Certificate Manager 中创建一个与 Azure AD 中的 Britta Simon 相对应的关联用户。 |
| **[测试单一登录](#test-single-sign-on)** | 验证配置是否正常工作。 |

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中使用 Sectigo Certificate Manager 配置 Azure AD 单一登录。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Sectigo Certificate Manager”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录选项](common/select-sso.png)

1. 在“选择单一登录方法”窗格中，选择“SAML”或“SAML/WS-Fed”模式以启用单一登录    。

    ![单一登录选择模式](common/select-saml-option.png)

1. 在“设置 SAML 单一登录”窗格中，选择“编辑”（铅笔图标）以打开“基本 SAML 配置”窗格    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”窗格中，若要配置“IDP 发起模式”，请完成以下步骤：  

    1. 在“标识符”  框中，输入以下 URL 之一：
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. 在“回复 URL”  框中，输入以下 URL 之一：
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. 选择“设置其他 URL”  。

    1. 在“中继状态”  框中，输入以下 URL 之一：
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Sectigo Certificate Manager 域和 URL 单一登录信息](common/idp-relay.png)

1.  若要在 SP 发起的模式下配置应用程序，请完成以下步骤  ：

    * 在“登录 URL”  框中，输入以下 URL 之一：
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Sectigo Certificate Manager 域和 URL 单一登录信息](common/both-signonurl.png)

1. 在“设置 SAML 单一登录”窗格的“SAML 签名证书”部分中，选择“证书 (Base64)”旁边的“下载”     。 根据需要选择下载选项。 将证书保存在计算机上。

    ![证书 (Base64) 下载选项](common/certificatebase64.png)

1. 在“设置 Sectigo Certificate Manager”部分，根据要求复制以下 URL  ：

    * 登录 URL
    * Azure AD 标识符
    * 注销 URL

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>配置 Sectigo Certificate Manager 单一登录

若要在 Sectigo Certificate Manager 端配置单一登录，请将下载的“证书(Base64)”文件以及从 Azure 门户复制的相关 URL 发送到 [Sectigo Certificate Manager 支持团队](https://sectigo.com/support)。 Sectigo Certificate Manager 支持团队使用你发送的信息来确保双方都正确设置 SAML 单一登录连接。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

在本部分中，会在 Azure 门户中创建名为“Britta Simon”的测试用户。

1. 在 Azure 门户中，选择“Azure Active Directory” > “用户” > “所有用户”。   

    ![“用户”和“所有用户”选项](common/users.png)

1. 选择“新建用户”。 

    ![“新建用户”选项](common/new-user.png)

1. 在“用户”窗格中完成以下步骤： 

    1. 在“姓名”  框中，输入 **BrittaSimon**。
  
    1. 在“用户名”框中，输入“brittasimon\@\<your-company-domain>.\<extension\>”   。 例如，brittasimon\@contoso.com  。

    1. 选中“显示密码”复选框  。 记下“密码”框中显示的值  。

    1. 选择“创建”  。

    ![“用户”窗格](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Sectigo Certificate Manager 的权限，使其可使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序” > “Sectigo Certificate Manager”    。

    ![“企业应用程序”窗格](common/enterprise-applications.png)

1. 在应用程序列表中，选择“Sectigo Certificate Manager”  。

    ![应用程序列表中的 Sectigo Certificate Manager](common/all-applications.png)

1. 在菜单中选择“用户和组”  。

    ![“用户和组”选项](common/users-groups-blade.png)

1. 选择“添加用户”。  然后，在“添加分配”窗格中选择“用户和组”。  

    ![“添加分配”窗格](common/add-assign-user.png)

1. 在“用户和组”窗格中，在用户列表中选择“Britta Simon”   。 选择“选择”。 

1. 如果希望在 SAML 断言中使用角色值，请在“选择角色”窗格中，从列表中为用户选择相关的角色  。 选择“选择”。 

1. 在“添加分配”窗格中选择“分配”   。

### <a name="create-a-sectigo-certificate-manager-test-user"></a>创建 Sectigo Certificate Manager 测试用户

在本部分中，在 Sectigo Certificate Manager 中创建名为 Britta Simon 的用户。 与 [Sectigo Certificate Manager 支持团队](https://sectigo.com/support)合作，在 Sectigo Certificate Manager 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

本部分将使用“我的应用”门户测试 Azure AD 单一登录配置。

设置单一登录后，当在“我的应用”门户中选择“Sectigo Certificate Manager”时，将自动登录到 Sectigo Certificate Manager  。 有关“我的应用”门户的详细信息，请参阅[访问和使用“我的应用”门户上的应用](../user-help/my-apps-portal-end-user-access.md)。

## <a name="next-steps"></a>后续步骤

若要了解更多信息，请查看下列文章：

- [用于将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


