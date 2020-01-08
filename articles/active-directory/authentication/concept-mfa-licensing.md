---
title: Azure MFA 版本和使用计划 - Azure Active Directory
description: 有关多重身份验证客户端以及可用的方法和版本的信息。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1ee55dd3aebca869da47bbc994f546aa4fe528
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66496767"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>如何获取 Azure 多重身份验证

如果想要保护帐户，应该在组织中标配双重验证。 对资源拥有访问特权的帐户尤其需要此功能。 为此，Microsoft 为 Office 365 和 Azure Active Directory (Azure AD) 管理员提供了基本的双重验证功能，无需额外费用。 如果想要升级管理员的功能，或者将双重验证扩展到其他用户，可以用多种方式购买 Azure 多重身份验证。

> [!IMPORTANT]
> 本文旨在指导用户如何以不同的方式购买 Azure 多重身份验证。 有关定价和计费的具体详细信息，请始终参阅[多重身份验证定价页](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>可用的 Azure 多重身份验证版本

下表介绍了多重身份验证的三个版本之间的差别：

| Version | 描述 |
| --- | --- |
| 适用于 Office 365 的多重身份验证 <br> Microsoft 365 商业版 | 从 Office 365 或 Microsoft 365 门户管理此版本。 管理员可以[使用双重验证来保护 Office 365 资源](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)。 此版本是 Office 365 或 Microsoft 365 商业版订阅的一部分。 |
| 面向 Azure AD 管理员的多重身份验证 | Azure AD 租户中被分配了 Azure AD 全局管理员角色的用户可以免费启用双重验证。 |
| Azure 多重身份验证 | Azure 多重身份验证（通常称为“完整”版本）提供了最丰富的功能集。 它通过 [Azure 门户](https://portal.azure.com)、高级报告及支持一系列本地和云应用程序来提供其他配置选项。 Azure 多重身份验证是一项功能[Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features)。 |

> [!NOTE]
> 自 2018 年 9 月 1 日起，新客户无法再将 Azure 多重身份验证作为独立产品进行购买。 多重身份验证将继续以 Azure AD Premium 许可证中的功能的形式提供。

## <a name="feature-comparison-of-versions"></a>版本功能比较

下表提供了 Azure 多重身份验证的各个版本中可用的功能列表。

> [!NOTE]
> 此比较表讨论了每个版本的多重身份验证的部分功能。 如果拥有完整的 Azure 多重身份验证服务，某些功能可能不可用，具体取决于是否在云中使用 [MFA 或本地 MFA](concept-mfa-whichversion.md)。
>

| Feature | 适用于 Office 365 的多重身份验证 | 面向 Azure AD 管理员的多重身份验证 | Azure 多重身份验证 |
| --- |:---:|:---:|:---:|
| 使用 MFA 保护 Azure AD 管理员帐户 |● |●（仅适用于 Azure AD 全局管理员帐户） |● |
| 将移动应用用作第二个因素 |● |● |● |
| 将电话呼叫用作第二个因素 |● |● |● |
| 将短信用作第二个因素 |● |● |● |
| 不支持 MFA 的客户端的应用密码 |● |● |● |
| 管理员控制验证方法 |● |● |● |
| 使用 MFA 保护非管理员帐户 |● | |● |
| PIN 模式 | | |● |
| 欺诈警报 | | |● |
| MFA 报告 | | |● |
| 一次性跳过 | | |● |
| 通话的自定义问候语 | | |● |
| 通话的自定义呼叫方 ID | | |● |
| 受信任的 IP | | |● |
| 记住受信任的设备的 MFA |● |● |● |
| 适用于本地应用程序的 MFA | | |● |

> [!IMPORTANT]
> 开始在 2019 年 3 月中的电话呼叫选项将不能向免费/试用 Azure AD 租户中用户的 MFA 和 SSPR 的用户。 此更改不会影响短信。 电话呼叫将继续可供用户在付费 Azure AD 租户。 此更改只会影响免费/试用 Azure AD 租户。

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>如何为 Azure AD 管理员启用 Azure 多重身份验证

Azure AD 租户中被分配了全局管理员角色的用户可以免费为其 Azure AD 全局管理员帐户启用双重验证。 如果使用的是 Microsoft 帐户，则可以使用 Microsoft 帐户支持文章[关于双重验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)中的指南注册多重身份验证。 如果未使用 Microsoft 帐户，请使用文章[如何对用户或组要求双重验证](howto-mfa-userstates.md)中的指南为全局管理员启用多重身份验证。

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>如何购买 Azure 多重身份验证

购买包含 Azure 多重身份验证，如 Azure Active Directory 高级版或一个许可证包，包括 Azure AD Premium 或条件性访问，并将其分配给 Azure Active Directory 中的用户的许可证。

### <a name="consumption-based-licensing"></a>基于消耗的许可

基于消耗的许可不再可供新客户有效 2018 年 9 月 1 日。

可能无法再创建有效 2018 年 9 月 1 日新身份验证提供程序。 可以继续使用和更新现有的身份验证提供程序。 多重身份验证将继续成为 Azure AD Premium 许可证中的可用功能。

使用 Azure 多重身份验证提供程序时，有两种使用模式（通过 Azure 订阅计费）可用：

1. **按启用的用户** - 适用于想要为人数固定、需要定期进行身份验证的员工启用双重验证的企业。 按用户计费基于 Azure AD 租户和 Azure MFA 服务器中启用了 MFA 的用户数。 如果同时在 Azure AD 和 Azure MFA 服务器中为用户启用 MFA 并启用域同步 (Azure AD Connect)，我们会根据更多的用户计费。 如果未启用域同步，我们会根据 Azure AD 和 Azure MFA 服务器中启用 MFA 的所有用户总人数计费。 费用按比例计算，每日在商务系统中报告。

   > [!NOTE]
   > 计费示例 1：今天你为 5,000 个用户启用了 MFA。 MFA 系统将该数字除以 31，报告当日有 161.29 个用户产生了费用。 明天要为另外 15 个用户启用 MFA，到时 MFA 系统将报告该日期有 161.77 个用户产生费用。 在计费周期结束时，根据 Azure 订阅计费的用户总数累计为大约 5,000。
   >
   > 计费示例 2：某些用户持有许可证，而有些用户则没有，因此，使用了按用户计费的 Azure MFA 提供程序来弥补差异。 租户中有 4,500 个企业移动性 + 安全性许可证，但有 5,000 个用户启用了 MFA。 在这种情况下，将按比例针对 500 个用户计收 Azure 订阅费用，每日报告 16.13 个用户的费用。
   >

1. **按身份验证** - 适用于想要为不定期需要身份验证的大量用户启用双重验证的企业。 费用会根据双重验证请求数计算，不管这些验证是成功还是被拒绝。 此笔费用以 10 次身份验证为一组显示在 Azure 用量结算单中，并且每日报告。

   > [!NOTE]
   > 计费示例 3：Azure MFA 服务今天收到了 3,105 个双重验证请求。 将按 310.5 次身份验证计收 Azure 订阅费用。
   >

请务必注意，即使有许可证，也仍要基于使用量支付配置费用。 如果设置了按身份验证计费的 Azure MFA 提供程序，需要支付每次双重验证请求的费用，即使这些请求是持有许可证的用户发出的。 如果在未链接到 Azure AD 租户的域中设置了按用户计费的 Azure MFA 提供程序，则需要为每个启用 MFA 的用户付费，即使这些用户在 Azure AD 中持有许可证。

## <a name="next-steps"></a>后续步骤

- 有关定价详细信息，请参阅 [Azure MFA 定价](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)。

- 选择是要将 Azure MFA 部署[在云中还是本地](concept-mfa-whichversion.md)
