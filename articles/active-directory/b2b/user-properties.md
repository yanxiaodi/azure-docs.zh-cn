---
title: 属性的某个 B2B 来宾用户的 Azure Active Directory |Microsoft Docs
description: Azure Active Directory B2B 来宾用户属性和状态之前和之后邀请兑换
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1416dacd65024457e713547223f5c35290b3d15
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65768164"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Azure Active Directory B2B 协作用户的属性

本文介绍的属性和 Azure Active Directory (Azure AD) 中的 B2B 来宾用户对象的状态之前和之后邀请兑换。 Azure AD 企业到企业 (B2B) 协作用户是 usertype = Guest。 此来宾用户通常来自某个合作伙伴组织，默认情况下，对邀请方的目录拥有有限的特权。

根据邀请方组织的需要，Azure AD B2B 协作用户可以处于以下帐户状态之一：

- 状态 1：驻留在 Azure AD 的外部实例中，代表邀请方组织中的来宾用户。 在这种情况下，B2B 用户需使用属于受邀方租户的 Azure AD 帐户进行登录。 如果合作伙伴组织不使用 Azure AD，仍会在 Azure AD 中创建来宾用户。 相应要求是，他们兑换自己的邀请，并由 Azure AD 验证其电子邮件地址。 此安排也称为实时 (JIT) 租户或“促销型”租户。

- 状态 2：驻留在 Microsoft 或其他帐户中，代表宿主组织中的来宾用户。 这种情况下，来宾用户使用 Microsoft 帐户或社交帐户（google.com 或类似账户）登录。 产品兑换期间，在邀请方组织目录中将受邀用户标识创建为 Microsoft 帐户。

- 状态 3：驻留在宿主组织的本地 Active Directory 中，并且与宿主组织的 Azure AD 同步。 可以使用 Azure AD Connect 将合作伙伴帐户作为 Azure AD B2B 用户（即 UserType = Guest 的用户）同步到云。 请参阅[向本地托管的合作伙伴帐户授予对云资源的访问权限](hybrid-on-premises-to-cloud.md)。

- 状态 4：驻留在宿主组织的 Azure AD 中并且 UserType = Guest，其凭据由宿主组织管理。

  ![图描绘了四个用户状态](media/user-properties/redemption-diagram.png)


现在，让我们看看 Azure AD B2B 协作用户在 Azure AD 中的大致情况。

### <a name="before-invitation-redemption"></a>兑换邀请之前

状态 1 和状态 2 帐户是邀请来宾用户使用来宾用户自己的凭据进行协作的结果。 最初向来宾用户发送邀请时，会在目录中创建帐户。 这个帐户没有与之关联的任何凭据，因为是由来宾用户的标识提供者执行身份验证。 目录中来宾用户帐户的“源”属性设置为“受邀用户”   。 

![显示产品/服务兑换之前的用户属性的屏幕截图](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>兑换邀请之后

来宾用户接受邀请之后，将根据来宾用户的标识提供者更新“源”属性  。

对于状态 1 中的来宾用户，“源”为“外部 Azure Active Directory”   。

![兑换产品之后，状态 1 的来宾用户](media/user-properties/after-redemption-state1.png)

对于状态 2 中的来宾用户，“源”为“Microsoft 帐户”   。

![兑换产品之后，状态 2 的来宾用户](media/user-properties/after-redemption-state2.png)

对于状态 3 和状态 4 中的来宾用户，“源”属性设置为“Azure Active Directory”或“Windows Server Active Directory”，如下一节所述    。

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Azure AD B2B 协作用户的关键属性
### <a name="usertype"></a>UserType
此属性表示用户与宿主租户之间的关系。 此属性可以具有两个值：
- 成员：此值表示宿主组织的某位员工，即组织的工资单中的某个用户。 例如，此用户应当对仅限内部站点具有访问权限。 此用户不被视为外部协作者。

- 来宾：此值表示不被视为公司内部成员的用户，例如外部协作者、合作伙伴或客户。 此类用户不会接收 CEO 的内部备注，也不会享受公司福利等。

  > [!NOTE]
  > UserType 与用户的登录方式、用户的目录角色等等之间没有关系。 此属性只是指明该用户与宿主组织之间的关系，使该组织能够实施依赖于此属性的策略。

### <a name="source"></a>Source
此属性指示用户如何登录。

- 受邀用户：此用户已受邀但尚未兑换其邀请。

- 外部 Active Directory：此用户驻留在外部组织中，使用属于另一组织的 Azure AD 帐户进行身份验证。 此登录类型对应于状态 1。

- Microsoft 帐户：此用户驻留在某个 Microsoft 帐户中，使用 Microsoft 帐户进行身份验证。 此登录类型对应于状态 2。

- Windows Server Active Directory：此用户从属于此组织的本地 Active Directory 进行登录。 此登录类型对应于状态 3。

- Azure Active Directory：此用户使用属于此组织的 Azure AD 帐户进行身份验证。 此登录类型对应于状态 4。
  > [!NOTE]
  > Source 和 UserType 是独立的属性。 Source 的值并不暗示特定的 UserType 值。

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>是否可将 Azure AD B2B 用户添加为成员而非来宾？
通常，Azure AD B2B 用户和来宾用户是同义词。 因此，默认情况下，Azure AD B2B 协作用户将添加为 UserType = Guest 的用户。 但在某些情况下，合作伙伴组织又是一家更大型上级组织的成员，而宿主组织也属于该大型组织。 如果是这样，宿主组织可能希望将合作伙伴组织中的用户视为成员而非来宾。 可以使用 Azure AD B2B 邀请管理器 API 将合作伙伴组织中的用户作为成员添加或邀请到宿主组织。

## <a name="filter-for-guest-users-in-the-directory"></a>对目录中的来宾用户进行筛选

![显示来宾用户的筛选器的屏幕截图](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>转换 UserType
可使用 PowerShell 将 UserType 从“成员”转换为“来宾”，反之亦然。 但是，UserType 属性表示用户与组织之间的关系。 因此，只有当用户与组织之间的关系发生更改时，才应当更改此属性。 如果用户的关系发生更改，用户主体名称 (UPN) 是否应该更改？ 用户是否应该继续有权访问同样的资源？ 是否应该分配邮箱？ 我们不建议使用 PowerShell 以原子活动的形式更改 UserType。 此外，为防止使用 PowerShell 导致此属性不可变，我们不建议对此值产生依赖关系。

## <a name="remove-guest-user-limitations"></a>删除来宾用户限制
在某些情况下，你可能想要为来宾用户提供更高的特权。 可将来宾用户添加到任何角色，甚至可在目录中删除默认的来宾用户限制，向用户提供与成员相同的特权。

可以禁用默认限制，便于为公司目录中的来宾用户提供与成员用户相同的权限。

![用户设置中显示的外部用户选项的屏幕截图](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>能否在 Exchange 全局地址列表中显示来宾用户？
是的。 默认情况下，来宾对象在组织的全局地址列表中不可见，但可使用 Azure Active Directory PowerShell 使其可见。 有关详细信息，请参阅[在 Office 365 组中管理来宾访问权限](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?redirectSourcePath=%252fen-us%252farticle%252fmanage-guest-access-in-office-365-groups-9de497a9-2f5c-43d6-ae18-767f2e6fe6e0&view=o365-worldwide#faq)中的**能否在全局地址列表中显示来宾对象？** 。 

## <a name="next-steps"></a>后续步骤

* [什么是 Azure AD B2B 协作？](what-is-b2b.md)
* [B2B 协作用户令牌](user-token.md)
* [B2B 协作用户声明映射](claims-mapping.md)
