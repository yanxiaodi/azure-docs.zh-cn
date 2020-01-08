---
title: 如何在 Azure Active Directory Identity Protection 中关闭活动风险检测Microsoft Docs
description: 了解已关闭活动风险检测的选项。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d24ec94d0381fc2e79fdef97b6d525b7cec33fef
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126471"
---
# <a name="how-to-close-active-risk-detections"></a>如何：关闭活动风险检测

在出现[风险检测](../reports-monitoring/concept-risk-events.md)时, Azure Active Directory 检测可能已泄露的用户帐户的指示器。 作为管理员, 你希望将所有风险检测都关闭, 以使受影响的用户不再有风险。

本文概述了用于关闭活动风险检测的其他选项。

## <a name="options-to-close-risk-detections"></a>用于关闭风险检测的选项 

风险检测的状态为 "**活动**" 或 "**已关闭**"。 所有活动风险检测都有助于计算名为 "用户风险级别" 的值。 用户风险级别是反映帐户泄露可能性的一个指标（低、中、高）。 

若要关闭活动风险检测, 可选择以下选项:

- 要求使用用户风险策略进行密码重置
- 手动重置密码
- 消除所有风险检测 
- 手动关闭各个风险检测

## <a name="require-password-reset-with-a-user-risk-policy"></a>要求使用用户风险策略进行密码重置

通过配置[用户风险条件访问策略](howto-user-risk-policy.md)，可以要求在自动检测到指定的用户风险级别时更改密码。 

![重新设置密码](./media/howto-close-active-risk-events/13.png)

密码重置会关闭相关用户的所有活动风险事件，并使标识恢复安全状态。 使用用户风险策略是关闭活动风险检测的首选方法, 因为此方法是自动进行的。 受影响的用户与支持人员或管理员之间不需要交互。

但是，不一定总适合使用用户风险策略。 例如，对于以下情况，用户风险策略就不适用：

- 用户尚未注册多重身份验证 (MFA)。
- 具有已删除的活动风险检测的用户。
- 表明合法用户已执行报告的风险检测的调查。

## <a name="manual-password-reset"></a>手动重置密码

如果要求使用用户风险策略重置密码不是选项, 则可以通过手动重置密码来获取已关闭用户的所有风险检测。

![重新设置密码](./media/howto-close-active-risk-events/04.png)

相关对话框中提供了重置密码的两种不同方法：

![重新设置密码](./media/howto-close-active-risk-events/05.png)

**生成临时密码** - 生成临时密码可以立即让标识恢复安全状态。 此方法需要与受影响的用户交互，因为这些用户需要知道临时密码。 例如，可将新的临时密码发送到用户的备用电子邮件地址或用户的经理。 由于该密码是临时性的，因此系统会在用户下次登录时提示更改密码。

**要求用户重置密码** - 要求用户重置密码可以实现自助恢复，而无需联系支持人员或管理员。 与用户风险策略一样，此方法仅适用于已注册 MFA 的用户。 对于尚未注册 MFA 的用户，此选项不可用。

## <a name="dismiss-all-risk-detections"></a>消除所有风险检测

如果不能使用密码重置, 还可以消除所有风险检测。 

![重新设置密码](./media/howto-close-active-risk-events/03.png)

单击“清除所有事件”时，所有事件都会关闭，受影响的用户不再有风险。 但是，此方法不会对现有密码产生影响，因为它不能将相关的标识恢复安全状态。 此方法的首选用例是具有活动风险检测的已删除用户。 

## <a name="close-individual-risk-detections-manually"></a>手动关闭各个风险检测

您可以手动关闭各个风险检测。 通过手动关闭风险检测, 可以降低用户风险级别。 通常, 会手动关闭风险检测以响应相关调查。 例如, 当与用户交谈时, 会发现不再需要有效的风险检测。 
 
手动关闭风险检测时, 可以选择执行以下任一操作来更改风险检测的状态:

![个操作](./media/howto-close-active-risk-events/06.png)

- **解决**-如果在调查风险检测后, 你在 Identity Protection 之外采取了适当的补救措施, 并且你认为应将风险检测视为已关闭, 请将事件标记为已解决。 已解决事件会将风险检测的状态设置为 "已关闭", 并且风险检测将不再导致用户风险。
- **标记为 "假**"-在某些情况下, 您可以调查风险检测并发现它被错误地标记为危险。 可以通过将风险检测标记为误报来帮助减少此类出现的次数。 这可以帮助机器学习算法将来改善类似事件的分类。 误报事件的状态为“已关闭”，不再算作用户风险。
- **忽略**-如果尚未执行任何更正操作, 但希望从活动列表中删除风险检测, 则可以将风险检测标记为 "忽略", 事件状态将为 "已关闭"。 忽略的事件不算作用户风险。 这种做法只能在非常情况下使用。
- **重新激活**-可以重新激活手动关闭的风险检测 (通过选择 "解决"、"假正" 或 "忽略"), 并将事件状态设置回活动状态。 重新激活的风险检测会对用户风险级别计算产生影响。 无法重新激活通过修正 (如安全密码重置) 关闭的风险检测。

## <a name="next-steps"></a>后续步骤

若要获取“Azure AD 标识保护”的概述，请参阅 [Azure AD 标识保护概述](overview.md)。
