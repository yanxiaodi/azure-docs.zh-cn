---
title: 使用访问评审管理来宾访问-Azure Active Directory |Microsoft Docs
description: 使用 Azure Active Directory 访问评审管理属于组的成员或已分配到应用程序的来宾用户
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/13/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a982b97bcef6a24f026bf6ad2943661db18d6af0
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499465"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>使用 Azure AD 访问评审管理来宾访问权限


使用 Azure Active Directory (Azure AD)，可以借助 [Azure AD B2B 功能](../b2b/what-is-b2b.md)轻松实现跨组织边界的协作。 [管理员](../b2b/add-users-administrator.md)或[其他用户](../b2b/what-is-b2b.md)可以邀请其他租户中的来宾用户。 此功能也适用于 Microsoft 帐户等社交标识。

可以轻松确保来宾用户拥有适当的访问权限。 为此，可让来宾本人或决策人参与访问评审，鉴定（或“证明”）来宾的访问权限。 审阅者可基于 Azure AD 的建议，针对每个用户就继续访问的需求提供意见。 访问评审完成后，即可进行更改，并删除不再需要访问权限的来宾的访问权限。

> [!NOTE]
> 本文档重点介绍如何评审来宾用户的访问权限。 如果想要评审所有用户的访问权限，而不仅仅是来宾，请参阅[通过访问评审管理用户访问权限](manage-user-access-with-access-reviews.md)。 如果希望评审具有管理角色（如全局管理员）的用户成员身份，请参阅[在 Azure AD Privileged Identity Management 中启动访问评审](../privileged-identity-management/pim-how-to-start-security-review.md)。

## <a name="prerequisites"></a>系统必备

- Azure AD Premium P2

有关详细信息, 请参阅[哪些用户必须拥有许可证？](access-reviews-overview.md#which-users-must-have-licenses)。

## <a name="create-and-perform-an-access-review-for-guests"></a>创建和执行来宾的访问评审

首先, 作为全局管理员或用户管理员, 请访问 "[标识管理" 页](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), 以确保对你的组织的访问评审已准备就绪。

Azure AD 支持采用多种方案评审来宾用户。

你可以评审以下任何一项：

 - Azure AD 中具有一个或多个来宾成员的组。
 - 连接到 Azure AD（已为其分配一个或多个来宾用户）的应用程序。 

然后可以决定是要求每个来宾评审其自己的访问权限还是要求一个或多个用户评审每个来宾的访问权限。

 以下部分介绍了这些方案。

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>让来宾自我评审他们在组中的成员身份

可以使用访问评审确保受邀并已添加到组中的用户需要持续提供访问权限。 可以轻松让来宾自我评审他们在组中的成员身份。

1. 若要为组创建访问评审, 请选择仅包括来宾用户成员的评审, 并确保成员自行查看。 有关详细信息, 请参阅[创建组或应用程序的访问评审](create-access-review.md)。

2. 让每个来宾自我评审其成员身份。 默认情况下，接受邀请的每个来宾会收到来自 Azure AD 的电子邮件，其中包含访问评审的链接。 Azure AD 提供有关如何[查看对组或应用程序的访问权限](perform-access-review.md)的来宾的说明。

3. 审阅者提供输入后，将停止访问评审并应用更改。 有关详细信息, 请参阅[完成对组或应用程序的访问评审](complete-access-review.md)。

4. 除了表明自己不需要持续访问权限的这些用户以外，还可以删除未做出答复的用户。 未做出答复的用户可能不会再收到电子邮件。

5. 如果未将组用于访问管理，还可以删除未选择参与评审的用户，因为他们未接受邀请。 未接受邀请可能表示受邀用户的电子邮件地址拼写错误。 如果某个组被用作通讯组，则可能没有选择某些来宾用户让其参与，因为他们是联系人对象。

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>让发起人评审来宾在组中的成员身份

可以让发起人（例如组的所有者）评审来宾是否持续需要获得组中的成员身份。

1. 若要为组创建访问评审, 请选择仅包括来宾用户成员的评审。 然后指定一个或多个审阅者。 有关详细信息, 请参阅[创建组或应用程序的访问评审](create-access-review.md)。

2. 要求审阅者提供输入。 默认情况下, 他们会收到来自 Azure AD 的电子邮件, 其中包含访问面板的链接, 用户可以在其中[查看对组或应用程序的访问权限](perform-access-review.md)。

3. 审阅者提供输入后，将停止访问评审并应用更改。 有关详细信息, 请参阅[完成对组或应用程序的访问评审](complete-access-review.md)。

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>让来宾自我评审他们对应用程序的访问权限

可以使用访问评审确保受邀参与特定应用程序的用户需要持续提供访问权限。 可以轻松让来宾自己评审他们的访问权限需求。

1. 若要为应用程序创建访问评审, 请选择该评审以仅包括来宾, 并且用户查看其自己的访问权限。 有关详细信息, 请参阅[创建组或应用程序的访问评审](create-access-review.md)。

2. 让每个来宾自我评审他们对应用程序的访问权限。 默认情况下，接受邀请的每个来宾会收到来自 Azure AD 的电子邮件。 该电子邮件中包含指向组织访问面板中访问评审的链接。 Azure AD 提供有关如何[查看对组或应用程序的访问权限](perform-access-review.md)的来宾的说明。

3. 审阅者提供输入后，将停止访问评审并应用更改。 有关详细信息, 请参阅[完成对组或应用程序的访问评审](complete-access-review.md)。

4. 除了表明自己不需要持续访问权限的用户以外，还可以删除未做出答复的来宾用户。 未做出答复的用户可能不会再收到电子邮件。 还可以删除未选择让其参与的来宾用户，尤其是最近未邀请其参与的用户。 这些用户未接受其邀请，因此没有应用程序的访问权限。 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>让发起人评审来宾对应用程序的访问权限

可以让发起人（例如应用程序的所有者）评审来宾是否需要持续获得应用程序的访问权限。

1. 若要为应用程序创建访问评审, 请选择仅包括来宾的评审。 然后指定一个或多个用户作为审阅者。 有关详细信息, 请参阅[创建组或应用程序的访问评审](create-access-review.md)。

2. 要求审阅者提供输入。 默认情况下, 他们会收到来自 Azure AD 的电子邮件, 其中包含访问面板的链接, 用户可以在其中[查看对组或应用程序的访问权限](perform-access-review.md)。

3. 审阅者提供输入后，将停止访问评审并应用更改。 有关详细信息, 请参阅[完成对组或应用程序的访问评审](complete-access-review.md)。

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>让来宾评审访问权限的一般需求

在某些组织中，来宾可能不知道其组成员身份。

> [!NOTE]
> 早期版本的 Azure 门户不允许 UserType 为“来宾”的用户具有管理访问权限。 在某些情况下，目录中的管理员可能已使用 PowerShell 将来宾的 UserType 值更改为“成员”。 如果之前目录中已发生此更改，则之前的查询可能不包括以前具有管理访问权限的所有来宾用户。 在这种情况下，需要更改来宾的 UserType 或手动将来宾包含在组成员身份中。

1. 如果尚不存在适当的组，请在 Azure AD 中创建一个安全组并在其中以成员的形式包含来宾。 例如，可以创建一个组并在其中包含手动维护的来宾成员身份。 或者，可以针对 Contoso 租户中 UserType 属性值为“来宾”的用户，以类似于“Guests of Contoso”的名称创建一个动态组。  为了提高效率，请确保该组主要是来宾 - 不要选择包含成员用户的组，因为成员用户不需要进行评审。  另外，请记住，作为组成员的来宾用户可以看到该组的其他成员。

2. 若要为该组创建访问评审, 请选择要作为成员本身的审阅者。 有关详细信息, 请参阅[创建组或应用程序的访问评审](create-access-review.md)。

3. 让每个来宾自我评审其成员身份。 默认情况下，接受邀请的每个来宾会收到来自 Azure AD 的电子邮件，其中包含组织访问面板中访问评审的链接。 Azure AD 提供有关如何[查看对组或应用程序的访问权限](perform-access-review.md)的来宾的说明。  未接受其邀请的这些来宾将在评审结果中显示为“未通知”。

4. 审阅者提供输入后，将停止访问评审。 有关详细信息, 请参阅[完成对组或应用程序的访问评审](complete-access-review.md)。

5. 删除已拒绝评审、未完成评审或者事先未接受邀请的来宾的来宾访问权限。 如果某些来宾是已被选择参加评审或以前未接受邀请的联系人，则可以使用 Azure 门户或 PowerShell 禁用其帐户。 如果来宾不再需要访问权限并且不是联系人，则可以使用 Azure 门户或 PowerShell 从目录中删除其来宾用户对象。

## <a name="next-steps"></a>后续步骤

[创建组或应用程序的访问评审](create-access-review.md)







