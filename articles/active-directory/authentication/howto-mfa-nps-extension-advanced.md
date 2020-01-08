---
title: 配置 Azure MFA NPS 扩展-Azure Active Directory
description: 在安装 NPS 扩展后，通过下列步骤来进行高级配置，如 IP 允许列表和 UPN 替换。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e156585ba063515bd8be573b5d99b41e7ce35d1
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932492"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>用于多重身份验证的 NPS 扩展的高级配置选项

网络策略服务器 (NPS) 扩展可将基于云的 Azure 多重身份验证扩展至本地基础结构。 本文假设你已安装扩展，并想了解如何为自身需求自定义扩展。 

## <a name="alternate-login-id"></a>备用登录 ID

由于 NPS 扩展同时连接到本地和云端的目录，因此可能会出现本地用户主体名称 (UPN) 与云中的名称不匹配的问题。 要解决此问题，请使用备用登录 ID。 

在 NPS 扩展中，可以指定一个 Active Directory 属性，用它来替换用于 Azure 多重身份验证的 UPN。 这样就能通过双重验证来保护本地资源，且无需修改本地 UPN。 

要配置备用登录 ID，请转至 `HKLM\SOFTWARE\Microsoft\AzureMfa` 并编辑下列注册表值：

| 姓名 | 类型 | 默认值 | 描述 |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | string | 空 | 指定要使用的 Active Directory 属性（而非 UPN）的名称。 此属性将用作 AlternateLoginId 属性。 如果将此注册表值设置为[有效的 Active Directory 属性](https://msdn.microsoft.com/library/ms675090.aspx)（例如 mail 或 displayName），那么将使用该属性的值（而不使用用户的 UPN）来进行身份验证。 如果此注册表值为空或未配置，则将禁用 AlternateLoginId，并使用用户的 UPN 来进行身份验证。 |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | False | 在查找 AlternateLoginId 时，凭此标记强制使用全局编录执行 LDAP 搜索。 将域控制器配置为全局编录，向全局编录中添加 AlternateLoginId 属性，然后启用此标记。 <br><br> 如果配置了 LDAP_LOOKUP_FORESTS（非空），则无论注册表设置的值为何，都会将此标记强制设为 True。 在这种情况下，NPS 扩展要求对每个林都使用 AlternateLoginId 属性来配置全局编录。 |
| LDAP_LOOKUP_FORESTS | string | 空 | 提供以分号分隔的林列表以供搜索。 例如，contoso.com;foobar.com。 如果配置了此注册表值，则 NPS 扩展将以迭代的方式、按列表顺序搜索整个林，然后返回第一个成功的 AlternateLoginId 值。 如果未配置此注册表值，则将 AlternateLoginId 的查找范围限制在当前域中。|

要使用备用登录 ID 排除故障，请对[备用登录 ID 错误](howto-mfa-nps-extension-errors.md#alternate-login-id-errors)执行推荐的步骤。

## <a name="ip-exceptions"></a>IP 异常

如果需要监视服务器的可用性（例如，负载均衡器是否在发送工作负荷前验证了哪个服务器正在运行），则并不希望验证请求阻止这些检查。 而是创建已知由服务帐户使用的 IP 地址列表，并为该列表禁用多重身份验证要求。

若要配置 IP 允许列表，请参阅`HKLM\SOFTWARE\Microsoft\AzureMfa`并配置以下注册表值：

| 姓名 | 类型 | 默认值 | 描述 |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | string | 空 | 提供以分号隔开的 IP 地址列表。 包括发出服务请求的计算机的 IP 地址，例如 NAS/VPN 服务器。 不支持 IP 范围和子网。 <br><br> 例如 *10.0.0.1;10.0.0.2;10.0.0.3*。

> [!NOTE]
> 此注册表项不是由安装程序默认创建的，并且在重新启动该服务时，AuthZOptCh 日志中会出现错误。 可能会忽略日志中的此错误，但如果创建了此注册表项并在不需要时保留为空，则不会返回错误消息。

当请求传入来自中`IP_WHITELIST`存在的 IP 地址时，将跳过双重验证。 IP 列表与 RADIUS 请求的*包含 ratnasipaddress*属性中提供的 ip 地址进行比较。 如果传入的 RADIUS 请求不包含 ratNASIPAddress 属性，则会记录以下警告：“P_WHITE_LIST_WARNING::IP 允许列表被忽略，因为 RADIUS 请求中的 NasIpAddress 属性缺少源 IP。”

## <a name="next-steps"></a>后续步骤

[解决 Azure 多重身份验证的 NPS 扩展出现的错误消息](howto-mfa-nps-extension-errors.md)
