---
title: 教程：使用 Azure Active Directory 为 BlueJeans 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 BlueJeans 和取消其预配。
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84870ef03e07cdabe994da7c04a103bead48d34f
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672995"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>教程：为 BlueJeans 配置自动用户预配

本教程的目的是演示要将 Azure AD 配置为自动将用户和/或组预配到 BlueJeans 以及取消其预配需在 BlueJeans 和 Azure Active Directory (Azure AD) 中执行的步骤。

> [!NOTE]
> 本教程介绍在 Azure AD 用户预配服务之上构建的连接器。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../manage-apps/user-provisioning.md)。

## <a name="prerequisites"></a>先决条件

本教程中所述的方案假定你已具备以下项：

* Azure AD 租户
* 启用了[我的公司](https://www.BlueJeans.com/pricing)计划或更佳计划的 BlueJeans 租户
* BlueJeans 中具有管理员权限的用户帐户

> [!NOTE]
> Azure AD 预配集成依赖于可供 BlueJeans 团队在标准计划或更佳计划中使用的 [BlueJeans API](https://BlueJeans.github.io/developer)。

## <a name="adding-bluejeans-from-the-gallery"></a>从库中添加 BlueJeans

在使用 Azure AD 为 BlueJeans 配置自动用户预配之前，需要从 Azure AD 应用程序库将 BlueJeans 添加到托管的 SaaS 应用程序列表。

若要从 Azure AD 应用程序库中添加 BlueJeans，请执行以下步骤： 

1. 在中 **[Azure 门户](https://portal.azure.com)** ，在左侧的导航窗格中，选择**Azure Active Directory**。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用程序”，并选择“所有应用程序”。  

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新的应用程序，请选择**新的应用程序**窗格顶部的按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，输入**BlueJeans**，选择**BlueJeans**在结果面板中，，然后选择**添加**按钮添加该应用程序。

    ![结果列表中的 BlueJeans](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>将用户分配到 BlueJeans

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和/或组。

在配置和启用自动用户预配之前，应确定 Azure AD 中的哪些用户和/或组需要访问 BlueJeans。 确定后，可以按照此处的说明将这些用户和/或组分配到 BlueJeans：

* [向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>将用户分配到 BlueJeans 的重要提示

* 建议将单个 Azure AD 用户分配到 BlueJeans 以测试自动用户预配配置。 其他用户和/或组可以稍后分配。

* 如果将用户分配到 BlueJeans，必须在分配对话框中选择任何特定于应用程序的有效角色（如果有）。 具有“默认访问权限”  角色的用户排除在预配之外。

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>配置 BlueJeans 的自动用户预配

本部分介绍如何配置 Azure AD 预配服务以基于 Azure AD 中的用户和/或组分配在 BlueJeans 中创建、更新和禁用用户和/或组。

> [!TIP]
> 还可选择按照 [BlueJeans 单一登录教程](bluejeans-tutorial.md)中提供的说明为 BlueJeans 启用基于 SAML 的单一登录。 可以独立于自动用户预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>若要在 Azure AD 中为 BlueJeans 配置自动用户预配，请执行以下操作：

1. 登录到[Azure 门户](https://portal.azure.com)，然后选择**企业应用程序**，选择**所有应用程序**，然后选择**BlueJeans**。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中选择“BlueJeans”  。

    ![应用程序列表中的 BlueJeans 链接](common/all-applications.png)

3. 选择“预配”  选项卡。

    ![BlueJeans 预配](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. 将“预配模式”  设置为“自动”  。

    ![BlueJeans 预配](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. 在“管理员凭据”部分下，输入 BlueJeans 帐户的“管理员用户名”和“管理员密码”    。 这些值的示例如下：

   * 在“管理员用户名”字段中，填入 BlueJeans 租户上管理员帐户的用户名  。 示例：admin@contoso.com。

   * 在“管理员密码”  字段中，填入管理员用户名所对应的密码。

6. 填入步骤 5 中所示的字段后，请单击“测试连接”  以确保 Azure AD 可以连接到 BlueJeans。 如果连接失败，请确保 BlueJeans 帐户具有管理员权限，然后重试。

    ![BlueJeans 预配](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. 在“通知电子邮件”字段中，输入应接收预配错误通知的个人或组的电子邮件地址，并选中复选框“发生故障时发送电子邮件通知”   。

    ![BlueJeans 预配](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. 单击“保存”  。

9. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 BlueJeans”   。

    ![BlueJeans 预配](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. 在“属性映射”  部分中，查看从 Azure AD 同步到 BlueJeans 的用户属性。 选为“匹配”  属性的特性用于匹配 BlueJeans 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改  。

    ![BlueJeans 预配](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. 若要配置范围筛选器，请参阅[范围筛选器教程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的以下说明。

12. 若要为 BlueJeans 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”  更改为“启用”   。

    ![BlueJeans 预配](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. 通过在“设置”  部分的“范围”  中选择所需的值，定义要预配到 BlueJeans 的用户和/或组。

    ![BlueJeans 预配](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. 已准备好预配时，单击“保存”  。

    ![BlueJeans 预配](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

此操作会对“设置”部分的“范围”中定义的所有用户和/或组启动初始同步   。 初始同步执行的时间比后续同步长，只要 Azure AD 预配服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”  部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了 Azure AD 预配服务对 BlueJeans 执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>连接器限制

* Bluejeans 不允许超过 30 个字符的用户名。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>后续步骤

* [了解如何查看日志并获取有关预配活动的报告](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
