---
title: 教程：使用 Azure Active Directory 为 DocuSign 配置自动用户预配 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 DocuSign 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 121d147a3f8c91f17e955120b2c14f7dbd3da592
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60280121"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>教程：为 DocuSign 配置自动用户预配

本教程旨在介绍为了从 Azure AD 自动将用户帐户预配到 DocuSign 以及取消其预配而需要在 DocuSign 和 Azure 中执行的步骤。

## <a name="prerequisites"></a>必备组件

在本教程中概述的方案假定已有以下各项：

*   Azure Active Directory 租户。
*   已启用 DocuSign 单一登录的订阅。
*   具有团队管理员权限的 DocuSign 用户帐户。

## <a name="assigning-users-to-docusign"></a>将用户分配到 DocuSign

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中应用程序的用户和组。

在配置和启用预配服务前，需确定 Azure AD 中哪些用户和/或组表示需要访问 DocuSign 应用的用户。 确定后，可按照此处的说明将这些用户分配到 DocuSign 应用：

[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>将用户分配到 DocuSign 的重要提示

*   建议将单个 Azure AD 用户分配到 DocuSign 以测试预配配置。 其他用户可以稍后分配。

*   将用户分配到 DocuSign 时，必须选择有效的用户角色。 “默认访问权限”角色不可用于预配。

> [!NOTE]
> Azure AD 不支持使用 Docusign 应用程序预配组，只能预配用户。

## <a name="enable-user-provisioning"></a>启用用户预配

本部分介绍了如何将 Azure AD 连接到 DocuSign 的用户帐户预配 API 和配置预配服务，以便在 DocuSign 中根据 Azure AD 中的用户和组分配创建、更新和禁用分配的用户帐户。

> [!Tip]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 DocuSign 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-user-account-provisioning"></a>配置用户帐户预配：

本部分的目的是概述如何对 DocuSign 启用 Active Directory 用户帐户的用户预配。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分  。

1. 如果已为 DocuSign 配置单一登录，请使用搜索字段搜索 DocuSign 实例。 否则，请选择“添加”  ，然后在应用程序库中搜索“DocuSign”  。 从搜索结果中选择 DocuSign，并将其添加到应用程序列表。

1. 选择 DocuSign 实例，然后选择“预配”  选项卡。

1. 将“预配模式”  设置为“自动”  。 

    ![预配](./media/docusign-provisioning-tutorial/provisioning.png)

1. 在“管理员凭据”  部分中，提供以下配置设置：
   
    a. 在“管理员用户名”  文本框中，键入在 DocuSign.com 中已分配“系统管理员”  配置文件的 DocuSign 帐户名称。
   
    b. 在“管理员密码”  文本框中，键入此帐户的密码。

1. 在 Azure 门户中，单击“测试连接”  ，确保 Azure AD 可以连接到 DocuSign 应用。

1. 在“通知电子邮件”  字段中输入应收到预配错误通知的用户或组的电子邮件地址，并选中复选框。

1. 单击“保存”  。

1. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 DocuSign”  。

1. 在“属性映射”  部分中，查看从 Azure AD 同步到 DocuSign 的用户属性。 选为“匹配”  属性的特性用于匹配 DocuSign 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

1. 若要为 DocuSign 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”  更改为“启用” 

1. 单击“保存”  。

此操作会对“用户和组”部分中分配到 DocuSign 的任何用户启动初始同步。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。 可以使用“同步详细信息”  部分监视进度并跟踪指向预配活动日志的链接，这些日志描述了预配服务对 DocuSign 应用执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [配置单一登录](docusign-tutorial.md)
