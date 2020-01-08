---
title: 预配到 Azure AD 库应用程序的用户组错误 | Microsoft Docs
description: 了解如何找到为何预配到应用程序的用户组与预期不同的原因
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1727c45378e36abf695fca32c6e630806b4a6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784502"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>预配到 Azure AD 库应用程序的用户组错误

将哪些用户预配到应用主要取决于已将哪些用户和组**分配**到该应用程序。

使用以下资源，了解如何查看已将哪些用户和组分配到 Azure Active Directory 中的应用程序。

## <a name="assign-a-user-directly-as-an-administrator"></a>以管理员身份直接将用户分配到应用程序

若要直接将一个或多个用户分配到应用程序，请执行以下步骤：

1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”  身份登录。

2. 在左侧主导航菜单顶部单击“所有服务”  ，打开“Azure Active Directory 扩展”  。

3. 在筛选器搜索框中键入“Azure Active Directory”  ，选择“Azure Active Directory”  项。

4. 在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”  。

5. 单击“所有应用程序”  ，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”  顶部的“筛选器”  控件，并将“显示”  选项设置为“所有应用程序”  。

6. 从列表中选择要向其分配用户的应用程序。

7. 在应用程序加载后，在应用程序的左侧导航菜单中单击“用户和组”  。

8. 若要打开“添加分配”窗格，请单击“用户和组”列表顶部的“添加”按钮。   

9. 在“添加分配”  窗格中，单击“用户和组”  选择器。

10. 在“按名称或电子邮件地址搜索”  搜索框中，键入要分配的用户的**全名**或**电子邮件地址**。

11. 将鼠标悬停在列表中的“用户”  上方以显示“复选框”  。 单击用户个人资料头像或徽标旁边的复选框，将用户添加到“已选择”  列表。

12. **可选：** 如果要“添加多个用户”，请在“按名称或电子邮件地址搜索”搜索框中键入其他“全名”或“电子邮件地址”，然后单击复选框以将此用户添加到“已选择”列表      。

13. 在完成用户的选择后，单击“选择”  按钮将他们添加到要分配给应用程序的用户和组列表。

14. **可选：** 单击“添加分配”  窗格中的“选择角色”  选择器，选择一个角色来分配给所选用户。

15. 单击“分配”  按钮，将应用程序分配给选定用户。

如果已配置预配且已对应用运行预配，新用户应会在约 10 分钟后预配到应用程序。 查看**审核日志**了解详细信息。

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>以管理员身份直接将组分配到应用程序

若要直接将一个或多个组分配到应用程序，请执行以下步骤：

1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”  身份登录。

2. 在左侧主导航菜单顶部单击“所有服务”  ，打开“Azure Active Directory 扩展”  。

3. 在筛选器搜索框中键入“Azure Active Directory”  ，选择“Azure Active Directory”  项。

4. 在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”  。

5. 单击“所有应用程序”  ，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”  顶部的“筛选器”  控件，并将“显示”  选项设置为“所有应用程序”  。

6. 从列表中选择要向其分配用户的应用程序。

7. 在应用程序加载后，在应用程序的左侧导航菜单中单击“用户和组”  。

8. 若要打开“添加分配”窗格，请单击“用户和组”列表顶部的“添加”按钮。   

9. 在“添加分配”  窗格中，单击“用户和组”  选择器。

10. 在“按名称或电子邮件地址搜索”  搜索框中，键入要分配的组的**完整组名**。

11. 将鼠标悬停在列表中的**组**上以显示**复选框**。 单击组头像或徽标旁边的复选框以将用户添加到“已选择”  列表。

12. **可选：** 如果要“添加多个组”，请在“按名称或电子邮件地址搜索”搜索框中键入其他“完整组名”，然后单击复选框以将此组添加到“已选择”列表     。

13. 选择完所有组后，单击“选择”  按钮将所有已选择的组添加到要分配给应用程序的用户和组的列表中。

14. **可选：** 单击“添加分配”  窗格中的“选择角色”  选择器，选择一个角色来分配给所选组。

15. 单击“分配”  按钮，将应用程序分配给所选组。

如果预配已配置且已针对应用运行，应在约 10 分钟内将组中包含的新用户预配到应用程序。 查看**审核日志**了解详细信息。

>[!IMPORTANT]
>如果某些应用程序支持，除了成员以外，还可以对组名和详细信息进行预配。 可通过对“预配”  选项卡中显示的组对象启用或禁用**映射**，从而启用或禁用此功能。 
>
>

如果启用预配组，请务必查看属性映射以确保相应字段用于“匹配 ID”。 此匹配 ID 可以是显示名称或电子邮件别名。 如果匹配属性为空，或没有为 Azure AD 中的某个组填写时，该组及组成员将不会进行预配。

## <a name="next-steps"></a>后续步骤
[通过 Azure Active Directory 应用程序为 SaaS 自动化用户预配和取消预配](user-provisioning.md)
