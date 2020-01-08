---
title: 如何在 Azure Active Directory 标识保护中配置登录风险策略| Microsoft Docs
description: 了解如何配置“Azure AD 标识保护”登录风险策略。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00376c6689b6be773f24e8acd09c3697fb6a799
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126309"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>如何：配置登录风险策略

Azure Active Directory 实时、脱机地检测[风险检测类型](../reports-monitoring/concept-risk-events.md#risk-detection-types)。 已检测到的用户登录的每个风险检测都有助于成为有风险登录的逻辑概念。 风险登录是指可能不是由用户帐户合法拥有者进行的登录尝试。

## <a name="what-is-the-sign-in-risk-policy"></a>什么是登录风险策略？

Azure AD 会分析用户的每次登录。 分析的目的是检测伴随登录而来的可疑操作。 例如，登录是否是使用匿名 IP 地址执行的？或者，登录是否是从不熟悉的位置发起的？ 在 Azure AD 中, 系统可以检测到的可疑操作也称为风险检测。 根据在登录期间检测到的风险检测, Azure AD 计算一个值。 该值表示登录不是由合法用户执行的可能性（低、中、高）。 此可能性称为**登录风险级别**。

登录风险策略是可以为特定登录风险级别配置的自动响应。 在响应中，你可以阻止对资源的访问，或者要求传递多重身份验证 (MFA) 质询来获取访问权限。
   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>如何访问登录风险策略？
   
登录风险策略位于[“Azure AD 标识保护”页](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)上的“配置”部分中。
   
![登录风险策略](./media/howto-sign-in-risk-policy/1014.png "登录风险策略")

## <a name="policy-settings"></a>策略设置

配置登录风险策略时，需要设置：

- 该策略应用到的用户和组：

    ![用户和组](./media/howto-sign-in-risk-policy/11.png)

- 触发该策略的登录风险级别：

    ![登录风险级别](./media/howto-sign-in-risk-policy/12.png)

- 当满足登录风险级别时要强制实施的访问类型：  

    ![访问权限](./media/howto-sign-in-risk-policy/13.png)

- 策略的状态：

    ![强制实施策略](./media/howto-sign-in-risk-policy/14.png)

策略配置对话框提供了一个选项来评估重新配置的影响。

![估计影响](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>要点

可以配置登录风险安全策略来要求进行 MFA：

![要求 MFA](./media/howto-sign-in-risk-policy/16.png)

但是，出于安全原因，此设置仅适用于已注册 MFA 的用户。 如果用户尚未注册 MFA，则标识保护会使用 MFA 要求阻止用户。

如果希望要求有风险的登录进行 MFA，则应当：

1. 对受影响的用户启用[多重身份验证注册策略](howto-mfa-policy.md)。
2. 要求受影响的用户登录无风险会话来执行 MFA 注册。

完成这些步骤可确保对有风险的登录要求执行多重身份验证。

登录风险策略：

- 应用到所有使用新式身份验证的浏览器流量和登录。
- 不会应用到使用旧式安全协议并在联合 IDP 上禁用 WS-Trust 终结点的应用程序，例如 ADFS。

如需相关用户体验的概述，请参阅：

* [有风险的登录恢复](flows.md#risky-sign-in-recovery)
* [有风险的登录已阻止](flows.md#risky-sign-in-blocked)  
* [Azure AD 标识保护中的登录体验](flows.md)  

## <a name="best-practices"></a>最佳实践

选择“高”阈值可减少触发策略的次数，最大程度地降低对用户的影响。  

但是，它会从策略中排除标记为“低”和“中”风险的登录，而无法阻止攻击者利用遭到入侵的标识。

设置策略时：

- 排除没有/无法注册多重身份验证的用户
- 排除无法启用策略（例如无法访问技术支持）的区域中的用户
- 排除可能会生成大量误报的用户（开发人员、安全分析人员）
- 在首次实施策略期间，或者必须尽量减少向最终用户显示质询时，请使用“高”阈值。
- 如果组织需要更高的安全性，请使用“低”阈值。 选择“低”阈值会显示更多的用户登录质询，但可以提高安全性。

对于大多数组织而言，建议的默认值是设置“中”阈值的规则，以便在可用性与安全性之间取得均衡。

## <a name="next-steps"></a>后续步骤

若要获取“Azure AD 标识保护”的概述，请参阅 [Azure AD 标识保护概述](overview.md)。
