---
title: 配置应用程序单一登录 |Microsoft Docs
description: 如何为正在使用 Azure AD 开发和注册的自定义应用程序配置单一登录。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc9f20903d65fbde161d26ed3e194e63d341ccb
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320958"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>如何为应用程序配置单一登录

通过适用于 OpenID Connect 的 Azure AD、SAML 2.0 或 WS-Fed 联合时，会自动启用应用中的联合单一登录 (SSO)。 如果与 Azure AD 已有一个现有会话，但是最终用户仍然需要登录，这有可能是应用配置错误。

* 如果使用 ADAL/MSAL，请确保将 **PromptBehavior** 设置为“自动”  而不是“始终”  。

* 如果生成移动应用，可能需要进行额外配置以启用中转或非中转 SSO。

对于 Android 系统，请参阅[在 Android 中启用跨应用 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)。<br>

对于 iOS 系统，请参阅[在 iOS 中启用跨应用 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)。

## <a name="next-steps"></a>后续步骤

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[在 Android 中启用跨应用 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[在 iOS 中启用跨应用 SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[将应用与 AzureAD 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[同意并为 AzureAD v2.0 聚合应用授予权限](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
