---
title: 将 Azure AD 与应用集成入门 | Microsoft Docs
description: 本文是一篇入门指南，介绍如何将 Azure Active Directory (AD) 与本地应用程序和云应用程序集成。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11453ad9132664313df2b56cb3664512628a16ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108240"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>将 Azure Active Directory 与应用程序集成入门指南

本主题概述了将应用程序与 Azure Active Directory (AD) 进行集成的流程。 以下各部分包含对一个更详细主题的简要汇总，方便用户确定此入门指南的哪些部分属于相关内容。

若要下载详细的部署计划，请参阅[后续步骤](#next-steps)。

## <a name="take-inventory"></a>获取清单
在将应用程序与 Azure AD 进行集成之前，必须知道你所在的位置，以及要前往的位置。  以下问题旨在帮助你思考 Azure AD 应用程序集成项目。

### <a name="application-inventory"></a>应用程序盘点
* 所有应用程序的所在位置？ 其所有者是谁？
* 应用程序需要哪种身份验证？
* 谁需要访问哪些应用程序？
* 是否要部署新应用程序？
  * 是否要在公司内部构建应用程序并将其部署在 Azure 计算实例上？
  * 是否要使用 Azure 应用程序库中提供的某个应用程序？

### <a name="user-and-group-inventory"></a>用户和组盘点
* 用户帐户位于哪个位置？
  * 本地 Active Directory
  * Azure AD
  * 在拥有的独立应用程序数据库中
  * 在未经认可的应用程序中
  * 以上都是
* 各个用户当前有哪些权限和角色分配？ 是否需要审查其访问权限，或者是否确定用户当前的访问和角色分配适当？
* 是否已在本地 Active Directory 中建立组？
  * 组的组织方式如何？
  * 有哪些组成员？
  * 组当前有哪些权限/角色分配？
* 是否需要在集成之前清理用户/组数据库？  （垃圾的进出是一个 相当重要的问题。）

### <a name="access-management-inventory"></a>访问管理盘点
* 当前如何管理用户对应用程序的访问？ 是否需要做出更改？  是否考虑过使用其他方式来管理访问，例如使用 [RBAC](../../role-based-access-control/role-assignments-portal.md)？
* 谁需要访问哪些应用程序？

也许你最初无法回答所有这些问题，但没有关系。  本指南可帮助你回答其中一些问题并做出一些明智的决策。

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>使用 Cloud Discovery 查找未批准的云应用程序

如上所述，可能有些应用程序到目前为止仍不受组织的管理。  在盘点过程中，可以查找未经认可的云应用程序。 请参阅[设置 Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)。

## <a name="integrating-applications-with-azure-ad"></a>将应用程序与 Azure AD 集成
以下文章介绍了将应用程序与 Azure AD 集成的不同方法，并提供了一些指导。

* [确定要使用的 Active Directory](../fundamentals/active-directory-administer.md)
* [使用 Azure 应用程序库中的应用程序](what-is-single-sign-on.md)
* [集成 SaaS 应用程序教程列表](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>身份验证类型
每个应用程序可能有不同的身份验证要求。 借助 Azure AD，可对使用 SAML 2.0、WS 联合身份验证或 OpenID Connect 协议以及密码单一登录的应用程序使用签名证书。 有关可用于 Azure AD 的应用程序身份验证类型的详细信息，请参阅[在 Azure Active Directory 中管理用于联合单一登录的证书](manage-certificates-for-federated-single-sign-on.md)和[基于密码的单一登录](what-is-single-sign-on.md)。

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>使用 Azure AD 应用代理启用 SSO
使用 Microsoft Azure AD 应用程序代理，可以从任何位置和任何设备安全访问专用网络中的应用程序。 在环境中安装应用程序代理连接器后，可以使用 Azure AD 轻松配置该连接器。

### <a name="integrating-custom-applications"></a>集成自定义应用程序
如果正在编写新应用程序，并想要协助开发人员利用 Azure AD 的强大功能，请参阅[指导开发人员](../active-directory-applications-guiding-developers-for-lob-applications.md)。

如果想要将自定义应用程序添加到 Azure 应用程序库，请参阅[使用 Azure AD 自助 SAML 配置加入自己的应用](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)。

## <a name="managing-access-to-applications"></a>管理对应用程序的访问
以下文章介绍了在使用 Azure AD 连接器和 Azure AD 将应用程序与 Azure AD 集成之后，如何管理对应用程序的访问。

* [使用 Azure AD 管理对应用的访问](what-is-access-management.md)
* [使用 Azure AD 连接器自动化](user-provisioning.md)
* [将用户分配到应用程序](../active-directory-applications-guiding-developers-assigning-users.md)
* [将组分配到应用程序](../active-directory-applications-guiding-developers-assigning-groups.md)
* [共享帐户](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>后续步骤
若要了解详细信息，可以从 [GitHub](https://aka.ms/deploymentplans) 下载 Azure Active Directory 部署计划。 对于库应用程序，您可以下载上单一登录、 条件性访问和用户通过预配的部署计划[Azure 门户](https://portal.azure.com)。 

若要从 Azure 门户下载部署计划，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择“企业应用程序” | “选取应用” | “部署计划”。   

通过完成[部署计划调查](https://aka.ms/DeploymentPlanFeedback)来提供有关部署计划的反馈。
