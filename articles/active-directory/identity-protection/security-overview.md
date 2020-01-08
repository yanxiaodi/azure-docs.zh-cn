---
title: Azure Active Directory (Azure AD) 标识保护安全概览 | Microsoft Docs
description: 了解如何通过“安全概览”深入了解组织的安全状态。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b894f7020083dd6ca46c394ec2930a3da36bc76d
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335201"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory 标识保护 - 安全概览

通过[安全概览](https://aka.ms/IdentityProtectionRefresh)，可以深入了解组织的安全状态。 它有助于发现潜在攻击，并了解策略有效性。

“安全概览”大致分为以下两部分：

- 趋势：位于左侧，呈现组织的风险时间线。
- 磁贴：位于右侧，突出显示组织中正在发生的关键问题，并建议如何快速采取措施。

![安全概述](./media/security-overview/01.png)
  
## <a name="trends"></a>趋势

### <a name="new-risky-users-detected"></a>检测到新的有风险用户

此图表显示在选定时间段内检测到的新的有风险用户数。 可以按用户风险级别（低、中、高）筛选此图表视图。 将鼠标悬停在 UTC 日期增量之上可以查看当天检测到的有风险用户数。 单击此图表会转到“有风险用户”报告。 若要修正有风险用户，不妨更改这些用户的密码。

### <a name="new-risky-sign-ins-detected"></a>检测到新的有风险登录

此图表显示在选定时间段内检测到的有风险登录数。 可以按登录风险类型（实时或聚合）和登录风险级别（低、中、高）筛选此图表视图。 未保护的登录是指未接受 MFA 质询的成功实时风险登录。 （注意：无法使用登录风险策略为脱机检测到有风险的登录提供实时保护）。 将鼠标悬停在 UTC 日期增量之上可以查看当天检测到的有风险登录数。 单击此图表会转到“有风险登录”报告。

## <a name="tiles"></a>平铺
 
### <a name="high-risk-users"></a>高风险用户

“高风险用户”磁贴显示标识遭入侵可能性高的用户的最新计数。 应最优先调查这些用户。 单击“高风险用户”磁贴会重定向到“有风险用户”报告的筛选后视图，其中只显示高风险级别用户。 使用此报告，可以了解详细信息，并通过重置密码来修正这些用户。

![安全概述](./media/security-overview/02.png)

### <a name="medium-risk-users"></a>中等风险用户
“中等风险用户”磁贴显示标识遭入侵可能性中等的用户的最新计数。 单击“中等风险用户”磁贴会重定向到“有风险用户”报告的筛选后视图，其中只显示中等风险级别用户。 使用此报告，可以进一步调查，并修正这些用户。

### <a name="unprotected-risky-sign-ins"></a>未保护的有风险登录

“未保护的有风险登录”磁贴显示上周成功实时有风险登录数，这些登录既没有遭阻止，也没有受到条件访问策略、标识保护风险策略或每用户 MFA 的 MFA 质询。 这些是成功但没有接受 MFA 质询的潜在遭入侵登录。 为了今后保护此类登录，请应用登录风险策略。 单击“未保护的有风险登录”磁贴会重定向到“登录风险策略配置”边栏选项卡，在其中可以将登录风险策略配置为，必须对指定风险级别的登录进行 MFA。

### <a name="legacy-authentication"></a>旧身份验证

“旧身份验证”磁贴显示上周组织中的旧身份验证数。 旧身份验证协议不支持 MFA 等新式安全方法。 为了防止使用旧身份验证，可以应用条件访问策略。 单击“旧身份验证”磁贴会重定向到“标识安全功能分数”。

### <a name="identity-secure-score"></a>标识安全功能分数

“标识安全功能分数”衡量并比较你的安全状态与行业模式。 单击“标识安全功能分数(预览版)”磁贴会重定向到“标识安全功能分数(预览版)”边栏选项卡，在其中可以详细了解如何提升安全状态。

## <a name="next-steps"></a>后续步骤

- [第 9 频道：Azure AD 和标识展示：“标识保护”预览版](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [启用 Azure Active Directory 标识保护](enable.md)
