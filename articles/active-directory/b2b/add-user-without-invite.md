---
title: 添加而无需邀请链接或电子邮件-Azure Active Directory B2B 来宾 |Microsoft Docs
description: 可允许来宾用户将其他来宾用户添加到 Azure AD，而无需在 Azure Active Directory B2B 协作中兑换邀请。
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81aad3ef9a4a53532d19fdb81bc48fc50931d49c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056061"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>添加 B2B 协作来宾用户，而无需邀请链接或电子邮件

现在可以通过发送指向共享应用的直接链接来邀请来宾用户。 在使用此方法时，除非在某些特殊情况下，否则来宾用户不再需要使用邀请电子邮件。 来宾用户单击应用链接、查看并接受隐私条款，然后无缝访问应用。 有关详细信息，请参阅 [B2B 协作邀请兑换](redemption-experience.md)。   

在这一新方法可用前，无需邀请电子邮件也可邀请来宾用户，方法是将邀请者（从组织或从合作伙伴组织）添加到“来宾邀请者”  目录角色，然后让邀请者通过 UI 或 PowerShell 将来宾用户添加到目录、组或应用程序。 （如果使用 PowerShell，可取消邀请电子邮件）。 例如：

1. 主办组织（如 WoodGrove）中的用户邀请合作伙伴组织中的一名用户（如 Sam@litware.com）作为来宾。
2. 主办组织的管理员[设置相关策略](delegate-invitations.md)，允许 Sam 标识和添加合作伙伴组织 (Litware) 中的其他用户。 （必须将 Sam 添加到“来宾邀请者”  角色。）
3. 现在，Sam 无需兑换邀请即可将 Litware 中的其他用户添加到 WoodGrove 目录、组或应用程序。 如果 Sam 在 Litware 中具有相应的枚举特权，则会自动执行此操作。
 
此原始方法仍然适用。 但是，在行为上略有不同。 如果使用 PowerShell，你会注意到，受邀请的来宾帐户现在具有 PendingAcceptance  状态，而非立即显示“已接受”  。 尽管状态为挂起，来宾用户仍然可以登录并访问应用，无需单击电子邮件邀请链接。 挂起状态意味着用户尚未经历[同意体验](redemption-experience.md#consent-experience-for-the-guest)（他们在此处接受邀请组织的隐私条款）。 来宾用户在第一次登录时将看到此同意屏幕。 

如果邀请用户加入该目录，来宾用户必须直接访问特定于资源租户的 Azure 门户 URL（例如 https://portal.azure.com/*resourcetenant*.onmicrosoft.com），以查看并同意隐私条款。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [B2B 协作邀请兑换](redemption-experience.md)
- [委托 Azure Active Directory B2B 协作邀请](delegate-invitations.md)
- [信息工作者如何添加 B2B 协作用户？](add-users-information-worker.md)

