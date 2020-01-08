---
title: Azure HDInsight 中的身份验证问题
description: Azure HDInsight 中的身份验证问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 3d2ba5965fef19a36faa8b9bbef235fd4117c20f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71071942"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Azure HDInsight 中的身份验证问题

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

在 Azure Data Lake （Gen1 或 Gen2）支持的安全群集上，当域用户通过 HDI Gateway 登录到群集服务（如登录到 Apache Ambari 门户）时，HDI 网关将首先尝试从 Azure Active Directory （Azure AD）获取 OAuth 令牌，然后从 Azure AD DS 获取 Kerberos 票证。 在以下任一阶段，身份验证可能会失败。 本文旨在调试其中一些问题。

如果身份验证失败，系统会提示你输入凭据。 如果取消此对话框，则将打印错误消息。 下面是一些常见的错误消息：

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant 或 unauthorized_client，50126

### <a name="issue"></a>问题

联合用户登录失败，错误代码为50126（云用户的登录成功）。 错误消息类似于：

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>原因

Azure AD 错误代码50126表示该`AllowCloudPasswordValidation`策略尚未由租户设置。

### <a name="resolution"></a>分辨率

Azure AD 租户的公司管理员应该允许 Azure AD 为支持 ADFS 的用户使用密码哈希。  应用， `AllowCloudPasswordValidationPolicy`如[使用 HDInsight 中的企业安全性套餐](../domain-joined/apache-domain-joined-architecture.md)一文中所示。

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant 或 unauthorized_client，50034

### <a name="issue"></a>问题

登录失败，错误代码为50034。 错误消息类似于：

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>原因

用户名不正确（不存在）。 用户使用的用户名与 Azure 门户中使用的用户名相同。

### <a name="resolution"></a>分辨率

使用该门户中的相同用户名。

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant 或 unauthorized_client，50053

### <a name="issue"></a>问题

用户帐户被锁定，错误代码50053。 错误消息类似于：

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>原因

使用错误的密码尝试登录的次数过多。

### <a name="resolution"></a>分辨率

等待30分钟，停止任何可能尝试进行身份验证的应用程序。

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant 或 unauthorized_client，50053

### <a name="issue"></a>问题

密码已过期，错误代码50053。 错误消息类似于：

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>原因

密码已过期。

### <a name="resolution"></a>分辨率

更改 "Azure 门户中的密码（在本地系统上），然后等待30分钟以便同步同步。

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>问题

收到错误消息`interaction_required`。

### <a name="cause"></a>原因

条件访问策略或 MFA 正在应用于用户。 由于目前尚不支持交互式身份验证，因此需要从 MFA/条件访问中免除用户或群集。 如果选择免除群集（基于 IP 地址的免除策略），请确保已为该 vnet 启用 AD `ServiceEndpoints` 。

### <a name="resolution"></a>分辨率

按照[使用 Azure Active Directory 域服务配置包含企业安全性套餐的 HDInsight 群集](./apache-domain-joined-configure-using-azure-adds.md)中所述，使用条件访问策略并免除 MFA 中的 HDInisght 群集。

---

## <a name="sign-in-denied"></a>已拒绝登录

### <a name="issue"></a>问题

拒绝登录。

### <a name="cause"></a>原因

若要转到此阶段，OAuth 身份验证不是问题，但 Kerberos 身份验证为。 如果此群集由 ADLS 提供支持，则在尝试 Kerberos 身份验证之前，OAuth 登录已成功。 在 WASB 群集上，不会尝试 OAuth 登录。 Kerberos 失败的原因有很多，例如密码哈希不同步、用户帐户在 Azure AD DS 中被锁定等。 仅当用户更改密码时，才会同步密码哈希。 创建 Azure AD DS 实例时，它将开始同步在创建后更改的密码。 它不会在开始之前设置以追溯方式同步密码。

### <a name="resolution"></a>分辨率

如果你认为密码可能不同步，请尝试更改密码并等待几分钟进行同步。

尝试通过 SSH 连接到，你将需要尝试使用同一用户凭据（从已加入域的计算机）进行身份验证（kinit）。 使用本地用户通过 SSH 连接到头/边缘节点，然后运行 kinit。

---

## <a name="kinit-fails"></a>kinit 失败

### <a name="issue"></a>问题

Kinit 失败。

### <a name="cause"></a>原因

随着.

### <a name="resolution"></a>分辨率

要使 kinit 成功，需要知道`sAMAccountName` （这是没有领域的短帐户名）。 `sAMAccountName`通常是帐户前缀（如中`bob@contoso.com`的 bob）。 对于某些用户，可能会有所不同。 你将需要能够浏览/搜索目录以了解你`sAMAccountName`的。

查找`sAMAccountName`方法：

* 如果可以使用本地 Ambari 管理员登录到 Ambari，请查看用户列表。

* 如果已[加入域的 windows 计算机](../../active-directory-domain-services/manage-domain.md)，则可以使用标准的 windows AD 工具进行浏览。 这需要域中有一个工作帐户。

* 在头节点中，可以使用 SAMBA 命令搜索。 这需要有效的 Kerberos 会话（成功 kinit）。 net ads 搜索 "（userPrincipalName = bob *）"

    搜索/浏览结果应显示`sAMAccountName`属性。 此外，还可以查看`pwdLastSet` `userPrincipalName` 、 `badPasswordTime`等其他属性，以查看这些属性是否与预期相符。

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit 失败，预身份验证失败

### <a name="issue"></a>问题

Kinit 失败， `Preauthentication`出现故障。

### <a name="cause"></a>原因

用户名或密码不正确。

### <a name="resolution"></a>分辨率

请检查用户名和密码。 还要检查前面介绍的其他属性。 若要启用详细调试， `export KRB5_TRACE=/tmp/krb.log`请在尝试 kinit 之前从会话中运行。

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>作业/HDFS 命令因 TokenNotFoundException 而失败

### <a name="issue"></a>问题

作业/HDFS 命令因`TokenNotFoundException`而失败。

### <a name="cause"></a>原因

找不到所需的 OAuth 访问令牌，无法成功完成该作业/命令。 在发出存储请求之前，ADLS/ABFS 驱动程序将尝试从凭据服务检索 OAuth 访问令牌。 当使用同一用户登录到 Ambari 门户时，将注册此令牌。

### <a name="resolution"></a>分辨率

确保你已成功登录到 Ambari 门户，并通过使用其标识的用户名来运行作业。

---

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* [@AzureSupport](https://twitter.com/azuresupport)连接-官方 Microsoft Azure 帐户来改善客户体验。 将 Azure 社区连接到正确的资源：答案、支持和专家。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”，或打开“帮助 + 支持”中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限，并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。
