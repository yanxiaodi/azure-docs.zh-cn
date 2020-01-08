---
title: 什么是 Azure Active Directory B2B 协作？ - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 协作支持来宾用户访问权限，以便安全地与外部合作伙伴共享资源和协作。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 09/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62bc550a809d596b5378c03aa95877db5e15febb
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019510"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>什么是 Azure Active Directory B2B 中的来宾用户访问权限？

使用 Azure Active Directory (Azure AD) 企业到企业 (B2B) 协作可以安全地将公司的应用程序和服务与来自任何其他组织的来宾用户共享，同时保持对自己公司数据的控制。 与外部合作伙伴安全放心地合作，不论其规模是大是小，甚至就算他们没有 Azure AD 或 IT 部门也无妨。 合作伙伴通过一个简单的邀请和兑换过程即可使用自己的凭据来访问公司资源。 开发人员可以使用 Azure AD 企业到企业 API 自定义邀请处理或编写自助注册门户之类的应用程序。

请观看视频，了解如何邀请来宾用户使用他们自己的标识登录公司的应用和服务以安全地与之协作。

以下视频提供了有用的概述。

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="collaborate-with-any-partner-using-their-identities"></a>与使用自己标识的任何合作伙伴协作
借助 Azure AD B2B，合作伙伴可使用自己的标识管理解决方案，因此组织省去了外部管理开销。 
- 合作伙伴使用自己的标识和凭据；Azure AD 不是必需的。 
- 不需要管理外部帐户或密码。 
- 不需要同步帐户或管理帐户生命周期。  

![显示“添加成员”页的屏幕截图](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>通过一个简单的邀请和兑换过程邀请来宾用户
来宾用户可使用自己的工作、学校或社交标识登录应用和服务。 如果来宾用户没有 Microsoft 帐户或和 Azure AD 帐户，当他们在兑换邀请时，系统会为他们创建一个帐户。 
- 邀请使用自选电子邮件标识的来宾用户。
- 发送应用的直接链接，或发送邀请至来宾用户自己的访问面板。 
- 来宾用户遵循一些简单的兑换步骤登录。

![显示“查看权限”页的屏幕截图](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>使用策略安全地共享你的应用和服务
可以使用授权策略保护企业内容。 可在以下级别强制执行多重身份验证等条件访问策略：
- 租户级别。
- 应用程序级别。
- 针对特定来宾用户，保护企业应用和数据。

![显示“条件访问”选项的屏幕截图](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>在 Azure AD 门户中轻松添加来宾用户

管理员可以在 Azure 门户中轻松地向组织添加来宾用户。
- 在 Azure AD 中创建新的来宾用户，方法类似于添加新用户。
- 来宾用户会立即收到允许他们登录访问面板的可自定义邀请。
- 目录中的来宾用户会被分配到应用或组。  

![显示“新建来宾用户邀请”入口页的屏幕截图](media/what-is-b2b/adding-b2b-users-admin.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>让应用程序和组所有者管理自己的来宾用户

可以委托应用程序所有者管理来宾用户，不论是否为 Microsoft 应用程序，他们都可以将来宾用户直接添加到他们想要共享的任何应用程序。 
 - 管理员设置自助服务应用和组管理。
 - 非管理员使用其[访问面板](https://myapps.microsoft.com)将来宾用户添加到应用程序或组。

![显示来宾用户的访问面板的屏幕截图](media/what-is-b2b/access-panel-manage-app.png)

## <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>使用 API 和示例代码轻松生成要载入的应用程序

使用按组织需求自定义的方法引入外部合作伙伴。
- 使用 [B2B 协作邀请 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)，可自定义载入体验，包括创建自助服务注册门户。 
- 使用我们[在 GitHub 上](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web)为自助服务门户提供的示例代码。

![显示示例注册门户的屏幕截图](media/what-is-b2b/sign-up-portal.png)

## <a name="next-steps"></a>后续步骤

- [Azure AD B2B 协作的许可指南](licensing-guidance.md)
- [在门户中添加 B2B 协作来宾用户](add-users-administrator.md)
- [了解邀请兑换过程](redemption-experience.md)
- 此外，还可与往常一样通过 [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)（Microsoft 技术社区）与产品团队联系，获取任何反馈、讨论和建议。
