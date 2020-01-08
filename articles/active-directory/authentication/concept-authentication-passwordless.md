---
title: Azure Active Directory 无密码登录（预览版）
description: 无密码使用 FIDO2 安全密钥或 Microsoft Authenticator 应用登录到 Azure AD （预览）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc633780d8b816d8fc2e313bb1955a5719979efe
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240864"
---
# <a name="what-is-passwordless"></a>什么是无密码？

多重身份验证（MFA）是保护组织安全的一种好方法，但用户在必须记住其密码的情况下就会遇到其他层的失望。 无密码身份验证方法更为方便，因为密码会被删除，并将其替换为你具有的内容以及你知道的内容。

|   | 你拥有的东西 | 你或知道的内容 |
| --- | --- | --- |
| 无密码 | 电话号码或安全密钥 | 生物识别或 PIN |

当涉及身份验证时，每个组织都有不同的需求。 Microsoft 目前为 windows 电脑提供 Windows Hello。 我们正在将 Microsoft Authenticator 应用和 FIDO2 安全密钥添加到无密码系列。

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator 应用

允许员工的电话成为无密码的身份验证方法。 除密码外，你可能已使用 Microsoft Authenticator 应用作为便利的多重身份验证选项。 但现在可以使用无密码选项。

![通过 Microsoft Authenticator 应用登录 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

它通过允许用户登录到任何平台或浏览器来登录到任何平台或浏览器，将任何 iOS 或 Android 手机变成强的无密码凭据，将屏幕上显示的数字与电话上的一个数字匹配，然后使用其生物识别（触摸或面部）或锁定以确认。

## <a name="fido2-security-keys"></a>FIDO2 安全密钥

FIDO2 安全密钥是基于 unphishable 标准的无密码身份验证方法，可采用任何形式。 Fast Identity Online （FIDO）是无密码 authentication 的开放标准。 它可让用户和组织利用标准登录到其资源，而无需使用外部安全密钥或设备内置的平台密钥。

对于公共预览版，员工可以使用外部安全密钥登录到其 Azure Active Directory 加入 Windows 10 计算机（运行版本1809或更高版本），并对其云资源进行单一登录。 他们还可以登录到受支持的浏览器。

![使用安全密钥登录 Microsoft Edge](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

尽管有很多密钥是通过 FIDO 联盟 FIDO2 认证的，但 Microsoft 需要由供应商实现的 FIDO2 CTAP 规范的一些可选扩展，以确保最高的安全性和最佳体验。

安全密钥**必须**实现 FIDO2 CTAP 协议中的以下功能和扩展，才能与 Microsoft 兼容：

| # | 功能/扩展信任 | 为什么需要此功能或扩展？ |
| --- | --- | --- |
| 1 | 居民密钥 | 此功能使安全密钥可移植，其中的凭据存储在安全密钥上。 |
| 2 | 客户端 pin | 利用此功能，你可以使用另一个因素来保护凭据，并将其应用于没有用户界面的安全密钥。 |
| 3 | hmac-secret | 此扩展可确保你可以在设备处于脱机状态或处于飞行模式时登录到你的设备。 |
| 4 | 每个 RP 多个帐户 | 此功能可确保你可以在多个服务（如 Microsoft 帐户和 Azure Active Directory）上使用相同的安全密钥。 |

以下提供商提供了 FIDO2 安全密钥，它们具有已知兼容 paswordless 体验的不同形式因素。 Microsoft 鼓励客户通过联系供应商和 FIDO 联盟来评估这些密钥的安全属性。

| 提供商 | 联系人 |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

如果你是供应商，并且想要获取此列表中的设备， [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)请联系。

对于安全敏感的企业而言，FIDO2 安全密钥是一个不错的选择，或者不愿意或无法使用其电话作为第二个因素的方案或员工。

## <a name="what-scenarios-work-with-the-preview"></a>使用预览版的情况如何？

- 管理员可以为其租户启用无密码 authentication 方法
- 对于每个方法，管理员可面向所有用户或选择其租户中的用户/组
- 最终用户可以在其帐户门户中注册和管理这些无密码 authentication 方法
- 最终用户可以用这些无密码身份验证方法登录
   - Microsoft Authenticator 应用：在使用 Azure AD 身份验证的情况下（包括所有浏览器、在 Windows 10 开箱（OOBE）安装过程中，以及在任何操作系统上集成的移动应用），都适用。
   - 安全密钥：适用于 Windows 10 版本1809或更高版本的锁屏界面，以及受支持的浏览器（如 Microsoft Edge）中的网页。

## <a name="next-steps"></a>后续步骤

[在组织中启用 FIDO2 security key passwordlesss 选项](howto-authentication-passwordless-security-key.md)

[在组织中启用基于手机的无密码选项](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>外部链接

[FIDO 联盟](https://fidoalliance.org/)

[FIDO2 CTAP 规范](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
