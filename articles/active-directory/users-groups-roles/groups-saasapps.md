---
title: 使用组来管理对 SaaS 应用程序的访问 - Azure Active Directory | Microsoft Docs
description: 如何在 Azure Active Directory 中使用组来分配与 Azure Active Directory 集成的 SaaS 应用程序的访问权限。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75fdd0fcd4ceeeb0f680b2cbdb743d30d236c93d
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68736506"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>使用组来管理对 SaaS 应用程序的访问

将 Azure Active Directory (Azure AD) 与 Azure AD Premium 许可证计划结合使用, 可以使用组分配对与 Azure AD 集成的 SaaS 应用程序的访问权限。 例如，如果要为营销部分配使用五个不同 SaaS 应用程序所需的访问权限，则可以创建一个包含销售部用户的组，然后将该组分配给营销部所需的这五个 SaaS 应用程序。 这样就可以在一个位置管理营销部的成员身份，从而节省了时间。 之后，当将用户添加为营销组的成员时，这些用户将分配到应用程序；当将用户从营销组中删除时，也会从应用程序中删除为其分配的权限。 此功能适用于可从 Azure AD 应用程序库中添加的数百个应用程序。

> [!IMPORTANT]
> 只有开始 Azure AD Premium 试用或购买 Azure AD Premium 许可计划后, 才能使用此功能。
> 仅安全组支持基于组的分配。
> 目前应用程序的基于组的分配不支持嵌套的组成员身份。

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>为用户或组分配对 SaaS 应用程序的访问权限

1. 在 [Azure AD 管理中心](https://aad.portal.azure.com)内，选择“企业应用程序”。
2. 选择从应用程序库添加的应用程序，即可打开它。
3. 依次选择“用户和组”和“添加用户”。
4. 在“添加分配”上，选择“用户和组”，打开“用户和组”选择列表。
6. 选择任意多个组或用户，再单击或点击“选择”，将它们添加到“添加分配”列表。 还可以在此阶段向用户分配角色。
7. 选择“分配”，将用户或组分配到选定的企业应用程序。

## <a name="next-steps"></a>后续步骤
这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory 中的应用程序管理](../manage-apps/what-is-application-management.md)
* [用于配置组设置的 Azure Active Directory cmdlet](groups-settings-cmdlets.md)
* [什么是 Azure Active Directory？](../fundamentals/active-directory-whatis.md)
* [将本地标识与 Azure Active Directory 集成](../hybrid/whatis-hybrid-identity.md)
