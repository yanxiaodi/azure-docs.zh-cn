---
title: Azure Active Directory 条件访问中的条件是什么？ | Microsoft Docs
description: 了解如何条件中使用 Azure Active Directory 条件性访问来触发策略。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e6c18b7ffca83c8e0fe9576ec275f89b6db7b4f
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509013"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 条件访问中的条件是什么？

您可以控制用户可以访问你的云应用通过使用[Azure Active Directory (Azure AD) 条件性访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)。 在条件性访问策略中，定义的响应 （"然后执行此操作"） 到 （"在此情况下"） 触发策略的原因。

![原因和响应](./media/conditions/10.png)

条件性访问的上下文中**这何时发生**称为**条件**。 “则执行此操作”称为“访问控制”。   你的条件和访问控制的组合表示条件性访问策略。

![条件性访问策略](./media/conditions/61.png)

不会应用尚未配置条件性访问策略中的条件。 某些条件均[必需](best-practices.md)将条件性访问策略应用到环境。

本文是条件和及其使用方式的条件性访问策略中的概述。 

## <a name="users-and-groups"></a>用户和组

用户和组条件是条件性访问策略中必需的。 在策略中，可以选择**所有用户**或选择特定的用户和组。

![用户和组](./media/conditions/111.png)

选择“所有用户”时，策略将应用到目录中的所有用户，包括来宾用户。 

**选择用户和组**时，可以设置以下选项：

* “所有来宾用户”使策略面向 B2B 来宾用户。  此条件与将 **userType** 属性设置为“来宾”的所有用户帐户匹配。  如果在 Azure AD 的邀请流中创建帐户后需要应用策略，请使用此设置。
* “目录角色”基于用户的角色分配定位策略。  此条件支持目录角色，如“全局管理员”  或“密码管理员”  。
* “用户和组”面向特定的用户集。  例如，将某个人力资源应用选作云应用时，可以选择包含人力资源部所有成员的组。 某个组可以是 Azure AD 中任何类型的组，包括动态组，或分配的安全组和通讯组。

此外，你还可以从策略中排除特定的用户或组。 例如，在策略强制实施多重身份验证 (MFA) 的情况下，往往会排除服务帐户。

若要部署新策略，面向特定的用户集十分有用。 在新策略中，应该只将初始用户集用作目标来验证策略行为。

## <a name="cloud-apps-and-actions"></a>云应用程序和操作

云应用是网站、 服务或终结点保护的 Azure AD 应用程序代理。 有关受支持云应用的详细说明，请参阅[云应用分配](technical-reference.md#cloud-apps-assignments)。 **云应用程序或操作**条件是条件性访问策略中必需的。 在策略中，您可以选中**所有云应用**，或者指定使用应用程序**选择应用**。

组织可以选择以下选项：

* **所有云应用**时应用基准策略要应用于整个组织。 将此选项用于所有云应用检测到登录风险时都要求多重身份验证的策略。 应用于所有云应用的策略适用于访问到所有网站和服务。 此设置不限于选择应用程序列表中显示的云应用。
* “选择应用”，以便根据策略将特定服务指定为目标  。 例如，可以要求用户具有兼容的设备访问 SharePoint Online。 当其他服务访问 SharePoint 内容时，也会对这些服务应用此策略。 例如 Microsoft Teams。

> [!NOTE]
> 可以从策略中排除特定的应用。 但是，这些应用仍受到应用于它们访问的服务的策略的限制。

**用户操作**是用户可以执行的任务。 当前支持的唯一操作是**注册安全信息 （预览版）** ，它允许条件性访问策略强制执行时组合注册为启用的用户尝试注册其安全信息。 可在本文中，找到更多信息[启用结合安全信息注册 （预览版）](../authentication/howto-registration-mfa-sspr-combined.md)。

## <a name="sign-in-risk"></a>登录风险

登录风险指不是由用户帐户合法所有者执行的登录的可能性（高、中或低）。 Azure AD 在用户登录期间会计算登录风险级别。 计算登录风险级别可用作条件性访问策略中的条件。

![登录风险级别](./media/conditions/22.png)

若要使用此条件，需要启用 [Azure Active Directory 标识保护](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable)。
 
此条件的常见用例包括提供以下保护的策略： 

- 阻止存在高登录风险的用户。 此项保护可防止潜在的非法用户访问你的云应用。 
- 要求中等登录风险的用户执行多重身份验证。 通过强制实施多重身份验证，可以更加肯定登录操作是由帐户的合法所有者执行的。

有关详细信息，请参阅[检测到会话风险时阻止访问](app-sign-in-risk.md)。  

## <a name="device-platforms"></a>设备平台

设备平台根据设备上运行的操作系统来定义特征。 Azure AD 使用设备（例如用户代理）提供的信息来标识平台。 此信息未经验证。 我们建议在所有平台中应用某个策略。 该策略应阻止访问、要求符合 Microsoft Intune 政策，或要求设备加入域。 默认设置是将策略应用到所有设备平台。 

![配置设备平台](./media/conditions/24.png)

有关支持的设备平台列表，请参阅[设备平台条件](technical-reference.md#device-platform-condition)。

此条件的一个常见用例是，策略将云应用的访问权限制为[托管设备](require-managed-devices.md)。 有关更多方案，包括设备平台条件，请参阅[Azure Active Directory 基于应用的条件性访问](app-based-conditional-access.md)。

## <a name="device-state"></a>设备状态

设备状态条件排除加入的混合 Azure AD 的设备和设备标记为符合条件访问策略中。 

![配置设备状态](./media/conditions/112.png)

当策略仅应用到非托管设备以使会话更安全时，此条件非常有用。 例如，当设备处于非托管状态时，仅强制执行 Microsoft Cloud App Security 会话控制。 

## <a name="locations"></a>位置

使用位置可以基于尝试连接的位置定义条件。 

![配置位置](./media/conditions/25.png)

此条件的常见用例包括提供以下保护的策略：

- 用户访问服务，它们在公司网络时要求多重身份验证。  
- 阻止特定国家或地区的用户访问服务。 

有关详细信息，请参阅[什么是 Azure Active Directory 条件访问中的位置条件？](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)。

## <a name="client-apps"></a>客户端应用

默认情况下，条件性访问策略应用于以下应用：

- **[浏览器应用](technical-reference.md#supported-browsers)** -  浏览器应用包括使用 SAML、WS-Federation 或 OpenID Connect Web SSO 协议的网站。 这也适用于已注册为 OAuth 机密客户端的任何网站或 Web 服务。 例如，Office 365 SharePoint 网站。 
- **[使用新式身份验证的移动和桌面应用](technical-reference.md#supported-mobile-applications-and-desktop-clients)** - 这些应用包括 Office 桌面应用和手机应用。 


另外，可以让策略针对不使用新式身份验证的特定客户端应用，例如：

- **[Exchange ActiveSync 客户端](conditions.md#exchange-activesync-clients)** - 当某项策略阻止使用 Exchange ActiveSync 时，受影响的用户会收到一封有关隔离的电子邮件，说明为何会阻止他们。 必要时，该电子邮件会说明如何将设备注册到 Intune。
- **[其他客户端](block-legacy-authentication.md)** - 这些应用包括通过邮件协议（如 IMAP、MAPI、POP、SMTP）使用基本身份验证的客户端，以及不使用新式身份验证的旧版 Office 应用。 有关详细信息，请参阅[如何对 Office 2013 和 Office 2016 客户端应用使用新式身份验证](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016)。

![客户端应用](./media/conditions/41.png)

此条件的常见用例包括提供以下要求的策略：

- 对于将数据下载到设备的移动和桌面应用程序， **[要求使用托管设备](require-managed-devices.md)** 。 同时，允许从任何设备上的浏览器进行访问。 此方案阻止将文档保存并同步到非托管设备。 使用此方法，在设备丢失或失窃的情况下就可以降低数据丢失的可能性。
- 对于使用 ActiveSync 来访问 Exchange Online 的应用， **[需要托管设备](require-managed-devices.md)** 。
- **[阻止向 Azure AD 进行的旧身份验证](block-legacy-authentication.md)** （其他客户端）
- 阻止从 Web 应用程序访问，但允许从移动和桌面应用程序访问。

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync 客户端

只有在以下情况下，才可以选在“Exchange ActiveSync 客户端”： 

- Microsoft Office 365 Exchange Online 是所选的唯一云应用。

    ![云应用](./media/conditions/32.png)

- 未在策略中配置其他条件。 但是，可以缩小此条件的范围，使之仅应用到[支持的平台](technical-reference.md#device-platform-condition)。
 
    ![仅将策略应用到支持的平台](./media/conditions/33.png)

如果系统因为要求使用[托管设备](require-managed-devices.md)而阻止某些用户的访问，则受影响的用户会收到一封引导他们使用 Intune 的邮件。 

如果系统要求使用经审核的应用，则受影响的用户会收到 Outlook 移动客户端的安装和使用指南。

在其他情况下（例如，需要 MFA），则会阻止受影响用户，因为使用基本身份验证的客户端不支持 MFA。

只能将此设置用于用户和组。 它不支持来宾或角色。 如果配置来宾或角色的条件，因为无法确定条件性访问，或不如果该策略将应用到用户，将阻止所有用户。

有关详细信息，请参阅：

- [为 Azure Active Directory 条件性访问设置 SharePoint Online 和 Exchange Online](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)。
- [Azure Active Directory 基于应用的条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)。 

## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件性访问策略，请参阅[快速入门：使用 Azure Active Directory 条件性访问需要针对特定应用的 MFA](app-based-mfa.md)。
- 若要配置你的环境的条件性访问策略，请参阅[的 Azure Active Directory 中条件性访问的最佳做法](best-practices.md)。 
