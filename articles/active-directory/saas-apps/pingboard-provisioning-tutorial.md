---
title: 教程：使用 Azure Active Directory 为 Pingboard 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Pingboard 和取消其预配。
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f7e2fabc86374f7fe055303d056ae8e00f33389
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964372"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>教程：为 Pingboard 配置自动用户预配

本教程的目的在于介绍将用户帐户从 Azure Active Directory (Azure AD) 自动预配到 Pingboard 和取消其预配时所要遵循的步骤。

## <a name="prerequisites"></a>必备组件

在本教程中概述的方案假定您已具有以下各项：

* Azure AD 租户
* Pingboard 租户[专业版帐户](https://pingboard.com/pricing)
* Pingboard 中具有管理员权限的用户帐户

> [!NOTE]
> Azure AD 预配集成依赖于帐户可用的 [Pingboard API](https://pingboard.docs.apiary.io/#)。

## <a name="assign-users-to-pingboard"></a>将用户分配到 Pingboard

Azure AD 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和组。 

配置和启用预配服务前，需确定 Azure AD 中的哪些用户或组需访问 Pingboard 应用。 确定后，可以按照此处的说明将这些用户分配到 Pingboard 应用：

[向企业应用分配用户](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>将用户分配到 Pingboard 的重要提示

建议将单个 Azure AD 用户分配到 Pingboard 以测试预配配置。 其他用户可以稍后分配。

## <a name="configure-user-provisioning-to-pingboard"></a>向 Pingboard 配置用户预配 

本部分将指导你完成将 Azure AD 连接到 Pingboard 的用户帐户预配 API。 还可根据 Azure AD 中的用户和组分配，配置预配服务，以创建、更新和禁用 Pingboard 中分配的用户帐户。

> [!TIP]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Pingboard 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>若要在 Azure AD 中为 Pingboard 配置自动用户帐户预配，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory” > “企业应用” > “所有应用程序”部分    。

1. 如果已为 Pingboard 配置单一登录，请使用搜索字段搜索 Pingboard 实例。 否则，请选择“添加”  ，然后在应用程序库中搜索“Pingboard”  。 从搜索结果中选择“Pingboard”，并将其添加到应用程序列表  。

1. 选择 Pingboard 实例，并选择“预配”选项卡  。

1. 将“预配模式”  设置为“自动”  。

    ![Pingboard 预配](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. 在“管理员凭据”  部分中，使用以下步骤：

    a. 在“租户 URL”中，输入 `https://your_domain.pingboard.com/scim/v2` 并将 your_domain 替换为实际的域 

    b. 使用管理员帐户登录到 [Pingboard](https://pingboard.com/)。

    c. 选择**外接程序** > **集成** > **Azure Active Directory**。

    d. 单击“配置”选项卡并选择“从 Azure 启用用户预配”   。

    e. 复制中的令牌**OAuth 持有者令牌**，并在输入中**机密令牌**。

1. 在 Azure 门户中，选择“测试连接”  以测试 Azure AD 可连接到 Pingboard 应用。 如果连接失败，请测试 Pingboard 帐户具有管理员权限，并重试“测试连接”  步骤。

1. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址  。 然后选中复选框。

1. 选择“保存”。 

1. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Pingboard”   。

1. 在“属性映射”  部分中，查看将从 Azure AD 同步到 Pingboard 的用户属性。 选为“匹配”  属性的特性用于匹配 Pingboard 中的用户帐户以执行更新操作。 选择“保存”，提交所有更改  。 有关详细信息，请参阅[自定义用户预配属性映射](../manage-apps/customize-application-attributes.md)。

1. 若要为 Pingboard 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”    。

1. 单击“保存”，开始对分配到 Pingboard 的用户执行初始同步  。

初始同步运行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动日志的链接  。 这些日志描述了预配服务对 Pingboard 应用所采取的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [配置单一登录](pingboard-tutorial.md)
