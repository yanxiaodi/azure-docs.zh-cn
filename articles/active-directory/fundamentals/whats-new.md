---
title: 新增功能 发行说明 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 中的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将应用的更改。
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2598ce01c749574833d53133ed3aced184e71737
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694601"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

>通过复制并粘贴此 URL，获取有关何时通过复制并粘贴此 URL 来`https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`重新访问![此页面的通知](./media/whats-new/feed-icon-16x16.png) ：到 rss 源读者图标源读者。

Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 如果要查找超过 6 个月的项目，可以在 [Azure Active Directory 新增功能存档](whats-new-archive.md)中找到它们。

---

## <a name="september-2019"></a>2019 年 9 月

### <a name="my-profile-is-re-naming-and-integrating-with-the-microsoft-office-account-page"></a>我的配置文件正在重新命名并与 Microsoft Office 帐户页面集成

**类型：** 更改计划  
**服务类别：** 我的配置文件/帐户  
**产品功能：** 协作

从10月开始，"我的个人资料经验" 将成为我的帐户。 作为此更改的一部分，当前说， **"我的配置文件**" 将更改为 **"** 我的帐户"。 在命名更改和某些设计改进的基础上，更新的体验将提供与 Microsoft Office 帐户页面的其他集成。 具体而言，你将能够从 "**概述帐户**" 页访问 office 安装和订阅，还可以从 "**隐私**" 页访问与 Office 相关的联系人首选项。

有关 "我的个人资料（预览版）" 体验的详细信息，请参阅[我的个人资料（预览版）门户概述](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)。

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>在 Azure AD 门户中使用 CSV 文件批量管理组和成员（公共预览版）

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

我们非常高兴地宣布 Azure AD 门户中的批量组管理体验的公开预览版。 你现在可以使用 CSV 文件和 Azure AD 门户来管理组和成员列表，其中包括：

- 添加或删除组中的成员。

- 正在从目录下载组列表。

- 正在下载特定组的组成员的列表。

有关详细信息，请参阅[批量添加成员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)、[批量删除成员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)、[批量下载成员列表](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)和[批量下载组列表](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)。

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>现在，通过新的管理员许可终结点支持动态许可

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

我们已经创建了一个新的管理员许可终结点来支持动态同意，这对于想要在 Microsoft 标识平台上使用动态同意模式的应用很有用。

有关如何使用此新终结点的详细信息，请参阅[使用管理员许可终结点](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)。

---

### <a name="new-azure-ad-global-reader-role"></a>新 Azure AD 全局读者角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制

从2019年9月24日开始，我们将开始推出称为全局读取器的新 Azure Active Directory （AD）角色。 此推出将从生产和全球云客户（GCC）开始，在10月的全球范围内完成。

全局读者角色是全局管理员的只读对应角色。 此角色中的用户可以跨 Microsoft 365 服务读取设置和管理信息，但不能执行管理操作。 我们已经创建了全局读者角色，以帮助减少组织中全局管理员的数量。 由于全局管理员帐户功能强大并且容易受到攻击，因此我们建议你的全局管理员少于五台。 建议使用全局读者角色进行计划、审核或调查。 我们还建议将全局读者角色与其他有限的管理员角色（如 Exchange 管理员）结合使用，以帮助完成工作而不需要全局管理员角色。

全局读者角色适用于新的 Microsoft 365 管理中心、Exchange 管理中心、团队管理中心、安全中心、合规性中心、Azure AD 管理中心和设备管理管理中心。

>[!NOTE]
> 公共预览版开始时，全局读者角色将无法使用：SharePoint，Privileged Access Management，客户密码箱，敏感性标签，团队生命周期，团队报表 & 呼叫分析、团队 IP 电话设备管理和团队应用程序目录。 所有这些服务都适用于将来的角色。

有关详细信息，请参阅[Azure Active Directory 中的管理员角色权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理从 Power BI 移动版应用访问本地报表服务器

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制

通过 Power BI 移动应用与 Azure AD 应用程序代理之间的新集成，你可以安全地登录到 Power BI 移动应用，并查看本地 Power BI 报表服务器上托管的任何组织的报表。

有关 Power BI 移动版应用的信息，包括下载应用的位置，请参阅[Power BI 站点](https://powerbi.microsoft.com/mobile/)。 有关如何使用 Azure AD 应用程序代理设置 Power BI 移动应用的详细信息，请参阅[使用 Azure AD 应用程序代理启用对 Power BI 移动版的远程访问](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)。

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>新版本的 AzureADPreview PowerShell 模块可用

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** 目录

新的 cmdlet 已添加到 AzureADPreview 模块中，可帮助定义和分配 Azure AD 中的自定义角色，其中包括：

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect 的新版本

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** 目录

我们发布了自动升级客户的更新版本 Azure AD Connect。 此新版本包括几项新功能、改进和 bug 修复。 有关此新版本的详细信息，请参阅 [Azure AD Connect：版本发布历史记录](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14x0)。

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>现在可以使用 Azure 多重身份验证（MFA）服务器版本8.0。2

**类型：** 已修复  
**服务类别：** MFA  
**产品功能：** 标识安全性和保护

如果你是在2019年7月1日之前激活了 MFA 服务器的现有客户，则现在可以下载最新版本的 MFA 服务器（8.0.2 版）。 在这个新版本中，我们：

- 修复了这样的问题：当 Azure AD 同步将用户从禁用更改为启用时，会向用户发送电子邮件。

- 修复了一个问题，使客户能够成功升级，同时继续使用标记功能。

- 添加了科索沃（+ 383）国家/地区代码。

- 向 Multifactorauthsvc.log 添加了一次性跳过审核日志记录。

- 提高了 Web 服务 SDK 的性能。

- 修复了其他次要错误。

从2019年7月1日开始，Microsoft 停止为新部署提供 MFA 服务器。 需要多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 有关详细信息，请参阅[规划基于云的 Azure 多重身份验证部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)。

---

## <a name="august-2019"></a>2019 年 8 月

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>计划更改:弃用 Power BI 内容包

**类型：** 更改计划  
**服务类别：** 报告  
**产品功能：** 监视和报告

从2019年10月1日开始，Power BI 将开始弃用所有内容包，包括 Azure AD Power BI 内容包。 作为此内容包的替代方法，你可以使用 Azure AD 工作簿来深入了解你 Azure AD 相关的服务。 还有其他工作簿，其中包括有关仅限报表模式下的条件性访问策略的工作簿、基于应用同意的见解，等等。

有关工作簿的详细信息，请参阅[如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。 有关内容包弃用的详细信息，请参阅[公告 Power BI 模板应用正式](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)发布博客文章。

---

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Azure AD 门户（公共预览版）中提供了组的增强搜索、筛选和排序功能

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

我们非常高兴地宣布 Azure AD 门户中的增强组相关体验的公共预览版。 这些增强功能可帮助你更好地管理组和成员列表，方法是提供：

- 高级搜索功能，如对组列表的子字符串搜索。
- 成员和所有者列表上的高级筛选和排序选项。
- 成员和所有者列表的新搜索功能。
- 大型组的更准确的组计数。

有关详细信息，请参阅[Azure 门户中的管理组](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)。

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>新的自定义角色可用于应用注册管理（公共预览版）

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制

自定义角色（与 Azure AD P1 或 P2 订阅一起提供）现在可以通过让你创建具有特定权限的角色定义，然后将这些角色分配给特定资源，帮助提供精细的访问权限。 目前，你通过使用管理应用注册的权限，然后将该角色分配给特定应用来创建自定义角色。 有关自定义角色的详细信息，请参阅[Azure Active Directory （预览版）中的自定义管理员角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)。

如果需要其他权限或支持的资源（当前未显示），可以将反馈发送到我们的[Azure 反馈网站](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)，我们会将你的请求添加到我们的更新路线图。

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>新的预配日志可帮助你监视应用预配部署并对其进行故障排除（公共预览版）

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理

提供了新的预配日志，可帮助你监视用户和组预配部署。 这些新日志文件包含以下信息：

- 已成功在[ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)中创建哪些组
- 从 Amazon Web Services 导入的角色[（AWS）](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- 员工未从[Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)导入的内容

有关详细信息，请参阅[在 Azure Active Directory 门户（预览版）中预配报表](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)。

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>所有 Azure AD 管理员的新安全报表（公开发布）

**类型：** 新功能  
**服务类别：** Identity Protection  
**产品功能：** 标识安全性和保护

默认情况下，所有 Azure AD 管理员很快就可以访问 Azure AD 内的新式安全报告。 在9月底结束之前，你将能够使用新式安全报表顶部的横幅返回到旧报表。

新式安全报告将提供更早版本的其他功能，包括：

- 高级筛选和排序
- 大容量操作，如消除用户风险
- 确认泄露或安全实体
- 风险状态，涵盖：有风险，已解除、修正和确认已泄露
- 新的与风险相关的检测（可用于 Azure AD Premium 订户）

有关详细信息，请参阅有[风险的用户](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report)、有风险的[登录](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)和[风险检测](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections-report)。

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>用户分配的托管标识可用于虚拟机和虚拟机规模集（正式发布）

**类型：** 新功能  
**服务类别：** Azure 资源的托管标识  
**产品功能：** 开发人员体验

用户分配的托管标识现已正式用于虚拟机和虚拟机规模集。 作为此过程的一部分，Azure 可以在所使用的订阅所信任的 Azure AD 租户中创建标识，并且可以将其分配给一个或多个 Azure 服务实例。 有关用户分配的托管标识的详细信息，请参阅[Azure 资源的托管标识是什么？](https://aka.ms/azuremanagedidentity)。

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>用户可以使用移动应用或硬件令牌来重置其密码（正式发布）

**类型：** 已更改的功能  
**服务类别：** 自助服务密码重置  
**产品功能：** 用户身份验证

向你的组织注册了移动应用的用户现在可以通过从 Microsoft Authenticator 应用批准通知或通过从其移动应用或硬件令牌输入代码来重置其自己的密码。

有关详细信息，请[参阅工作原理：Azure AD 自助式密码重置](https://aka.ms/authappsspr)。 有关用户体验的详细信息，请参阅[重置你自己的工作或学校密码概述](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)。

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET 为代表的方案忽略 MSAL.NET 共享缓存

**类型：** 已修复  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

从 Azure AD 身份验证库（ADAL.NET）第5.0.0 版-预览版开始，应用开发人员必须[为 web 应用和 Web api 每个帐户序列化一个缓存](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)。 否则，某些使用代理[流](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)的方案以及某些特定用例`UserAssertion`可能会导致权限提升。 为避免出现这种情况，ADAL.NET 会为代表方案忽略用于 dotnet （MSAL.NET）共享缓存的 Microsoft 身份验证库。

有关此问题的详细信息，请参阅[Azure Active Directory Authentication Library 提升权限漏洞](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD 应用库中提供了新的联合应用程序-2019 年8月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年8月，我们已将这26个具有联合支持的新应用添加到应用库：

[市政平台](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial)， [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial)， [ProNovos Ops 经理](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial)， [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial)， [Viareport Inativ Portal （欧洲）](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial)， [Azure Databricks](https://azure.microsoft.com/services/databricks)，[循](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial)，[院校](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial) [优先矩阵](https://sync.appfluence.com/pmwebng/)、[Cousto MySpace](https://cousto.platformers.be/account/login)， [Uploadcare](https://uploadcare.com/accounts/signup/)， [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial)， [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial)， [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial)， [media™门户](https://portal.deliver.media)，[前端教育](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial)， [F5](https://www.f5.com/products/security/access-policy-manager)， [stashcat ADconnect](https://www.stashcat.com)、[闪烁](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial)、 [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial)、 [ProNovos 分析](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial)、 [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial)、 [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial)、[按颜色监视](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial)、[工具](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)、 [EAB 导航战略性护理](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>提供了新版本的 AzureAD PowerShell 和 AzureADPreview PowerShell 模块

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** 目录

AzureAD 和 AzureAD 预览版 PowerShell 模块的新更新可用：

- 已将`-Filter`新参数添加到 AzureAD `Get-AzureADDirectoryRole`模块中的参数。 此参数可帮助你筛选 cmdlet 返回的目录角色。
- 新的 cmdlet 已添加到 AzureADPreview 模块中，可帮助定义和分配 Azure AD 中的自定义角色，其中包括：

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Azure 门户中动态组规则生成器的 UI 改进

**类型：** 已更改的功能  
**服务类别：** 组管理  
**产品功能：** 协作

我们对动态组规则生成器进行了一些 UI 改进，该生成器在 Azure 门户中提供，可帮助你更轻松地设置新规则或更改现有规则。 此设计改进允许您创建最多包含五个表达式（而不只是一个）的规则。 我们还更新了设备属性列表，以删除不推荐使用的设备属性。

有关详细信息，请参阅[管理动态成员身份规则](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule)。

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>可以与访问评审一起使用的新 Microsoft Graph 应用权限

**类型：** 已更改的功能  
**服务类别：** 访问评审  
**产品功能：** 标识监管

我们引入了新的 Microsoft Graph 应用权限`AccessReview.ReadWrite.Membership`，它允许应用自动创建和检索组成员身份和应用分配的访问评审。 此权限可由计划作业或自动化的一部分使用，无需登录的用户上下文。

有关详细信息，请参阅[示例如何使用 PowerShell 博客创建 Azure AD 访问评审 Microsoft Graph 应用权限](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)。

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD 活动日志现在适用于 Azure Monitor 中的政府云实例

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们很高兴地宣布，Azure AD 活动日志现在可用于 Azure Monitor 中的政府云实例。 你现在可以将 Azure AD 日志发送到你的存储帐户或事件中心，以便与 SIEM 工具（如[Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)、 [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)和[ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight)）集成。 

有关设置 Azure Monitor 的详细信息，请参阅[Azure Monitor 中 Azure AD 活动日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations)。

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>将用户更新为新的增强安全信息体验

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）   
**产品功能：** 用户身份验证

2019年9月25日，我们将关闭用于注册和管理用户安全信息的旧的、不安全的安全信息体验，并只启用新的[增强版本](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。 这意味着你的用户将无法再使用旧体验。

有关增强的安全信息体验的详细信息，请参阅我们的[管理员文档](https://aka.ms/securityinfodocs) 和我们的[用户文档](https://aka.ms/securityinfoguide)。

#### <a name="to-turn-on-this-new-experience-you-must"></a>若要启用这种新体验，你必须：

1. 以全局管理员或用户管理员身份登录到 Azure 门户。

2. 请参阅 **Azure Active Directory > 用户设置 > 管理访问面板预览功能的设置**。

3. 在 "**用户可以使用预览功能注册和管理安全信息-增强**区域" 中，选择 "已**选择**"，然后选择一组用户或选择 "**全部**" 以为租户中的所有用户启用此功能。

4. 在 "用户可以使用预览功能注册和管理安全性 * * 信息" 区域中，选择 "**无**"。

5. 保存设置。

    保存设置后，将无法再访问旧的安全信息体验。

>[!Important]
>如果未在2019年9月25日之前完成这些步骤，则会自动为你的 Azure Active Directory 租户启用增强体验。 如果有疑问，请通过联系我们registrationpreview@microsoft.com。

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>使用 POST 登录名的身份验证请求将受到严格验证

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 标准

从2019年9月2日开始，使用 POST 方法的身份验证请求将更严格地根据 HTTP 标准进行验证。 具体而言，将不再从请求窗体值中删除空格和双引号（""）。 这些更改不会中断任何现有的客户端，并且有助于确保每次都能可靠地处理发送到 Azure AD 的请求。

有关详细信息，请参阅[Azure AD 重大更改通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)。

---

## <a name="july-2019"></a>2019 年 7 月

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>计划更改:应用程序代理服务更新，仅支持 TLS 1。2

**类型：** 更改计划  
**服务类别：** 应用代理  
**产品功能：** 访问控制

为了帮助提供最强大的加密，我们将开始限制应用程序代理服务只能访问 TLS 1.2 协议。 此限制最初会向已使用 TLS 1.2 协议的客户推出，因此你不会看到这种影响。 TLS 1.0 和 TLS 1.1 协议的完整弃用将于8月 2019 31 日完成。 仍在使用 TLS 1.0 和 TLS 1.1 的客户将收到有关为此更改做好准备的高级通知。

若要在此更改期间保持与应用程序代理服务的连接，建议你确保客户端-服务器和浏览器-服务器组合已更新为使用 TLS 1.2。 我们还建议确保包含员工使用的任何客户端系统，以访问通过应用程序代理服务发布的应用。

有关详细信息，请参阅[在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)。

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>计划更改:适用于应用程序库的设计更新

**类型：** 更改计划  
**服务类别：** 企业应用  
**产品功能：** SSO

新用户界面更改将从 "**添加应用程序**" 边栏选项卡的 **"库**" 区域中的 "添加" 设计。 这些更改将帮助你更轻松地查找支持自动预配、OpenID Connect、安全断言标记语言（SAML）和密码单一登录（SSO）的应用。

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>计划更改:从 Office 365 IP 地址中删除 MFA 服务器 IP 地址

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全性和保护

我们正在从[Office 365 Ip 地址和 URL Web 服务](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service)中删除 MFA 服务器的 ip 地址。 如果你当前依赖这些页面来更新防火墙设置，则必须确保还包括 " **Azure 多重身份验证服务器防火墙要求**" 部分的 "入门" 中所述的 IP 地址列表[Azure 多重身份验证服务器](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)一文。

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>仅限应用的令牌现在要求客户端应用存在于资源租户中

**类型：** 已修复  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

2019年7月26日，我们更改了我们通过[客户端凭据 grant](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)提供仅限应用的令牌的方式。 以前，无论客户端应用是否在租户中，应用都可以获取令牌来调用其他应用。 我们更新了此行为，因此只能由资源租户中存在的客户端应用调用单租户资源（有时称为 Web Api）。

如果你的应用程序不在资源租户中，你将收到一条错误消息， `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`指出，若要解决此问题，必须使用[管理员许可终结点](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint)或[通过 PowerShell 在租户中创建客户端应用服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)，确保你的租户已为租户提供了在租户内操作的权限。

有关详细信息，请参阅[身份验证的新增功能？](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)。

> [!NOTE]
> 客户端与 API 之间的现有同意将继续进行。 应用仍应执行其自己的授权检查。

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>New 无密码使用 FIDO2 安全密钥登录到 Azure AD

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

Azure AD 客户现在可以设置策略以管理其组织的用户和组的 FIDO2 安全密钥。 最终用户还可以自行注册其安全密钥，在 FIDO 支持的设备上使用密钥登录到网站上的 Microsoft 帐户，以及登录到其 Azure AD 联接的 Windows 10 设备。

有关详细信息，请参阅[Enable 无密码 sign in for Azure AD （预览版）](/azure/active-directory/authentication/concept-authentication-passwordless) ，以获取管理员相关信息，并[将安全信息设置为对最终用户相关信息使用安全密钥（预览）](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) 。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD 应用库中提供了新的联合应用程序-2019 年7月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年7月，我们已将以下18个新应用和联合支持添加到应用库：

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial)，[鲜模式对全渠道 Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial)，[聪明 Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial)， [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial)， [Looop](https://www.looop.co/schedule-a-demo/)， [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial)， [MS Azure SSO Access for Ethidex 相容 Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso)，[夸大宣传](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial)， [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial)、 [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial)、 [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html)、 [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial)、 [TwineSocial](https://twinesocial.com/)、 [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial)、 [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial)、 [PharmID WasteWitness](https://www.pharmid.com/)、 [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/)、 [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动执行这些新支持的 SaaS 应用的用户帐户预配

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 监视和报告

你现在可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [联合目录](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>网络安全组的新 Azure AD 域服务服务标记

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

如果你厌倦了管理 IP 地址和范围的长列表，则可以使用 Azure 网络安全组中的 new **AzureActiveDirectoryDomainServices** network service 标记来帮助保护到 Azure AD 域服务虚拟的入站流量网络子网。

有关此新服务标记的详细信息，请参阅[Azure AD 域服务的网络安全组](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD 域服务的新安全审核（公共预览版）

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

我们很高兴地宣布发布 Azure AD 域服务安全审核到公共预览版。 通过使用 Azure AD 域服务将安全审核事件流式传输到目标资源（包括 Azure 存储、Azure Log Analytics 工作区和 Azure 事件中心），安全审核可帮助你深入了解身份验证服务端口.

有关详细信息，请参阅[为 Azure AD 域服务（预览版）启用安全审核](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)。

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>新的身份验证方法用法 & insights （公共预览版）

**类型：** 新功能  
**服务类别：** 自助服务密码重置  
**产品功能：** 监视和报告

使用 & insights 报表的新的身份验证方法，可帮助你了解 Azure 多重身份验证和自助密码重置等功能在你的组织中的注册和使用方式，包括已注册的数量每项功能的用户、使用自助服务密码重置重置密码的频率以及重置的方式。

有关详细信息，请参阅[身份验证方法用法 & insights （预览版）](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)。

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>所有 Azure AD 管理员都可以使用新的安全报告（公共预览版）

**类型：** 新功能  
**服务类别：** Identity Protection  
**产品功能：** 标识安全性和保护

所有 Azure AD 管理员现在可以选择现有安全报告（如**标记为风险**报告的用户）顶部的横幅，以开始使用有**风险的用户**和有**风险的登录**报告中所示的新安全体验. 随着时间的推移，所有安全报告都将从较旧的版本转移到新版本，同时提供了以下附加功能：

- 高级筛选和排序

- 大容量操作，如消除用户风险

- 确认泄露或安全实体

- 风险状态，涵盖：有风险，已解除、修正和确认已泄露

有关详细信息，请参阅有[风险的用户报告](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report)和有[风险的登录报告](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD 域服务的新安全审核（公共预览版）

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

我们很高兴地宣布发布 Azure AD 域服务安全审核到公共预览版。 通过使用 Azure AD 域服务将安全审核事件流式传输到目标资源（包括 Azure 存储、Azure Log Analytics 工作区和 Azure 事件中心），安全审核可帮助你深入了解身份验证服务端口.

有关详细信息，请参阅[为 Azure AD 域服务（预览版）启用安全审核](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)。

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>使用 SAML/WS 进纸进行新的 B2B 直接联合身份验证（公共预览版）

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C

通过使用支持 SAML 或 WS 送标准的标识系统，直接联合身份验证可让你更轻松地使用 IT 托管标识解决方案不 Azure AD 的合作伙伴。 设置与合作伙伴的直接联合身份验证关系后，你从该域邀请的任何新来宾用户都可以使用其现有组织帐户与你协作，使你的来宾的用户体验更顺畅。

有关详细信息，请参阅[guest 用户的直接联合身份验证 AD FS 和第三方提供程序（预览）](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动执行这些新支持的 SaaS 应用的用户帐户预配

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 监视和报告

你现在可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [联合目录](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>新检查 Azure AD 门户中的重复组名称

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

现在，当你在 Azure AD 门户中创建或更新组名称时，我们将执行检查以查看你是否在资源中复制现有组名。 如果我们确定名称已被另一个组使用，系统会要求你修改名称。

有关详细信息，请参阅[Azure AD 门户中的 "管理组"](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)。

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>现在 Azure AD 在答复（重定向） Uri 中支持静态查询参数

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

Azure AD 应用现在可以使用静态查询参数（例如， `https://contoso.com/oauth2?idp=microsoft`）为 OAuth 2.0 请求注册和使用回复（重定向） uri。 静态查询参数服从于回复 URI 的字符串匹配，就像回复 URI 的任何其他部分一样。 如果没有任何已注册的字符串与 URL 解码的重定向 uri 匹配，则会拒绝该请求。 如果找到了回复 URI，则使用整个字符串重定向用户，包括静态查询参数。

动态回复 Uri 仍被禁止，因为它们表示安全风险，不能用于在身份验证请求中保留状态信息。 为此，请使用`state`参数。

目前，Azure 门户的应用注册屏幕仍会阻止查询参数。 但是，您可以手动编辑应用程序清单以在应用程序中添加和测试查询参数。 有关详细信息，请参阅[身份验证的新增功能？](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)。

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>现在可通过 PowerShell Cmdlet 使用 Azure AD 的活动日志（MS Graph Api）

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们很高兴地宣布 Azure AD 活动日志（审核和登录报告）现已通过 Azure AD PowerShell 模块提供。 以前，你可以使用 MS 图形 API 终结点创建自己的脚本，现在我们已将该功能扩展到 PowerShell cmdlet。

有关如何使用这些 cmdlet 的详细信息，请参阅[Azure AD PowerShell cmdlet 用于报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)。

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>更新了 Azure AD 中的审核和登录日志的筛选器控件

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们更新了审核和登录日志报告，因此你现在可以应用多个筛选器，而无需将它们作为列添加到报表屏幕中。 此外，您现在可以决定要在屏幕上显示多少个筛选器。 这些更新将全部工作，以使报表更易于阅读，并使其更适合你的需求。

有关这些更新的详细信息，请参阅[筛选审核日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs)和[筛选登录活动](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)。

---

## <a name="june-2019"></a>2019 年 7 月

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>适用于 Microsoft Graph 的新 riskDetections API （公共预览版）

**类型：** 新功能  
**服务类别：** Identity Protection  
**产品功能：** 标识安全性和保护

我们很高兴地宣布推出新的 riskDetections API，以便 Microsoft Graph 现在为公共预览版。 可以使用此新 API 查看组织的标识保护相关用户和登录风险检测的列表。 你还可以使用此 API 更有效地查询你的风险检测，包括有关检测类型、状态和级别等的详细信息。

有关详细信息，请参阅[风险检测 API 参考文档](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年6月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年6月，我们已将这22个新应用和联合支持添加到应用库：

[AZURE AD SAML 工具包](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial)、 [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial)、 [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial)、 [Azure VPN 客户端](https://portal.azure.com/)、 [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial)、 [helper helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)、 [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial)、 [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial)、 [Mercedes](https://me.secure.mercedes-benz.com/)、Benz、[Skore](https://app.justskore.it/)、[Oracle 云基础结构控制台](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), oracle Access Manager for oracle零售促销营销, oracle Access Manager for Oracle 电子商务套件, oracle IDCS for 电子商务套件, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动执行这些新支持的 SaaS 应用的用户帐户预配

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 监视和报告

你现在可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>查看 Azure AD 预配服务的实时进度

**类型：** 已更改的功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理

我们更新了 Azure AD 预配体验，以包含一个新的进度栏，显示你在用户预配过程中的距离。 此更新的体验还提供了有关在当前周期中预配的用户数以及已设置为 "日期" 的用户数的信息。

有关详细信息，请参阅[检查用户预配的状态](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)。

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>公司品牌现在显示在 "注销" 和 "错误" 屏幕上

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

我们更新了 Azure AD，使公司品牌现在显示在 "注销" 和 "错误" 屏幕以及登录页面上。 无需执行任何操作即可启用此功能，Azure AD 只需使用已在 Azure 门户的 "**公司品牌**" 区域中设置的资产。

有关设置公司品牌的详细信息，请参阅[将品牌添加到组织的 Azure Active Directory 页面](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)。

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure 多重身份验证（MFA）服务器不再适用于新部署

**类型：** 已弃用  
**服务类别：** MFA  
**产品功能：** 标识安全性和保护

从2019年7月1日起，Microsoft 将不再为新部署提供 MFA 服务器。 希望在其组织中需要多重身份验证的新客户现在必须使用基于云的 Azure 多重身份验证。 在7月1日前激活 MFA 服务器的客户不会看到更改。 你仍可以下载最新版本，获取将来的更新，并生成激活凭据。

有关详细信息，请参阅[Azure 多重身份验证服务器](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)入门。 有关基于云的 Azure 多重身份验证的详细信息，请参阅[规划基于云的 Azure 多重身份验证部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)。

---

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>服务更改：未来仅支持应用程序代理服务上的 TLS 1.2 协议

**类型：** 更改计划  
**服务类别：** 应用代理  
**产品功能：** 访问控制

为帮助我们的客户提供一流的加密，我们仅限制对应用程序代理服务上的 TLS 1.2 协议的访问。 此更改将逐步推出给已仅使用 TLS 1.2 协议的客户，因此你不会看到任何更改。

弃用 TLS 1.0 和 TLS 1.1 的情况在8月 2019 31 日发生，但我们将提供额外的高级通知，因此你有时间为此更改做好准备。 为准备此更改，请确保客户端服务器和浏览器-服务器组合（包括用户用于访问通过应用程序代理发布的应用的任何客户端）都已更新为使用 TLS 1.2 协议来维护与应用程序的连接代理服务。 有关详细信息，请参阅[在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)。

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>使用使用量和 insights 报表查看与应用相关的登录数据

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 监视和报告

你现在可以使用 "Azure 门户的"**企业应用程序**"区域中的" 使用情况和 insights "报表，以应用程序为中心查看登录数据，其中包括有关以下内容的信息：

- 适用于你的组织的热门应用

- 登录失败次数最多的应用程序

- 每个应用的最常见登录错误

有关此功能的详细信息，请参阅[Azure Active Directory 门户中的使用情况和见解报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>使用 Azure AD 自动完成用户预配到云应用

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 监视和报告

请按照以下新教程使用 Azure AD 预配服务，为以下基于云的应用自动创建、删除和更新用户帐户：

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

你还可以遵循这个新的[Dropbox 教程](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)，该教程提供了有关如何预配组对象的信息。

有关如何通过自动用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>标识安全分数现已在 Azure AD 中提供（正式发布）

**类型：** 新功能  
**服务类别：** 不可用  
**产品功能：** 标识安全性和保护

你现在可以通过使用 Azure AD 中的 "标识" 安全分数功能来监视和改善标识安全状况。 标识安全分数功能使用单个仪表板来帮助你：

- 根据1到223之间的评分，客观地度量标识的安全状况。

- 规划标识安全改进

- 查看安全改进的成功率

有关标识安全分数功能的详细信息，请参阅 " [Azure Active Directory 中的标识安全分数是多少？"](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)。

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>现已推出新的应用注册体验（正式发布）

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 开发人员体验

全新的[应用注册](https://aka.ms/appregistrations)体验现已正式推出。 这种新体验包括你在 Azure 门户和应用程序注册门户中熟悉的所有关键功能，并通过以下方式改善它们：

- **更好地管理应用程序。** 你现在可以在一个位置看到所有应用，而不是跨不同门户查看应用。

- **简化的应用注册。** 从改进的导航体验到改进权限选择体验，现在可以更轻松地注册和管理应用。

- **更多详细信息。** 您可以找到有关您的应用程序的更多详细信息，包括快速入门指南等。

有关详细信息，请参阅[Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/)和[应用注册体验现已正式发布！](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 博客公告。

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>用于标识保护的有风险用户 API 中可用的新功能

**类型：** 新功能  
**服务类别：** Identity Protection  
**产品功能：** 标识安全性和保护

我们很高兴地宣布，你现在可以使用有风险的用户 API 来检索用户的风险历史记录、解除有风险的用户以及确认用户是否受到侵害。 此更改有助于更有效地更新用户的风险状态并了解其风险历史记录。

有关详细信息，请参阅有[风险的用户 API 参考文档](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD 应用库中提供了新的联合应用-可能为2019

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

在5月2019，我们已将这21个新应用和联合支持添加到应用库：

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial)， [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial)， [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/)，[简单符号](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial)， [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial)， [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial)， [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial)， [Marketo 销售接洽](https://toutapp.com/login)， [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial)， [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial)， [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial)，[量子工作区](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial)，[钴](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial)， [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)， [RedFlag](https://pocketstop.com/redflag/)， [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial)， [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial)， [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial)， [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial)， [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial)， [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>在 Azure AD 门户中改进了组的创建和管理体验

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

我们已改进了 Azure AD 门户中与组相关的体验。 这些改进使管理员能够更好地管理组列表、成员列表，并提供其他创建选项。

改进包括：

- 按成员身份类型和组类型进行的基本筛选。

- 添加新列，如源和电子邮件地址。

- 可以选择多个组、成员和所有者列表以便于删除。

- 可以选择电子邮件地址，并在创建组的过程中添加所有者。

有关详细信息，请参阅[使用 Azure Active Directory 创建基本组和添加成员](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>在 Azure AD 门户中为 Office 365 组配置命名策略（正式发布）

**类型：** 已更改的功能  
**服务类别：** 组管理  
**产品功能：** 协作

管理员现在可以使用 Azure AD 门户配置 Office 365 组的命名策略。 此更改有助于为组织中的用户创建或编辑的 Office 365 组强制实施一致的命名约定。

可以通过两种不同的方式为 Office 365 组配置命名策略：

- 定义自动添加到组名称中的前缀或后缀。

- 为你的组织上载一组自定义的阻止字词，这些字词在组名称中是不允许的（例如，"CEO，工资单，HR"）。

有关详细信息，请参阅为[Office 365 组强制实施命名策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API 终结点现可用于 Azure AD 活动日志（正式发布）

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们非常高兴地宣布 Microsoft Graph API 终结点支持 Azure AD 活动日志。 在此版本中，现在可以使用 Azure AD 审核日志的1.0 版，以及登录日志 Api。

有关详细信息，请参阅[Azure AD 审核日志 API 概述](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)。

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>管理员现在可以将条件访问用于合并的注册过程（公共预览版）

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全性和保护  

管理员现在可以创建条件性访问策略以供组合注册页面使用。 这包括在以下情况下应用策略以允许注册：

- 用户处于受信任的网络中。

- 用户的登录风险较低。

- 用户在被管理的设备上。

- 用户同意组织的使用条款（TOU）。

有关条件访问和密码重置的详细信息，请参阅[Azure AD 组合 MFA 和密码重置注册体验博客文章的条件性访问](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)。 有关组合注册过程的条件性访问策略的详细信息，请参阅[组合注册的条件性访问策略](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)。 有关 Azure AD 使用条款功能的详细信息，请参阅[Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>新 Azure AD 威胁情报检测现在作为 Azure AD Identity Protection 的一部分提供

**类型：** 新功能  
**服务类别：** Azure AD Identity Protection  
**产品功能：** 标识安全性和保护

Azure AD 威胁情报检测现在作为更新的 Azure AD Identity Protection 功能的一部分提供。 这一新功能可以帮助为特定用户或活动指出异常的用户活动，该活动与基于 Microsoft 内部和外部威胁智能源的已知攻击模式一致。

有关 Azure AD Identity Protection 的刷新版本的详细信息，请参阅[公共预览博客中的四个主要 Azure AD Identity Protection 增强功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935)，以及[Azure Active Directory Identity Protection （已刷新）是什么？](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) 下文. 有关 Azure AD 威胁智能检测的详细信息，请参阅[Azure Active Directory Identity Protection 风险检测](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence)一文。

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD 授权管理现已推出（公共预览版）

**类型：** 新功能  
**服务类别：** 标识监管  
**产品功能：** 标识监管

Azure AD 的权利管理，现在提供公共预览版，可帮助客户委托访问包的管理，该管理包定义了员工和业务合作伙伴如何请求访问权限、必须批准的人员以及他们有权访问的时间长短。 访问包可以管理 Azure AD 和 Office 365 组中的成员身份、企业应用程序中的角色分配以及 SharePoint Online 网站的角色分配。 有关权利管理的详细信息，请参阅[Azure AD 的权利管理概述](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)。 若要详细了解 Azure AD Identity Governance 功能的广度，包括 Privileged Identity Management、访问评审和使用条款，请参阅[什么是 Azure AD Identity Governance？](../governance/identity-governance-overview.md)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>在 Azure AD 门户中为 Office 365 组配置命名策略（公共预览版）

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

管理员现在可以使用 Azure AD 门户配置 Office 365 组的命名策略。 此更改有助于为组织中的用户创建或编辑的 Office 365 组强制实施一致的命名约定。

可以通过两种不同的方式为 Office 365 组配置命名策略：

- 定义自动添加到组名称中的前缀或后缀。

- 为你的组织上载一组自定义的阻止字词，这些字词在组名称中是不允许的（例如，"CEO，工资单，HR"）。

有关详细信息，请参阅为[Office 365 组强制实施命名策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD 活动日志现在 Azure Monitor 中可用（公开上市）

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

为了帮助解决有关使用 Azure AD 活动日志的可视化效果的反馈，我们在 Log Analytics 中引入了一个新的 Insights 功能。 此功能可帮助你使用称为工作簿的交互式模板获取有关 Azure AD 资源的见解。 这些预建工作簿可以提供应用程序或用户的详细信息，包括：

- **登录。** 提供应用和用户的详细信息，包括登录位置、正在使用的操作系统或浏览器客户端和版本，以及成功或失败的登录数。

- **旧身份验证和条件访问。** 提供有关使用旧身份验证的应用和用户的详细信息，包括条件访问策略触发的多重身份验证使用情况、使用条件性访问策略的应用等。

- **登录失败分析。** 帮助你确定登录错误是否是由于用户操作、策略问题或基础结构而发生的。

- **自定义报表。** 你可以创建新的工作簿或编辑现有工作簿，以帮助为你的组织自定义 Insights 功能。

有关详细信息，请参阅[如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年4月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年4月，我们已向应用程序库添加了以下21个具有联合支持的新应用：

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial)， [HRworks 单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)， [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial)， [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial)， [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)， [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial)， [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial)， [MileIQ](https://mileiq.onelink.me/991934284/7e980085)， [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial)， [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial)， [RStudio 连接](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial)、 [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial)、 [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial)、 [ALIBABA Cloud （基于角色的 SSO）](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial)、 [Certent 权益管理](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial)、 [Sectigo 证书管理器](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial)、 [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial)、 [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial)、 [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial)、[SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial)、 [Indiggo](https://indiggolead.com/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>新的访问评审频率选项和多个角色选择

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 标识监管

Azure AD 访问评审中的新更新可让你：

- 除了每周、每月、每季度和每年的现有选项外，还可以将访问评审的频率更改为**半年**。

- 创建单个访问评审时，请选择多个 Azure AD 和 Azure 资源角色。 在这种情况下，所有角色均设置为具有相同的设置，并同时通知所有审阅者。

有关如何创建访问评审的详细信息，请参阅[在 Azure AD 访问评审中创建组或应用程序的访问评审](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)。

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect 电子邮件警报系统正在转换，为某些客户发送新的电子邮件发件人信息

**类型：** 已更改的功能  
**服务类别：** AD Sync  
**产品功能：** 平台

Azure AD Connect 正在转换电子邮件警报系统，可能会向某些客户显示新的电子邮件发件人。 若要解决此情况，你`azure-noreply@microsoft.com`必须将添加到你的组织的允许列表，否则无法继续接收来自 Office 365、Azure 或你的同步服务的重要警报。

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN 后缀更改现在在 Azure AD Connect 中的联合域之间已成功完成

**类型：** 已修复  
**服务类别：** AD Sync  
**产品功能：** 平台

你现在可以成功地将用户的 UPN 后缀从一个联合域更改为 Azure AD Connect 中的另一个联合域。 此修补程序意味着在同步周期内您不会再遇到 FederatedDomainChangeError 错误消息，也不会收到通知电子邮件，指出，"无法在 Azure Active Directory 中更新此对象，因为属性 [FederatedUser] 无效。 更新本地目录服务中的值。

有关详细信息，请参阅[在同步过程中解决错误](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)。

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>在 Azure AD 中使用基于应用保护的条件性访问策略提高了安全性（公共预览版）

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全性和保护

现在，可以使用 "**需要应用保护**策略" 来使用基于应用保护的条件性访问。 这一新策略有助于防止以下情况，从而提高组织的安全性：

- 无 Microsoft Intune 许可证即可访问应用的用户。

- 用户无法获取 Microsoft Intune 的应用保护策略。

- 用户访问应用时，无需配置 Microsoft Intune 应用保护策略。

有关详细信息，请参阅[如何使用条件性访问来要求适用于云应用访问的应用保护策略](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)。

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge 中对 Azure AD 单一登录和条件访问的全新支持（公共预览版）

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全性和保护

我们已增强了对 Microsoft Edge 的 Azure AD 支持，包括提供对 Azure AD 单一登录和条件访问的全新支持。 如果以前使用过 Microsoft Intune Managed Browser，现在可以改为使用 Microsoft Edge。

有关使用条件性访问设置和管理设备和应用的详细信息，请参阅要求使用条件性访问的[云应用访问托管设备](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)，并[需要使用条件性访问的批准的客户端应用进行云应用访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 若要详细了解如何使用 Microsoft Intune 策略来管理使用 Microsoft Edge 的访问权限，请参阅[使用 Microsoft Intune 策略保护的浏览器管理 Internet 访问](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

---
