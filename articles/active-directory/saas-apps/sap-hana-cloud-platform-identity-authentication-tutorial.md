---
title: 教程：Azure Active Directory 与 SAP Cloud Platform Identity Authentication 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP Cloud Platform Identity Authentication 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eff07f157f6011b9b561334c00b3a4d94989ff5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091839"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>教程：Azure Active Directory 与 SAP Cloud Platform Identity Authentication 的集成

本教程介绍如何将 SAP Cloud Platform Identity Authentication 与 Azure Active Directory (Azure AD) 集成。
将 SAP Cloud Platform Identity Authentication 与 Azure AD 集成具有以下优势：

* 可以在 Azure AD 中控制谁有权访问 SAP Cloud Platform Identity Authentication。
* 可让用户使用其 Azure AD 帐户自动登录到 SAP Cloud Platform Identity Authentication（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAP Cloud Platform Identity Authentication 的集成，需备齐以下各项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 SAP Cloud Platform Identity Authentication 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SAP Cloud Platform Identity Authentication 支持 **SP** 和 **IDP** 发起的 SSO

深入了解技术细节前，先理解后面将会遇到的概念至关重要。 通过 SAP Cloud Platform Identity Authentication 和 Active Directory 联合身份验证服务，可以使用受 SAP Cloud Platform Identity Authentication 保护的 SAP 应用和服务，跨受 Azure AD（可用作 IdP）保护的应用或服务实现 SSO。

目前，SAP Cloud Platform Identity Authentication 可用作 SAP 应用的代理标识提供者， 而 Azure Active Directory 则在此设置中充当主要标识提供者。 

下图展示了此关系：

![创建 Azure AD 测试用户](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

在此设置中，SAP Cloud Platform Identity Authentication 租户被配置为 Azure Active Directory 中受信任的应用。

随后会在 SAP Cloud Platform Identity Authentication 管理控制台中，配置要通过这种方式进行保护的所有 SAP 应用和服务。

因此，需要在 SAP Cloud Platform Identity Authentication（而不是 Azure Active Directory）中进行授权，授予对 SAP 应用和服务的访问权限。

通过使用 Azure Active Directory 市场将 SAP Cloud Platform Identity Authentication 配置为应用，便无需配置各个声明或 SAML 断言。

> [!NOTE]
> 目前，只有 Web SSO 已通过双方测试。 应用到 API 通信或 API 间通信所需的流应该可以正常运行，但尚未经过测试。 将在后续活动中对这些流进行测试。

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>从库添加 SAP Cloud Platform Identity Authentication

要配置 SAP Cloud Platform Identity Authentication 到 Azure AD 中的集成，需将 SAP Cloud Platform Identity Authentication 从库添加到托管型 SaaS 应用的列表。

**若要从库添加 SAP Cloud Platform Identity Authentication，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **SAP Cloud Platform Identity Authentication**，从结果面板中选择“SAP Cloud Platform Identity Authentication”，单击“添加”按钮添加该应用程序。  

     ![结果列表中的 SAP Cloud Platform Identity Authentication](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为 **Britta Simon** 的测试用户的指示配置和测试 [应用程序名称] 的 Azure AD 单一登录。
若要运行单一登录，需要在 Azure AD 用户与 [应用程序名称] 相关用户之间建立链接关系。

若要配置和测试 [应用程序名称] 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 SAP Cloud Platform Identity Authentication 单一登录](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 SAP Cloud Platform Identity Authentication 测试用户](#create-sap-cloud-platform-identity-authentication-test-user)** - 在 SAP Cloud Platform Identity Authentication 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 [应用程序名称] 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“SAP Cloud Platform Identity Authentication”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 IDP 发起的模式下进行配置，请在“基本 SAML 配置”部分中执行以下步骤   ：

    ![SAP Cloud Platform Identity Authentication 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`<IAS-tenant-id>.accounts.ondemand.com`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [SAP Cloud Platform Identity Authentication 客户支持团队](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)获取这些值。 如果不了解标识符值，请阅读有关[租户 SAML 2.0 配置](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)的 SAP Cloud Platform Identity Authentication 文档。

5. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    ![SAP Cloud Platform Identity Authentication 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > 此值不是真实值。 请将此值更新为实际登录 URL。 请使用特定业务应用程序登录 URL。 如果有任何疑问，请联系 [SAP Cloud Platform Identity Authentication 客户支持团队](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)。

6. SAP Cloud Platform Identity Authentication 应用程序要求 SAML 断言采用特定格式。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![image](common/edit-attribute.png)

7. 如果 SAP 应用程序需要 **firstName** 等属性，请在“用户属性”对话框的“用户声明”部分添加 **firstName** 属性，按上图所示配置 SAML 令牌属性，然后执行以下步骤：  

    a. 单击“添加新声明”  以打开“管理用户声明”  对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入属性名称 firstName。 

    c. 将“命名空间”留空  。

    d. 选择“源”作为“属性”  。

    e. 在“源属性”列表中，选择属性值 user.givenname。 

    f. 单击“确定” 

    g. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分，单击“下载”  以根据要求下载从给定选项提供的元数据 XML 并将其保存在计算机上。 

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 SAP Cloud Platform Identity Authentication”部分，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>配置 SAP Cloud Platform Identity Authentication 单一登录

1. 若要为应用配置 SSO，请转到“SAP Cloud Platform Identity Authentication 管理控制台”。 URL 采用以下模式：`https://<tenant-id>.accounts.ondemand.com/admin`。 然后，阅读与 SAP Cloud Platform Identity Authentication 相关的文档 - [与 Microsoft Azure AD 集成](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)。

2. 在 Azure 门户中，选择“保存”  按钮。

3. 仅当要为另一个 SAP 应用添加和启用 SSO 时，才继续执行以下步骤。 重复执行“从库添加 SAP Cloud Platform Identity Authentication”  部分下的步骤。

4. 在 Azure 门户的“SAP Cloud Platform Identity Authentication”  应用集成页上，选择“链接登录”  。

    ![配置链接登录](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. 保存配置。

> [!NOTE]
> 新应用利用旧 SAP 应用的单一登录配置。 确保在 SAP Cloud Platform Identity Authentication 管理控制台中使用相同的企业标识提供者。

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

在本部分中，通过授予 Britta Simon 访问 SAP Cloud Platform Identity Authentication 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“SAP Cloud Platform Identity Authentication”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“SAP Cloud Platform Identity Authentication”  。

    ![应用程序列表中的“SAP Cloud Platform Identity Authentication”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>创建 SAP Cloud Platform Identity Authentication 测试用户

无需在 SAP Cloud Platform Identity Authentication 上创建用户。 Azure AD 用户存储中的用户可以使用 SSO 功能。

SAP Cloud Platform Identity Authentication 支持“联合身份验证”选项。 使用此选项，应用可以检查 SAP Cloud Platform Identity Authentication 的用户存储中是否有经过公司标识提供者验证的用户。

“联合身份验证”选项默认处于禁用状态。 如果启用了“联合身份验证”，只有已导入 SAP Cloud Platform Identity Authentication 的用户才能访问应用。

若要详细了解如何启用或禁用 SAP Cloud Platform Identity Authentication 的联合身份验证，请参阅[配置 SAP Cloud Platform Identity Authentication 用户存储的联合身份验证](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)中的“启用 SAP Cloud Platform Identity Authentication 的联合身份验证”。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“SAP Cloud Platform Identity Authentication”磁贴时，应会自动登录到设置了 SSO 的 SAP Cloud Platform Identity Authentication。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
