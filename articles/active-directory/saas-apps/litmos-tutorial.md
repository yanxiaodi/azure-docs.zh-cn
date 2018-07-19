---
title: 教程：Azure Active Directory 与 Litmos 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Litmos 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: jeedes
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 786040054875d5e90b558ca1684d0ce657205cff
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36219251"
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>教程：Azure Active Directory 与 Litmos 的集成

本教程介绍如何将 Litmos 与 Azure Active Directory (Azure AD) 集成。

将 Litmos 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Litmos。
- 可让用户使用其 Azure AD 帐户自动登录到 Litmos（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Litmos 的集成，需要具有以下项：

- Azure AD 订阅
- 已启用 Litmos 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Litmos
2. 配置和测试 Azure AD 单一登录

## <a name="adding-litmos-from-the-gallery"></a>从库中添加 Litmos
要配置 Litmos 与 Azure AD 的集成，需要从库中将 Litmos 添加到托管 SaaS 应用列表。

**若要从库中添加 Litmos，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Litmos”，在结果面板中选择“Litmos”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 Litmos](./media/litmos-tutorial/tutorial_litmos_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Litmos 配置和测试 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道 Litmos 中与 Azure AD 用户对应的用户。 换句话说，需要在 Azure AD 用户与 Litmos 中的相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Litmos 中“用户名”的值来建立此链接关系。

若要配置并测试 Litmos 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Litmos 测试用户](#create-a-litmos-test-user) - 在 Litmos 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 身份。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分介绍如何在 Azure 门户中启用 Azure AD 单一登录并在 Litmos 应用程序中配置单一登录。

**若要配置 Litmos 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Litmos 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/litmos-tutorial/tutorial_litmos_samlbase.png)

3. 在“Litmos 域和 URL”部分中，执行以下步骤：

    ![Litmos 域和 URL 单一登录信息](./media/litmos-tutorial/tutorial_litmos_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<companyname>.litmos.com/account/Login`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE] 
    > 这些不是实际值。 使用实际标识符和回复 URL 更新这些值（本教程后面部分将进行介绍）或联系 [Litmos 支持团队](https://www.litmos.com/contact-us/)获取这些值。

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/litmos-tutorial/tutorial_litmos_certificate.png)

5. 在配置过程中，需要为 Litmos 应用程序自定义 **SAML 令牌属性**。

    ![“属性”部分](./media/litmos-tutorial/tutorial_attribute.png)
           
    | 属性名称   | 属性值 |   
    | ---------------  | ----------------|
    | FirstName |user.givenname |
    | LastName  |user.surname |
    | Email |user.mail |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![添加属性](./media/litmos-tutorial/tutorial_attribute_04.png)

    ![“添加属性”对话框](./media/litmos-tutorial/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。
    
    d. 单击“确定” 。     

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/litmos-tutorial/tutorial_general_400.png)

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Litmos 公司站点。

8. 在左侧导航栏中，单击“帐户”。
   
    ![应用端上的“帐户”部分][22] 

9. 单击“集成”选项卡。
   
    ![“集成”选项卡][23] 

10. 在“集成”选项卡上，向下滚动到“第三方集成”，并单击“SAML 2.0”选项卡。
   
    ![SAML 2.0 部分][24] 

11. 复制“Litmos 的 SAML 终结点:”下的值并将其粘贴到 Azure 门户中“Litmos 域和 URL”部分中的“回复 URL”文本框。 
   
    ![SAML 终结点][26] 

12. 在 **Litmos** 应用程序中，执行以下步骤：
    
     ![Litmos 应用程序][25] 
     
     a. 单击“启用 SAML”。
    
     b. 在记事本中打开 base-64 编码证书，将其内容复制到剪贴板，再粘贴到“SAML X.509 证书”文本框中。
     
     c. 单击“保存更改”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/litmos-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/litmos-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/litmos-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/litmos-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
  
### <a name="create-a-litmos-test-user"></a>创建 Litmos 测试用户

本部分的目的是在 Litmos 中创建名为 Britta Simon 的用户。  
Litmos 应用程序支持实时预配。 这意味着，在尝试使用访问面板访问应用程序期间，如有必要，会自动创建一个用户帐户。

**若要在 Litmos 中创建名为 Britta Simon 的用户，请执行以下步骤：**

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Litmos 公司站点。

2. 在左侧导航栏中，单击“帐户”。
   
    ![应用端上的“帐户”部分][22] 

3. 单击“集成”选项卡。
   
    ![“集成”选项卡][23] 

4. 在“集成”选项卡上，向下滚动到“第三方集成”，并单击“SAML 2.0”选项卡。
   
    ![SAML 2.0][24] 
    
5. 选择“自动生成用户”
   
    ![自动生成用户][27] 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Litmos 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 Litmos，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Litmos”。

    ![应用程序列表中的 Litmos 链接](./media/litmos-tutorial/tutorial_litmos_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。  

当在访问面板中单击 Litmos 磁贴时，应当会自动登录到 Litmos 应用程序。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/litmos-tutorial/tutorial_general_01.png
[2]: ./media/litmos-tutorial/tutorial_general_02.png
[3]: ./media/litmos-tutorial/tutorial_general_03.png
[4]: ./media/litmos-tutorial/tutorial_general_04.png
[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png

[100]: ./media/litmos-tutorial/tutorial_general_100.png

[200]: ./media/litmos-tutorial/tutorial_general_200.png
[201]: ./media/litmos-tutorial/tutorial_general_201.png
[202]: ./media/litmos-tutorial/tutorial_general_202.png
[203]: ./media/litmos-tutorial/tutorial_general_203.png
