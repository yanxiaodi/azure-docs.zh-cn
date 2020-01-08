---
title: B2B 协作 API 和自定义-Azure Active Directory |Microsoft Docs
description: Azure Active Directory B2B 协作可让业务合作伙伴有选择性地访问本方的企业应用程序，为跨公司合作关系提供支持
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0369988bc6f6503f9940e6aabccb91ab843d63f5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65811871"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B 协作 API 和自定义

已经有许多客户和我们说他们想要以最适合其组织的方式自定义邀请过程。 使用我们的 API，便可以实现该想法。 [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>邀请 API 的功能

该 API 提供以下功能：

1. 使用*任何*电子邮件地址邀请外部用户。

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. 自定义希望用户在接受其邀请后登陆的位置。

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. 选择通过我们将包含可自定义消息的标准邀请邮件

    ```
    "sendInvitationMessage": true
    ```

   发送给收件人

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. 并选择你想要让其了解你邀请了此协作者的抄送人员。

5. 或者完全自定义邀请，并通过选择不通过 Azure AD 发送通知加入工作流。

    ```
    "sendInvitationMessage": false
    ```

   在这种情况下，将通过可嵌入电子邮件模板的 API、即时消息或所选择的其他分发方法收到兑换 URL。

6. 最后，如果是管理员，可以选择以成员身份邀请用户。

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>授权模型

可以在以下授权模式下运行 API：

### <a name="app--user-mode"></a>“应用 + 用户”模式

在此模式下，任何使用 API 的用户都需要有创建 B2B 邀请的权限。

### <a name="app-only-mode"></a>“仅应用”模式

在仅应用上下文中，应用需要 User.Invite.All 作用域才能使邀请成功。

有关详细信息，请参阅 https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

可使用 PowerShell 轻松地将外部用户添加并邀请到组织。 使用 cmdlet 创建邀请：

```powershell
New-AzureADMSInvitation
```

可以使用以下选项：

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>邀请状态

向外部用户发送邀请后，可使用 Get-AzureADUser cmdlet 查看是否用户已接受该邀请  。 向外部用户发送邀请时，将填充 Get-AzureADUser 的以下属性：

* **UserState** 指示邀请是“PendingAcceptance”还是“Accepted”   。
* **UserStateChangedOn** 显示 UserState 属性最新更改的时间戳  。

可使用“筛选器”选项按 UserState 筛选结果   。 以下示例显示了如何筛选结果以仅显示具有待处理邀请的用户。 该示例还显示了 Format-List 选项，借助该选项可以指定要显示的属性  。 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> 确保拥有最新版本的 AzureAD PowerShell 模块或 AzureADPreview PowerShell 模块。 

## <a name="see-also"></a>另请参阅

在 [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) 中查看邀请 API 参考。

## <a name="next-steps"></a>后续步骤

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [B2B 协作邀请电子邮件的元素](invitation-email-elements.md)
- [B2B 协作邀请兑换](redemption-experience.md)
- [在没有邀请的情况下添加 B2B 协作用户](add-user-without-invite.md)