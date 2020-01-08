---
title: Azure Active Directory Connect：无缝单一登录故障排除 | Microsoft Docs
description: 本主题介绍了如何排除 Azure Active Directory 无缝单一登录故障
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/24/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: abfdad1db655c102dbfb300434eac952fe2154dc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60381841"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>排除 Azure Active Directory 无缝单一登录故障

本文可帮助你找到有关 Azure Active Directory (Azure AD) 无缝单一登录（无缝 SSO）常见问题的故障排除信息。

## <a name="known-issues"></a>已知问题

- 在一些情况下，启用无缝 SSO 最多可能需要 30 分钟。
- 如果对租户禁用并重新启用无缝 SSO，则用户在其缓存的 Kerberos 票证（通常 10 小时有效）过期前，将不会获得单一登录体验。
- 不支持 Microsoft Edge 浏览器。
- 如果无缝 SSO 成功，用户将没有机会选择“使我保持登录状态”  。 由于此行为，[SharePoint 和 OneDrive 映射方案](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec)无法正常工作。
- 使用非交互式流支持版本为 16.0.8730.xxxx 及更高版本的 Office 365 Win32 客户端（Outlook、Word、Excel 等）。 不支持其他版本；在这些版本中，用户需输入用户名而不是密码登录。 对于 OneDrive，必须激活 [OneDrive 无提示配置功能](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894)才能获得无提示登录体验。
- 无缝 SSO 在 Firefox 的隐私浏览模式下不起作用。
- 开启增强保护模式时，无缝 SSO 在 Internet Explorer 中不起作用。
- 无缝 SSO 在 iOS 和 Android 的移动浏览器上不起作用。
- 如果某个用户属于 Active Directory 中过多的组，则该用户的 Kerberos 票证可能会太大而无法处理，这会导致无缝 SSO 失败。 Azure AD HTTPS 请求可以具有最大大小为 50 KB 的标头；Kerberos 票证需要远小于该限制，才能容纳其他 Azure AD 项目（通常 2 - 5 KB），比如 cookie。 我们的建议是减少用户的组成员身份，然后重试。
- 如果你要同步 30 个或更多的 Active Directory 林，则不能通过 Azure AD Connect 启用无缝 SSO。 作为一种解决方法，可以在租户中[手动启用](#manual-reset-of-the-feature)该功能。
- 将 Azure AD 服务 URL (https://autologon.microsoftazuread-sso.com) 添加到“受信任的站点”区域，而非会阻止用户登录的“本地 Intranet”区域  。
- 无缝 SSO 对 Kerberos 使用 **RC4_HMAC_MD5** 加密类型。 禁止在 Active Directory 设置中使用 **RC4_HMAC_MD5** 加密类型将中断无缝 SSO。 在“组策略管理编辑器”工具中，确保“计算机配置”->“Windows 设置”->“安全设置”->“本地策略”->“安全选项”->“网络安全：    配置 Kerberos 允许的加密类型”下 RC4_HMAC_MD5 的策略值为“已启用”。 此外，无缝 SSO 无法使用其他加密类型，因此请确保这些加密类型**已禁用**。

## <a name="check-status-of-feature"></a>检查功能状态

确保租户上的无缝 SSO 功能仍处于“已启用”状态  。 你可以通过转到 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)中的“Azure AD Connect”  窗格来检查状态。

![Azure Active Directory 管理中心：“Azure AD Connect”窗格](./media/tshoot-connect-sso/sso10.png)

点击浏览所有支持无缝 SSO 的 AD 林。

![Azure Active Directory 管理中心：无缝 SSO 窗格](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Azure Active Directory 管理中心登录失败原因（需要 Premium 许可证）

如果你的租户有与之关联的 Azure AD Premium 许可证，还可在 [Azure Active Directory 管理员中心](https://aad.portal.azure.com/)查看[登录活动报告](../reports-monitoring/concept-sign-ins.md)。

![Azure Active Directory 管理中心：登录报告](./media/tshoot-connect-sso/sso9.png)

浏览到 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)的“Azure Active Directory”   > “登录”  ，然后选择特定用户的登录活动。 查找“登录错误代码”  字段。 通过使用下表将该字段的值映射到某个失败原因和解决方法：

|登录错误代码|登录失败原因|解决方法
| --- | --- | ---
| 81001 | 用户的 Kerberos 票证太大。 | 减少用户的组成员身份，然后重试。
| 81002 | 无法验证用户的 Kerberos 票证。 | 请参阅[故障排除清单](#troubleshooting-checklist)。
| 81003 | 无法验证用户的 Kerberos 票证。 | 请参阅[故障排除清单](#troubleshooting-checklist)。
| 81004 | Kerberos 身份验证尝试失败。 | 请参阅[故障排除清单](#troubleshooting-checklist)。
| 81008 | 无法验证用户的 Kerberos 票证。 | 请参阅[故障排除清单](#troubleshooting-checklist)。
| 81009 | 无法验证用户的 Kerberos 票证。 | 请参阅[故障排除清单](#troubleshooting-checklist)。
| 81010 | 无缝 SSO 失败，因为用户的 Kerberos 票证已过期或无效。 | 用户需要从企业网络内部已加入域的设备登录。
| 81011 | 根据用户的 Kerberos 票证中的信息，找不到用户对象。 | 使用 Azure AD Connect 将用户信息同步到 Azure AD。
| 81012 | 尝试登录到 Azure AD 的用户不同于已登录到设备的用户。 | 用户需要从不同的设备登录。
| 81013 | 根据用户的 Kerberos 票证中的信息，找不到用户对象。 |使用 Azure AD Connect 将用户信息同步到 Azure AD。 

## <a name="troubleshooting-checklist"></a>故障排除清单

使用以下清单排查无缝 SSO 问题：

- 确保在 Azure AD Connect 中已启用无缝 SSO 功能。 如果无法启用该功能（例如，由于端口被阻止），请确保事先满足所有[先决条件](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)。
- 如果同时对租户启用了 [Azure AD Join](../active-directory-azureadjoin-overview.md)和无缝 SSO，请确保 Azure AD Join 没有问题。 如果设备同时注册了 Azure AD 并加入了域，则 Azure AD Join 的 SSO 将优先于无缝 SSO。 使用 Azure AD Join 的 SSO，用户将看到显示“已连接到 Windows”的登录磁贴。
- 确保 Azure AD URL (https://autologon.microsoftazuread-sso.com) 是用户 Intranet 区域设置中的一部分。
- 确保企业设备已加入 Active Directory 域。 设备  不需要[加入 Azure AD](../active-directory-azureadjoin-overview.md)，无缝 SSO 便可工作。
- 确保用户已通过 Active Directory 域帐户登录到设备。
- 确保用户的帐户来自已设置了无缝 SSO 的 Active Directory 林。
- 确保已在企业网络中连接该设备。
- 确保设备的时间与 Active Directory 和各域控制器的时间同步，并且彼此偏差不超过 5 分钟。
- 确保 `AZUREADSSOACC` 计算机帐户在要启用无缝 SSO 的每个 AD 林中存在并已启用。 如果计算机帐户已被删除或丢失，可使用 [PowerShell cmdlets](#manual-reset-of-the-feature) 重新创建。
- 使用命令提示符中的 `klist` 命令列出设备上的现有 Kerberos 票证。 确保存在针对 `AZUREADSSOACC` 计算机帐户颁发的票证。 通常情况下，用户的 Kerberos 票证的有效期为 10 小时。 你可能在 Active Directory 中有不同的设置。
- 如果对租户禁用并重新启用无缝 SSO，则用户在其缓存的 Kerberos 票证过期前，将不会获得单一登录体验。
- 使用 `klist purge` 命令从设备中清除现有的 Kerberos 票证，然后重试。
- 若要确定是否存在与 JavaScript 相关的问题，请查看浏览器的控制台日志（在“开发人员工具”  下）。
- 查看[域控制器日志](#domain-controller-logs)。

### <a name="domain-controller-logs"></a>域控制器日志

如果在域控制器上成功启用审核，则每当用户通过无缝 SSO 登录时，将在事件日志中记录一个安全条目。 你可以使用以下查询来查找这些安全事件。 （查找与计算机帐户 AzureADSSOAcc$  相关联的事件 4769  。）

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>手动重置功能

如果故障排除不起作用，请在租户中手动重置该功能。 在运行 Azure AD Connect 的本地服务器上执行以下步骤。

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>步骤 1：导入无缝 SSO PowerShell 模块

1. 首先，下载并安装 [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview)。
2. 浏览到 `%programfiles%\Microsoft Azure Active Directory Connect` 文件夹。
3. 使用以下命令导入无缝 SSO PowerShell 模块：`Import-Module .\AzureADSSO.psd1`。

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>步骤 2：获取已启用了无缝 SSO 的 Active Directory 林列表

1. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 出现提示时，输入租户的全局管理员凭据。
2. 调用 `Get-AzureADSSOStatus`。 此命令可提供已在其中启用了此功能的 Active Directory 林列表（请查看“域”列表）。

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>步骤 3：禁用已设置了该功能的每个 Active Directory 林的无缝 SSO

1. 调用 `$creds = Get-Credential`。 出现提示时，输入目标 Active Directory 林的域管理员凭据。

   > [!NOTE]
   > 我们使用以用户主体名称 (UPN) (johndoe@contoso.com) 格式或域限定的 SAM 帐户名（contoso\johndoe 或 contoso.com\johndoe）格式提供的域管理员用户名查找目标 AD 林。 如果你使用域限定的 SAM 帐户名，则我们使用用户名的域部分[使用 DNS 查找域管理员的域控制器](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx)。 如果你使用的是 UPN，则我们在查找合适的域控制器前会[将它转换为域限定的 SAM 帐户名](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa)。

2. 调用 `Disable-AzureADSSOForest -OnPremCredentials $creds`。 此命令将从本地域控制器删除此特定 Active Directory 林的 `AZUREADSSOACC` 计算机帐户。
3. 为在其中设置了该功能的每个 Active Directory 林重复上述步骤。

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>步骤 4：为每个 Active Directory 林启用无缝 SSO

1. 调用 `Enable-AzureADSSOForest`。 出现提示时，输入目标 Active Directory 林的域管理员凭据。

   > [!NOTE]
   > 我们使用以用户主体名称 (UPN) (johndoe@contoso.com) 格式或域限定的 SAM 帐户名（contoso\johndoe 或 contoso.com\johndoe）格式提供的域管理员用户名查找目标 AD 林。 如果你使用域限定的 SAM 帐户名，则我们使用用户名的域部分[使用 DNS 查找域管理员的域控制器](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx)。 如果你使用的是 UPN，则我们在查找合适的域控制器前会[将它转换为域限定的 SAM 帐户名](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa)。

2. 为你要在其中设置该功能的每个 Active Directory 林重复上述步骤。

### <a name="step-5-enable-the-feature-on-your-tenant"></a>步骤 5。 在租户上启用此功能

若要在租户上启用此功能，请调用 `Enable-AzureADSSO -Enable $true`。
