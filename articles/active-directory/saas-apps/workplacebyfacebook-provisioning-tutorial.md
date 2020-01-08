---
title: 教程：使用 Azure Active Directory 为 Workplace by Facebook 配置自动用户预配 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workplace by Facebook 间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f040ff4c8e59f764676aa6fdd9460ec94641684a
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881799"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>教程：为 Workplace by Facebook 配置自动用户预配

本教程旨在介绍为了从 Azure AD 自动将用户帐户预配到 Workplace by Facebook 以及取消其预配而需要在 Workplace by Facebook 和 Azure AD 中执行的步骤。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workplace by Facebook 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Workplace by Facebook 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="assigning-users-to-workplace-by-facebook"></a>将用户分配到 Workplace by Facebook

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中的应用程序的用户和组。

在配置和启用预配服务前，需确定 Azure AD 中哪些用户和/或组表示需要访问 Workplace by Facebook 应用的用户。 确定后，可按照此处的说明将这些用户分配到你的 Workplace by Facebook 应用：

[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>将用户分配到 Workplace by Facebook 的重要提示

*   建议将单个 Azure AD 用户分配到 Workplace by Facebook 以测试预配配置。 其他用户和/或组可以稍后分配。

*   将用户分配到 Workplace by Facebook 时，必须选择有效的用户角色。 “默认访问权限”角色不可用于预配。

## <a name="enable-user-provisioning"></a>启用用户预配

本部分介绍如何将你的 Azure AD 连接到 Workplace by Facebook 的用户帐户预配 API 并配置预配服务，以在 Workplace by Facebook 中根据 Azure AD 中的用户和组分配创建、更新和禁用分配的用户帐户。

>[!Tip]
>还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Workplace by Facebook 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>若要在 Azure AD 中为 Workplace by Facebook 配置用户帐户预配，请执行以下操作：

本部分旨在概述如何对 Workplace by Facebook 启用 Active Directory 用户帐户的预配。

Azure AD 支持将已分配的用户的帐户详细信息自动同步到 Workplace by Facebook。 此自动同步使 Workplace by Facebook 能够在用户首次尝试登录之前获取对用户进行访问授权所需的数据。 在 Azure AD 中撤销访问权限后，它还可以从 Workplace by Facebook 取消预配用户。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory” > “企业应用” > “所有应用程序”部分。

2. 如果已为 Workplace by Facebook 配置单一登录，请使用搜索字段搜索 Workplace by Facebook 实例。 否则，请选择“添加”并在应用程序库中搜索“Workplace by Facebook”。 从搜索结果中选择 Workplace by Facebook，并将其添加到应用程序列表。

3. 选择 Workplace by Facebook 实例，并选择“预配”选项卡。

4. 将“预配模式”设置为“自动”。 

    ![预配](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. 在“管理员凭据”部分下，输入 Workplace by Facebook 管理员的“访问令牌”并将“租户 URL”值设为 `https://www.facebook.com/scim/v1/`。 请参阅这些[说明](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps)为 Workplace 创建访问令牌。 

6. 在 Azure 门户中，单击“测试连接”，确保 Azure AD 可以连接到 Workplace by Facebook 应用。 如果连接失败，请确保 Workplace by Facebook 帐户具有团队管理员权限。

7. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址，并选中复选框。

8. 单击“保存”。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Workplace by Facebook”。

10. 在“属性映射”部分中，查看从 Azure AD 同步到 Workplace by Facebook 的用户属性。 选为“匹配”属性的属性将用于匹配 Workplace by Facebook 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

11. 若要为 Workplace by Facebook 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

12. 单击“保存”。

有关如何配置自动预配的详细信息，请参阅 [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

现可创建测试帐户。 等待最多 20 分钟即可验证该帐户是否已同步到 Workplace by Facebook。

> [!NOTE]
> 我们正在与 Workplace by Facebook 团队紧密合作，以确保 Azure AD 的应用程序获得批准并满足其新指导原则。   

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [配置单一登录](workplacebyfacebook-tutorial.md)
