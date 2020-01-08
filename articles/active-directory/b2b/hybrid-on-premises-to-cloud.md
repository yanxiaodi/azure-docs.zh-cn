---
title: 同步到云作为 B2B 用户的 Azure Active Directory 的本地合作伙伴帐户 |Microsoft Docs
description: 使用 Azure AD B2B 协作时所用的相同凭据，向本地托管的外部合作伙伴授予对本地和云资源的访问权限。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ae75311ab61449f37ccea15a0bcb88fed80c3ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65767341"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>使用 Azure AD B2B 协作向本地托管的合作伙伴帐户授予对云资源的访问权限

在 Azure Active Directory (Azure AD) 推出之前，使用本地标识系统的组织一贯是在其本地目录中管理合作伙伴帐户。 在此类组织中，开始将应用转移到 Azure AD 时，需要确保合作伙伴能够访问所需的资源。 资源是本地还是云中并不重要。 此外，你希望合作伙伴用户能够对本地和 Azure AD 资源使用相同的登录凭据。 

如果在本地目录中创建外部合作伙伴的帐户（例如，在 partners.contoso.com 域中为名为 Wendy Moran 的外部用户创建登录名为“wmoran”的帐户），则现在可将这些帐户同步到云。 具体而言，可以使用 Azure AD Connect 将合作伙伴帐户作为 Azure AD B2B 用户（即 UserType = Guest 的用户）同步到云。 这样，合作伙伴用户便可以使用与其本地帐户相同的凭据访问云资源，且不需要向他们授予超过需要的访问权限。 

## <a name="identify-unique-attributes-for-usertype"></a>识别 UserType 的唯一属性

在启用 UserType 属性的同步之前，必须首先确定如何从本地 Active Directory 派生 UserType 属性。 换句话说，你的本地环境中的哪些参数对于外部协作者而言是唯一的？ 确定能够将这些外部协作者与你自己的组织中的成员进行区分的参数。

用于实现此目的的两种常用方法是：

- 指定一个未使用的本地 Active Directory 属性（例如 extensionAttribute1）来用作源属性。 
- 或者，从其他属性派生 UserType 属性的值。 例如，你想要将所有用户都同步为 Guest，如果其本地 Active Directory UserPrincipalName 属性结尾域 *\@partners.contoso.com*。
 
有关详细的属性要求，请参阅[启用 UserType 同步](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)。 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>配置 Azure AD Connect 来将用户同步到云

在识别唯一属性后，可以配置 Azure AD Connect 来将这些用户作为 Azure AD B2B 用户（即 UserType = Guest 的用户）同步到云。 从授权角度来看，这些用户与通过 Azure AD B2B 协作邀请流程创建的 B2B 用户没有区别。

有关实现说明，请参阅[启用 UserType 同步](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)。

## <a name="next-steps"></a>后续步骤

- [适用于混合组织的 Azure Active Directory B2B 协作](hybrid-organizations.md)
- [向 Azure AD 中的 B2B 用户授予对本地应用程序的访问权限](hybrid-cloud-to-on-premises.md)
- 有关 Azure AD Connect 的概述，请参阅[将本地目录与 Azure Active Directory 进行集成](../hybrid/whatis-hybrid-identity.md)。

