---
title: 如何使用“Azure Active Directory 标识保护”取消阻止用户 | Microsoft Doc
description: 了解如何取消阻止已由 Azure Active Directory 标识保护策略阻止的用户。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e3756435703c4e8c887a4e7b9d4f75a6701d840
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126251"
---
# <a name="how-to-unblock-users"></a>如何：取消阻止用户

通过 Azure Active Directory 标识保护，可将策略配置为在满足配置条件的情况下阻止用户。 通常情况下，受阻止的用户可联系支持人员以取消阻止。 本文介绍了取消阻止受阻止用户的可执行步骤。

## <a name="determine-the-reason-for-blocking"></a>确定阻止的原因

取消阻止用户的第一步是需要确定已阻止用户的策略类型，因为后续步骤都依赖该策略类型。
通过 Azure Active Directory 标识保护，用户可能受登录风险策略或用户风险策略阻止。

可以从对话框中的标题（在登录尝试期间显示给用户）获取已阻止用户的策略类型。

| 策略 | 用户对话框 |
| --- | --- |
| 登录风险 |![阻止的登录](./media/howto-unblock-user/02.png) |
| 用户风险 |![阻止的帐户](./media/howto-unblock-user/104.png) |

受以下策略阻止的用户：

* 登录风险策略也称为可疑登录
* 用户风险策略也称为有风险的帐户

## <a name="unblocking-suspicious-sign-ins"></a>取消阻止可疑登录

要取消阻止可疑登录，具有以下选项：

1. 从熟悉的位置或设备登录 - 已阻止可疑登录的常见原因是尝试从不熟悉的位置或设备登录。 用户可通过尝试从熟悉的位置或设备登录来快速确定这是否是阻止原因。
2. **从策略中排除** - 如果认为登录策略的当前配置导致特定用户出现问题，可从中排除这些用户。 有关详细信息，请参阅 [Azure Active Directory 标识保护](../active-directory-identityprotection.md)。
3. **禁用** - 如果认为策略配置导致所有用户出现问题，可禁用该策略。 有关详细信息，请参阅 [Azure Active Directory 标识保护](../active-directory-identityprotection.md)。

## <a name="unblocking-accounts-at-risk"></a>取消阻止有风险的帐户

要取消阻止有风险的帐户，具有以下选项：

1. **重置密码** - 可以重置用户的密码。 
2. **消除所有风险检测**-如果已达到配置的用户风险级别用于阻止访问, 则用户风险策略会阻止用户。 可以通过手动关闭报告的风险检测来减少用户的风险级别。 
3. **从策略中排除** - 如果认为登录策略的当前配置导致特定用户出现问题，可从中排除这些用户。 有关详细信息，请参阅 [Azure Active Directory 标识保护](../active-directory-identityprotection.md)。
4. **禁用** - 如果认为策略配置导致所有用户出现问题，可禁用该策略。 有关详细信息，请参阅 [Azure Active Directory 标识保护](../active-directory-identityprotection.md)。

## <a name="next-steps"></a>后续步骤
 
是否要了解有关 Azure AD 标识保护的详细信息？ 请查看 [Azure Active Directory 标识保护](../active-directory-identityprotection.md)。
