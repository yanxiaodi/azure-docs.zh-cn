---
title: 教程：使用 Azure Active Directory 为 ThousandEyes 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 ThousandEyes 和取消其预配。
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8325737a62bba71364c02a234636999b0b1b9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964134"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>教程：为 ThousandEyes 配置自动用户预配

本教程的目的是说明从 Azure AD 自动将用户帐户预配到 ThousandEyes 和取消其预配而需要在 ThousandEyes 和 Azure 中执行的步骤。 

## <a name="prerequisites"></a>必备组件

在本教程中概述的方案假定已有以下各项：

* Azure Active Directory 租户
* 已启用[标准计划](https://www.thousandeyes.com/pricing)或更高版本的 ThousandEyes 租户 
* 在 ThousandEyes 中具有管理员权限的用户帐户 

> [!NOTE]
> Azure AD 预配集成依赖于 [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK)，标准计划或更高版本中向 ThousandEyes 团队提供 ThousandEyes SCIM API。

## <a name="assigning-users-to-thousandeyes"></a>将用户分配到 ThousandEyes

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。 

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 ThousandEyes 应用的用户。 确定后，可以按照此处的说明将这些用户分配到 ThousandEyes 应用：

[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>将用户分配到 ThousandEyes 的重要提示

* 建议将单个 Azure AD 用户分配到 ThousandEyes 以测试预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 ThousandEyes，必须在分配对话框中选择用户角色或其他特定于应用程序的有效角色（如果有）  。 “默认访问权限”角色不可用于预配，将跳过这些用户  。

## <a name="configuring-user-provisioning-to-thousandeyes"></a>向 ThousandEyes 配置用户预配 

本部分将指导用户完成以下操作：将 Azure AD 连接到 ThousandEyes 的用户帐户预配 API，配置预配服务以便基于 Azure AD 中的用户和组分配在 ThousandEyes 中创建、更新和禁用所分配的用户帐户。

> [!TIP]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 ThousandEyes 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>在 Azure AD 中为 ThousandEyes 配置自动用户帐户预配

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”  部分。

2. 如果已为 ThousandEyes 配置单一登录，请使用搜索字段搜索 ThousandEyes 实例。 否则，请选择“添加”并在应用程序库中搜索“ThousandEyes”   。 从搜索结果中选择 ThousandEyes，并将其添加到应用程序列表。

3. 选择 ThousandEyes 实例，并选择“预配”选项卡  。

4. 将“预配模式”  设置为“自动”  。

    ![ThousandEyes 预配](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. 在“管理员凭据”部分下，输入 ThousandEyes 帐户生成的“OAuth 持有者令牌”（可在 ThousandEyes 帐户的“配置文件”部分下找到或生成令牌）    。

    ![ThousandEyes 预配](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. 在 Azure 门户中，单击“测试连接”以确保 Azure AD 可以连接到 ThousandEyes 应用  。 如果连接失败，请确保 ThousandEyes 帐户具有管理员权限，然后重试步骤 5。

7. 在“通知电子邮件”字段中输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”  。

8. 单击“ **保存**”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 ThousandEyes”  。

10. 在“属性映射”部分中，查看从 Azure AD 同步到 ThousandEyes 的用户属性  。 选为“匹配”属性的特性用于匹配 ThousandEyes 中的用户帐户以执行更新操作  。 选择“保存”按钮以提交任何更改。

11. 若要为 ThousandEyes 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”   

12. 单击“ **保存**”。

此操作会开始将“用户和组”分区中分配的任何用户和/或组初始同步到 ThousandEyes。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用“同步详细信息”部分监视进度并跟踪指向预配活动日志的链接，这些日志描述了预配服务执行的所有操作  。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)
