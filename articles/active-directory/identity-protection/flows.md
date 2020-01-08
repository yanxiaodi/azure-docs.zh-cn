---
title: Azure AD 标识保护中的登录体验 | Microsoft Docs
description: 概述了当“标识保护”已缓解或补救某个用户问题时或当某个策略要求使用多重身份验证时的用户体验。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e513027eed44ec7649f41f8786882aed8511bc6
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335495"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Azure AD 标识保护中的登录体验

使用 Azure Active Directory 标识保护时，可以：

* 要求用户注册多重身份验证
* 处理有风险的登录以及用户遭到入侵的问题

系统对这些问题做出的响应会影响到用户的登录体验，因为此时已不再能够直接使用用户名和密码登录。 需要采取其他措施才能使用户安全地恢复正常工作。

本文概述在各种可能情况下的用户登录体验。

**多重身份验证**

* 多重身份验证注册

**有风险的登录**

* 有风险的登录恢复
* 有风险的登录已阻止
* 在发生有风险的登录期间注册多重身份验证

**有风险的用户**

* 遭到入侵的帐户恢复
* 遭到入侵的帐户已阻止

## <a name="multi-factor-authentication-registration"></a>多重身份验证注册
在实施遭到入侵的帐户恢复流程和有风险的登录流程时，最佳用户体验就是用户能够自行恢复。 如果用户已注册多重身份验证，则其帐户已有一个关联的电话号码，可用于通过安全质询。 帐户遭到入侵后，无需技术支持人员或管理员的介入即可恢复正常。 因此，强烈建议要求用户注册多重身份验证。 

管理员可设置一个策略，要求用户为其帐户设置附加安全验证。 此策略允许用户最多在 14 天内跳过多重身份验证注册。 这 14 天的宽限期是不可配置的。

**多重身份验证注册包括三个步骤：**

1. 第一个步骤是通知用户必须为其帐户设置多重身份验证。 
   
    ![补救](./media/flows/140.png "补救")
2. 要设置多重身份验证，需要告知系统如何与你取得联系。
   
    ![补救](./media/flows/141.png "补救")
3. 系统会向你提交质询，而需要做出回复。
   
    ![补救](./media/flows/142.png "补救")

## <a name="risky-sign-in-recovery"></a>有风险的登录恢复
管理员针对登录风险配置策略后，受影响的用户在尝试登录时会看到通知。 

**有风险的登录流程包括两个步骤：** 

1. 通知用户在其登录时检测到异常情况，例如从新的位置、设备或应用登录。 
   
    ![补救](./media/flows/120.png "补救")
2. 用户需要通过解决安全质询来证明其身份。 如果用户注册了多重身份验证，则需要请求将一个安全代码发送到其手机号码，然后填入该代码。 由于这只是一次有风险的登录，而不是帐户遭到入侵，因此不需要在流程中更改密码。 
   
    ![补救](./media/flows/121.png "补救")

## <a name="risky-sign-in-blocked"></a>有风险的登录已阻止
管理员还可以选择设置一个登录风险策略，用于根据风险级别阻止用户登录。 若要解除阻止，最终用户必须联系管理员或技术服务人员，或者尝试从惯用的位置或设备登录。 在此情况下，无法通过解决多重身份验证质询来自行恢复。

![补救](./media/flows/200.png "补救")

## <a name="compromised-account-recovery"></a>遭到入侵的帐户恢复
配置用户风险安全策略后，符合该策略中指定的用户风险级别的用户（因而也假设这些用户已遭到入侵）必须完成用户入侵恢复流程才能登录。 

**用户入侵恢复流程包括三个步骤：**

1. 通知用户，由于出现可疑活动或凭据泄露，其帐户的安全性面临风险。
   
    ![补救](./media/flows/101.png "补救")
2. 用户需要通过解决安全质询来证明其身份。 如果用户已注册多重身份验证，则可以在遭到入侵后自行恢复。 他们需要请求将一个安全代码发送到其手机号码，然后填入该代码。 
   
   ![补救](./media/flows/110.png "补救")
3. 最后，用户必须更改其密码，因为其他人可能已获取了其帐户的访问权限。 
   下面是此体验的屏幕截图。
   
   ![补救](./media/flows/111.png "补救")

## <a name="compromised-account-blocked"></a>遭到入侵的帐户已阻止
若要解除阻止某个已被用户风险安全策略阻止的用户，该用户必须与管理员或技术支持人员联系。 在此情况下，无法通过解决多重身份验证质询来自行恢复。

![补救](./media/flows/104.png "补救")

## <a name="reset-password"></a>重新设置密码
如果遭到入侵的用户已被阻止登录，管理员可以为其生成临时密码。 在下一次登录期间，用户必须更改其密码。

![补救](./media/flows/160.png "补救")

## <a name="see-also"></a>请参阅

* [Azure Active Directory 标识保护](../active-directory-identityprotection.md) 
