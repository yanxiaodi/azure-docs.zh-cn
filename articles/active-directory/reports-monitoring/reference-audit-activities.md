---
title: Azure Active Directory (Azure AD) 审核活动参考 | Microsoft Docs
description: 大致了解可以在 Azure Active Directory (Azure AD) 的审核日志中记录的审核活动。
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: aaa60a7737b7781a21e23516d139332f10bdf448
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306005"
---
# <a name="azure-ad-audit-activity-reference"></a>Azure AD 审核活动参考

通过 Azure Active Directory (Azure AD) 报告，可以获取确定环境运行状况所需的信息。

Azure AD 中的报告体系结构由以下部分组成：

- **活动报告** 
    - [登录](concept-sign-ins.md) - 提供有关托管应用程序的使用情况和用户登录活动的信息
    - [审核日志](concept-audit-logs.md) - 通过日志为 Azure AD 中的各种功能所做的所有更改提供可跟踪性。 
    
- **安全报表** 
    - [风险登录](concept-risky-sign-ins.md) - 风险登录是指可能由非用户帐户合法拥有者进行的登录尝试。 
    - [已标记为存在风险的用户](concept-user-at-risk.md) - 风险用户是指可能已泄露的用户帐户。 

本文列出了可以在审核日志中记录的审核活动。

## <a name="access-reviews"></a>访问审阅

|审核类别|activities|
|---|---|
|访问评审|访问评审结束|
|访问评审|向请求审核添加审核者|
|访问评审|向访问评审添加评审者|
|访问评审|应用访问评审|
|访问评审|创建访问评审|
|访问评审|创建程序|
|访问评审|创建请求审核|
|访问评审|删除访问评审|
|访问评审|删除程序|
|访问评审|链接程序控件|
|访问评审|载入到 Azure AD 访问评审|
|访问评审|从访问评审删除评审者|
|访问评审|请求停止评审|
|访问评审|请求应用评审结果|
|访问评审|评审 Rbac 角色成员身份|
|访问评审|评审应用分配|
|访问评审|评审组成员身份|
|访问评审|评审请求审核请求|
|访问评审|取消链接程序控件|
|访问评审|更新访问评审|
|访问评审|更新 Azure AD 访问评审加入状态|
|访问评审|更新访问评审邮件通知设置|
|访问评审|更新访问评审再评计数设置|
|访问评审|更新以天为单位的访问评审再评持续时间设置|
|访问评审|更新访问评审再评结束类型设置|
|访问评审|更新访问评审再评类型设置|
|访问评审|更新访问评审提醒设置|
|访问评审|更新程序|
|访问评审|更新请求审核|
|访问评审|用户已禁用|

## <a name="account-provisioning"></a>帐户预配

|审核类别|activities|
|---|---|
|应用程序管理|检索 V2 应用程序授权|
|应用程序管理|检索当前租户中的 V2 应用程序服务主体|
|应用程序管理|更新 V1 应用程序|
|应用程序管理|更新 V2 应用程序|
|应用程序管理|更新 V2 应用程序授权|
|应用程序管理|添加 OAuth2PermissionGrant|
|应用程序管理|向服务主体添加应用角色分配|

## <a name="application-proxy"></a>应用程序代理

|审核类别|activities|
|---|---|
|应用程序管理|添加应用程序|
|应用程序管理|将所有者添加到应用程序|
|应用程序管理|将所有者添加到服务主体|
|应用程序管理|将策略添加到服务主体|
|目录管理|添加服务主体|
|目录管理|添加服务主体凭据|
|目录管理|同意使用应用程序|
|目录管理|删除应用程序|
|目录管理|硬删除应用程序|
|目录管理|删除 OAuth2PermissionGrant|
|目录管理|从服务主体删除应用角色分配|
|目录管理|从应用程序中删除所有者|
|Resource|从服务主体中删除所有者|
|Resource|从服务主体中删除策略|
|Resource|删除服务主体|


## <a name="automated-password-rollover"></a>自动密码滚动更新

|审核类别|activities|
|---|---|
|应用程序管理|删除服务主体凭据|


## <a name="b2c"></a>B2C

|审核类别|activities|
|---|---|
|应用程序管理|还原应用程序|
|应用程序管理|吊销许可|
|应用程序管理|更新应用程序|
|应用程序管理|更新外部机密|
|应用程序管理|更新服务主体|
|应用程序管理|向应用程序颁发访问令牌|
|应用程序管理|向应用程序颁发授权代码|
|应用程序管理|向应用程序颁发 id_token|
|应用程序管理|验证本地帐户凭据|
|应用程序管理|验证用户身份验证|
|应用程序管理|添加 V2 应用程序权限|
|应用程序管理|将基于 ASCII 机密的密钥添加到 CPIM 密钥容器|
|应用程序管理|将密钥添加到 CPIM 密钥容器|
|应用程序管理|AdminPolicyDatas-SetResources|
|应用程序管理|AdminUserJourneys-GetResources|
|应用程序管理|AdminUserJourneys-RemoveResources|
|身份验证|AdminUserJourneys-SetResources|
|身份验证|创建 IdentityProvider|
|身份验证|创建 V1 应用程序|
|身份验证|创建 V2 应用程序|
|身份验证|在租户中创建自定义域|
|Authorization|创建新的 AdminUserJourney|
|Authorization|创建本地化资源 json|
|Authorization|创建新的自定义 IDP|
|Authorization|创建新的 IDP|
|Authorization|创建或更新 B2C 目录资源|
|Authorization|创建策略|
|Authorization|创建 trustFramework 策略|
|Authorization|创建前缀可配置的 trustFramework 策略|
|Authorization|创建用户属性|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|创建或更新新的 AdminUserJourney|
|Authorization|删除 IDP|
|Authorization|删除 IdentityProvider|
|Authorization|删除 V1 应用程序|
|Authorization|删除 V2 应用程序|
|Authorization|删除 V2 应用程序授权|
|Authorization|删除 B2C 目录资源|
|Authorization|删除 CPIM 密钥容器|
|Authorization|删除 trustFramework 策略|
|Authorization|删除用户属性|
|Authorization|启用 B2C 功能|
|Authorization|获取订阅中的 B2C 目录资源|
|Authorization|获取自定义 IDP|
|Authorization|获取 IDP|
|Authorization|获取 V1 和 V2 应用程序|
|Authorization|获取 V1 应用程序|
|Authorization|获取 V1 应用程序|
|Authorization|获取 V2 应用程序|
|Authorization|获取 V2 应用程序|
|Authorization|获取 B2C 目录资源|
|Authorization|获取租户中自定义域的列表|
|Authorization|获取用户旅程|
|Authorization|获取用户旅程允许的应用程序声明|
|Authorization|获取用户旅程允许的自断言声明|
|Authorization|获取允许的自断言策略声明|
|Authorization|获取可用的输出声明列表|
|Authorization|获取用户旅程的内容定义|
|Authorization|获取特定管理流的 IDP|
|Authorization|获取 JWK 格式的密钥容器活动密钥元数据|
|Authorization|获取所有管理流的列表|
|Authorization|获取所有用户的所有管理流的标记列表|
|Authorization|获取用户的租户列表|
|Authorization|获取本地帐户的自断言声明|
|Authorization|获取本地化资源 json|
|Authorization|获取 Microsoft.AzureActiveDirectory 资源提供程序的操作|
|Authorization|获取策略|
|Authorization|获取策略|
|Authorization|获取租户的资源属性|
|Authorization|获取受支持的 IDP 列表|
|Authorization|获取用户旅程的受支持的 IDP 列表|
|Authorization|获取租户信息|
|Authorization|获取租户允许的功能|
|Authorization|获取租户定义的自定义 IDP 列表|
|Authorization|获取租户定义的 IDP 列表|
|Authorization|获取租户定义的本地 IDP 列表|
|Authorization|获取用户的租户详细信息，以便创建资源|
|Authorization|获取租户列表|
|Authorization|获取 tenantDomains|
|Authorization|获取默认的 CPIM 支持的区域性|
|Authorization|获取管理流的详细信息|
|Authorization|获取此租户的 UserJourneys 的列表|
|Authorization|获取 CPIM 支持的可用区域性的集合|
|Authorization|获取 trustFramework 策略|
|Authorization|获取 XML 格式的 trustFramework 策略|
|Authorization|获取用户属性|
|Authorization|获取用户属性|
|Authorization|获取用户旅程列表|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|获取 jwk 格式的 CPIM 密钥容器|
|Authorization|获取租户中密钥容器的列表|
|Authorization|获取租户的类型|
|Authorization|MigrateTenantMetadata|
|Authorization|修补 IdentityProvider|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|删除用户旅程|
|Authorization|还原 CPIM 密钥容器备份|
|Authorization|检索 V2 应用程序授权|
|Authorization|检索当前租户中的 V2 应用程序服务主体|
|Authorization|更新自定义 IDP|
|Authorization|更新 IDP|
|Authorization|更新本地 IDP|
|Authorization|更新 V1 应用程序|
|Authorization|更新 V2 应用程序|
|Authorization|更新 V2 应用程序授权|
|Authorization|更新策略|
|Authorization|更新用户属性|
|Authorization|上传 CPIM 加密密钥|
|Authorization|用户授权：对租户功能集禁用了 API|
|Authorization|用户授权：为用户授予了“租户管理员”访问权限|
|Authorization|用户授权：为用户授予了“已验证用户”访问权限|
|Authorization|验证是否已启用 B2C 功能|
|Authorization|验证是否已启用功能|
|Authorization|创建程序|
|Authorization|删除程序|
|Authorization|链接程序控件|
|Authorization|载入到 Azure AD 访问评审|
|Authorization|取消链接程序控件|
|Authorization|更新程序|
|Authorization|禁用桌面 SSO|
|Authorization|禁用特定域的桌面 SSO|
|Authorization|禁用应用程序代理|
|Authorization|禁用直通身份验证|
|Authorization|启用桌面 SSO|
|目录管理|启用特定域的桌面 SSO|
|目录管理|启用应用程序代理|
|目录管理|启用直通身份验证|
|目录管理|在租户中创建自定义域|
|目录管理|启用 B2C 功能|
|目录管理|获取租户中自定义域的列表|
|目录管理|获取租户的资源属性|
|目录管理|获取租户信息|
|目录管理|获取租户允许的功能|
|目录管理|获取 tenantDomains|
|Key|获取租户的类型|
|Key|验证是否已启用 B2C 功能|
|Key|验证是否已启用功能|
|Key|将合作伙伴添加到公司|
|Key|添加未验证的域|
|Key|添加已验证的域|
|Key|创建公司|
|Key|创建公司设置|
|Key|删除公司设置|
|Key|降级合作伙伴|
|Key|目录已删除|
|其他|目录已永久删除|
|其他|目录已计划删除|
|Resource|将公司提升为合作伙伴|
|Resource|清除 Rights Management 属性|
|Resource|从公司中删除合作伙伴|
|Resource|删除未验证的域|
|Resource|删除已验证的域|
|Resource|设置公司信息|
|Resource|设置 DirSync 功能|
|Resource|设置 DirSyncEnabled 标志|
|Resource|设置合作关系|
|Resource|设置意外删除阈值|
|Resource|设置公司允许的数据位置|
|Resource|设置公司跨国功能已启用|
|Resource|在租户上设置目录功能|
|Resource|设置域身份验证|
|Resource|在域中设置联合设置|
|Resource|设置密码策略|
|Resource|设置 Rights Management 属性|
|Resource|更新公司|
|Resource|更新公司设置|
|Resource|更新域|
|Resource|验证域|
|Resource|验证电子邮件验证域|
|Resource|加入|
|Resource|更新警报设置|
|Resource|更新每周摘要设置|
|Resource|对目录禁用密码写回|
|Resource|对目录启用密码写回|
|Resource|向组添加应用角色分配|
|Resource|添加组|
|Resource|将成员添加到组|
|Resource|将所有者添加到组|
|Resource|创建组设置|
|Resource|删除组|
|Resource|删除组设置|
|Resource|完成向用户应用基于组的许可证|
|Resource|硬删除组|
|Resource|从组删除应用角色分配|
|Resource|从组中删除成员|
|Resource|从组中删除所有者|
|Resource|还原组|
|Resource|设置组许可证|
|Resource|已设置要由用户管理的组|
|Resource|开始向用户应用基于组的许可证|
|Resource|触发组许可证重新计算|
|Resource|更新组|
|Resource|更新组设置|
|Resource|添加成员|
|Resource|创建组|
|Resource|删除组|
|Resource|移除成员|
|Resource|更新组|
|Resource|批准要求加入组的挂起请求|
|Resource|取消要求加入组的挂起请求|
|Resource|创建生命周期管理策略|
|Resource|删除要求加入组的挂起请求|
|Resource|拒绝要求加入组的挂起请求|
|Resource|续订组|
|Resource|请求加入组|
|Resource|设置动态组属性|
|Resource|更新生命周期管理策略|
|Resource|将基于 ASCII 机密的密钥添加到 CPIM 密钥容器|
|Resource|将密钥添加到 CPIM 密钥容器|
|Resource|删除 CPIM 密钥容器|
|Resource|删除密钥容器|
|Resource|获取 JWK 格式的密钥容器活动密钥元数据|
|Resource|获取密钥容器元数据|
|Resource|获取 jwk 格式的 CPIM 密钥容器|
|Resource|获取租户中密钥容器的列表|
|Resource|还原 CPIM 密钥容器备份|
|Resource|保存密钥容器|
|Resource|上传 CPIM 加密密钥|
|Resource|向应用程序颁发授权代码|
|Resource|向应用程序颁发 id_token|


## <a name="core-directory"></a>核心目录

|审核类别|activities|
|---|---|
|管理单元管理|下载单个风险检测类型|
|管理单元管理|下载选择加入的每周摘要的管理和状态|
|管理单元管理|下载所有风险检测类型|
|管理单元管理|下载免费的用户风险检测|
|管理单元管理|下载已标记为存在风险的用户|
|应用程序管理|已处理批量邀请|
|应用程序管理|已上传批量邀请|
|应用程序管理|将所有者添加到策略|
|应用程序管理|添加策略|
|应用程序管理|删除策略|
|应用程序管理|删除策略凭据|
|应用程序管理|更新策略|
|应用程序管理|设置 MFA 注册策略|
|应用程序管理|设置登录风险策略|
|应用程序管理|设置用户风险策略|
|应用程序管理|接受使用条款|
|应用程序管理|创建使用条款|
|应用程序管理|拒绝使用条款|
|应用程序管理|删除使用条款|
|应用程序管理|编辑使用条款|
|应用程序管理|发布使用条款|
|应用程序管理|取消发布使用条款|
|应用程序管理|添加应用程序 SSL 证书|
|应用程序管理|删除 SSL 绑定|
|应用程序管理|注册连接器|
|应用程序管理|AdminPolicyDatas-RemoveResources|
|应用程序管理|AdminPolicyDatas-SetResources|
|应用程序管理|AdminUserJourneys-GetResources|
|目录管理|AdminUserJourneys-RemoveResources|
|目录管理|AdminUserJourneys-SetResources|
|目录管理|创建 IdentityProvider|
|目录管理|创建新的 AdminUserJourney|
|目录管理|创建本地化资源 json|
|目录管理|创建新的自定义 IDP|
|目录管理|创建新的 IDP|
|目录管理|创建或更新 B2C 目录资源|
|目录管理|创建策略|
|目录管理|创建 trustFramework 策略|
|目录管理|创建前缀可配置的 trustFramework 策略|
|目录管理|创建用户属性|
|目录管理|CreateTrustFrameworkPolicy|
|目录管理|删除 IDP|
|目录管理|删除 IdentityProvider|
|目录管理|删除 B2C 目录资源|
|目录管理|删除 trustFramework 策略|
|目录管理|删除用户属性|
|目录管理|获取资源组中的 B2C 目录资源|
|目录管理|获取订阅中的 B2C 目录资源|
|目录管理|获取自定义 IDP|
|目录管理|获取 IDP|
|目录管理|获取 B2C 目录资源|
|目录管理|获取用户旅程|
|目录管理|获取用户旅程允许的应用程序声明|
|目录管理|获取用户旅程允许的自断言声明|
|目录管理|获取允许的自断言策略声明|
|目录管理|获取可用的输出声明列表|
|目录管理|获取用户旅程的内容定义|
|目录管理|获取特定管理流的 IDP|
|目录管理|获取所有管理流的列表|
|目录管理|获取所有用户的所有管理流的标记列表|
|组管理|大容量下载组成员-已启动|
|组管理|批量下载组成员-已完成|
|组管理|大容量导入组成员-已启动|
|组管理|大容量导入组成员-已完成|
|组管理|批量删除组成员-已启动|
|组管理|批量删除组成员-已完成|
|组管理|大容量下载组-已启动|
|组管理|大容量下载组-已完成|
|组管理|获取用户的租户列表|
|组管理|获取本地帐户的自断言声明|
|组管理|获取本地化资源 json|
|组管理|获取 Microsoft.AzureActiveDirectory 资源提供程序的操作|
|组管理|获取策略|
|组管理|获取策略|
|组管理|获取受支持的 IDP 列表|
|组管理|获取用户旅程的受支持的 IDP 列表|
|组管理|获取租户定义的自定义 IDP 列表|
|组管理|获取租户定义的 IDP 列表|
|组管理|获取租户定义的本地 IDP 列表|
|组管理|获取用户的租户详细信息，以便创建资源|
|组管理|获取默认的 CPIM 支持的区域性|
|组管理|获取管理流的详细信息|
|组管理|获取此租户的 UserJourneys 的列表|
|组管理|获取 CPIM 支持的可用区域性的集合|
|组管理|获取 trustFramework 策略|
|组管理|获取 XML 格式的 trustFramework 策略|
|组管理|获取用户属性|
|策略管理|获取用户属性|
|策略管理|获取用户旅程列表|
|策略管理|GetIEFPolicies|
|策略管理|GetIdentityProviders|
|策略管理|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|移动资源|
|Resource|修补 IdentityProvider|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|删除用户旅程|
|Resource|更新自定义 IDP|
|Resource|更新 IDP|
|Resource|更新本地 IDP|
|Resource|更新 B2C 目录资源|
|Resource|更新策略|
|Resource|更新订阅状态|
|角色管理|更新用户属性|
|角色管理|验证移动资源|
|角色管理|添加设备|
|角色管理|添加设备配置|
|角色管理|将注册的所有者添加到设备|
|角色管理|将注册的用户添加到设备|
|角色管理|删除设备|
|角色管理|删除设备配置|
|角色管理|设备不再合规|
|角色管理|设备不再托管|
|用户管理|AccessReview_Review|
|用户管理|AccessReview_Update|
|用户管理|ActivationAborted|
|用户管理|ActivationApproved|
|用户管理|ActivationCanceled|
|用户管理|ActivationRequested|
|用户管理|将符合条件的成员添加到角色|
|用户管理|将成员添加到角色|
|用户管理|将角色分配添加到角色定义|
|用户管理|从模板添加角色|
|用户管理|将带有范围的成员添加到角色|
|用户管理|已添加|
|用户管理|分配|
|用户管理|批量创建用户-已启动|
|用户管理|批量创建用户-已完成|
|用户管理|批量删除用户-已启动|
|用户管理|批量删除用户-已完成|
|用户管理|大容量下载用户-已启动|
|用户管理|大容量下载用户-已完成|
|用户管理|大容量还原已删除用户-已启动|
|用户管理|大容量还原已删除的用户-已完成|
|用户管理|批量邀请用户-已启动|
|用户管理|批量邀请用户-已完成|
|用户管理|从设备中删除注册的所有者|
|用户管理|从设备中删除注册的用户|
|用户管理|从角色中删除符合条件的成员|
|用户管理|删除角色中的成员|
|用户管理|从角色定义中删除角色分配|
|用户管理|从角色中删除带有范围的成员|
|用户管理|更新设备|
|用户管理|更新设备配置|
|用户管理|更新角色|






## <a name="identity-protection"></a>标识保护

|审核类别|activities|
|---|---|
|目录管理|提升|
|目录管理|已删除|
|目录管理|角色设置更改|
|其他|ScanAlertsNow|
|其他|注册|
|其他|停用|
|其他|UpdateAlertSettings|
|其他|UpdateCurrentState|
|策略管理|访问评审结束|
|策略管理|向请求审核添加审核者|
|策略管理|向访问评审添加评审者|
|用户管理|应用访问评审|
|用户管理|创建访问评审|


## <a name="invited-users"></a>受邀用户

|审核类别|activities|
|---|---|
|其他|创建请求审核|
|其他|删除访问评审|
|用户管理|从访问评审删除评审者|
|用户管理|请求应用评审结果|
|用户管理|请求停止评审|
|用户管理|评审应用分配|
|用户管理|评审组成员身份|
|用户管理|评审 Rbac 角色成员身份|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|审核类别|activities|
|---|---|
|组管理|评审请求审核请求|
|组管理|更新访问评审|
|组管理|更新访问评审邮件通知设置|
|组管理|更新访问评审再评计数设置|
|组管理|更新以天为单位的访问评审再评持续时间设置|
|用户管理|更新访问评审再评结束类型设置|
|用户管理|更新访问评审再评类型设置|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|审核类别|activities|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|已添加|
|PIM|AddedOutsidePIM|
|PIM|分配|
|PIM|DismissAlert|
|PIM|提升|
|PIM|ReactivateAlert|
|PIM|已删除|
|PIM|RemovedOutsidePIM|
|PIM|请求停止评审|
|PIM|角色设置更改|
|PIM|ScanAlertsNow|
|PIM|注册|
|PIM|取消分配|
|PIM|停用|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>自助组管理

|审核类别|activities|
|---|---|
|组管理|重置用户密码|
|组管理|还原用户|
|组管理|设置强制更改用户密码|
|组管理|设置用户管理器|
|组管理|设置用户 oath 令牌元数据功能已启用|
|组管理|更新 StsRefreshTokenValidFrom 时间戳|
|组管理|更新外部机密|
|组管理|更新用户|
|组管理|管理员生成临时密码|


## <a name="self-service-password-management"></a>自助服务密码管理

|审核类别|activities|
|---|---|
|目录管理|管理员要求用户重置其密码|
|目录管理|将外部用户分配到应用程序|
|用户管理|电子邮件未发送，用户已取消订阅|
|用户管理|邀请外部用户|
|用户管理|兑换外部用户邀请|
|用户管理|创建病毒性租户|
|用户管理|创建病毒性用户|
|用户管理|用户密码注册|
|用户管理|用户密码重置|
|用户管理|被阻止进行自助密码重置|


## <a name="terms-of-use"></a>使用条款

|审核类别|activities|
|---|---|
|使用条款|接受使用条款|
|使用条款|创建使用条款|
|使用条款|拒绝使用条款|
|使用条款|删除许可|
|使用条款|删除使用条款|
|使用条款|编辑使用条款|
|使用条款|到期使用条款|
|使用条款|硬删除使用条款|
|使用条款|发布使用条款|
|使用条款|取消发布使用条款|


## <a name="next-steps"></a>后续步骤

- [Azure AD 报告概述](overview-reports.md)。
- [审核日志报告](concept-audit-logs.md)。 
- [以编程方式访问 Azure AD 报告](concept-reporting-api.md)
