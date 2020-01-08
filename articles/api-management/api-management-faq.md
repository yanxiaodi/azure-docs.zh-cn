---
title: Azure API 管理常见问题解答 | Microsoft Docs
description: 了解 Azure API 管理中的常见问题解答 (FAQ)、模式和最佳做法。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 677e38f69729bba8caf1ec3f88b2e0a1a4f8c7e8
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073667"
---
# <a name="azure-api-management-faqs"></a>Azure API 管理常见问题
了解有关 Azure API 管理的常见问题解答、模式和最佳做法。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>联系我们
* [如何向 Microsoft Azure API 管理团队提问？](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>常见问题
* [功能处于预览中意味着什么？](#what-does-it-mean-when-a-feature-is-in-preview)
* [如何确保 API 管理网关和后端服务之间的连接安全？](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [如何将 API 管理服务实例复制到新实例？](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [是否可以编程方式管理 API 管理实例？](#can-i-manage-my-api-management-instance-programmatically)
* [如何向管理员组添加用户？](#how-do-i-add-a-user-to-the-administrators-group)
* [我想要添加的策略为何在策略编辑器中不可用？](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [如何在单个 API 中设置多个环境？](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [是否可将 SOAP 用于 API 管理？](#can-i-use-soap-with-api-management)
* [是否可以使用 AD FS 安全配置 OAuth 2.0 授权服务器？](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [API 管理使用何种路由方法部署到多个地理位置？](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [是否可以使用 Azure 资源管理器模板创建 API 管理服务实例？](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [是否可以为后端使用自签名 SSL 证书？](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [为何在尝试克隆 GIT 存储库时得到身份验证失败？](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [API 管理是否适用于 Azure ExpressRoute ？](#does-api-management-work-with-azure-expressroute)
* [为什么在 Resource Manager 样式 VNET 中部署 API 管理时需要专用子网？](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [在 VNET 中部署 API 管理时所需的最小子网大小是多少？](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [是否可将 API 管理服务从一个订阅移到另一个订阅？](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [导入 API 是否存在限制或已知问题？](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>如何向 Microsoft Azure API 管理团队提问？
可使用以下选项之一联系我们：

* 在 [API 管理 MSDN 论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)中发布问题。
* 向 <mailto:apimgmt@microsoft.com> 发送电子邮件。
* 在 [Azure 反馈论坛](https://feedback.azure.com/forums/248703-api-management)中向我们发送功能请求。

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>功能处于预览中意味着什么？
当功能处于预览中时，这意味着我们正在积极寻求关于功能效果如何的反馈。 处于预览中的功能具备完整功能，但我们可能为了响应客户反馈而进行重大更改。 我们建议不要在生产环境中依赖处于预览中的功能。 如果有任何关于预览功能的反馈，请通过[如何向 Microsoft Azure API 管理团队提问？](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)中的联系选项之一告知我们。

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>如何确保 API 管理网关和后端服务之间的连接安全？
有多个选项可确保 API 管理网关和后端服务之间的连接安全。 你可以：

* 使用 HTTP 基本身份验证。 有关详细信息，请参阅[导入并发布第一个 API](import-and-publish.md)。
* 使用[如何使用 Azure API 管理中的客户端证书身份验证确保后端服务安全](api-management-howto-mutual-certificates.md)中所述的 SSL 相互身份验证。
* 在后端服务上使用 IP 允许列表。 在 API 管理的所有层中 (消耗层除外), 网关的 IP 地址仍保持不变, 并在[IP 文档一文](api-management-howto-ip-addresses.md)中介绍了几个注意事项。
* 将 API 管理实例连接到 Azure 虚拟网络。

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>如何将 API 管理服务实例复制到新实例？
如果要将 API 管理实例复制到新实例，则有多个选项。 你可以：

* 使用 API 管理中的备份和还原功能。 有关详细信息，请参阅[如何使用 Azure API 管理中的服务备份和还原实现灾难恢复](api-management-howto-disaster-recovery-backup-restore.md)。
* 使用 [API 管理 REST API](/rest/api/apimanagement/) 创建自己的备份和还原功能。 使用 REST API 保存和还原所需的服务实例中的实体。
* 使用 Git 下载服务配置，然后将其上传到新实例。 有关详细信息，请参阅[如何使用 Git 保存和配置 API 管理服务](api-management-configuration-repository-git.md)。

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>是否可以编程方式管理 API 管理实例？
是，可使用以下内容以编程方式管理 API 管理：

* [API 管理 REST API](/rest/api/apimanagement/)。
* [Microsoft Azure API 管理服务管理库 SDK](https://aka.ms/apimsdk)。
* [服务部署](https://docs.microsoft.com/powershell/module/wds)和[服务管理](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) PowerShell cmdlet。

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>如何向管理员组添加用户？
下面是向管理员组添加用户的方法：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 转到具有要更新的 API 管理实例的资源组。
3. 在 API 管理中，将“API 管理服务参与者”角色分配给该用户。

现在，新添加的参与者可以使用 Azure PowerShell [cmdlet](https://docs.microsoft.com/powershell/azure/overview)。 下面是以管理员身份登录的方法：

1. 使用 `Connect-AzAccount` cmdlet 登录。
2. 使用 `Set-AzContext -SubscriptionID <subscriptionGUID>` 将上下文设置为具有该服务的订阅。
3. 使用 `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>` 获取单一登录 URL。
4. 使用 URL 访问管理员门户。

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>我想要添加的策略为何在策略编辑器中不可用？
如果要添加的策略在策略管理器中显示为变暗或有阴影，请确保处于该策略的正确范围内。 每个策略声明都设计为在特定范围和策略部分中使用。 若要查看策略部分和策略范围，请参阅 [API 管理策略](/azure/api-management/api-management-policies)中的策略的用法部分。

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>如何在单个 API 中设置多个环境？
若要在单个 API 中设置多个环境（例如，一个测试环境和一个生产环境），则有两个选项。 你可以：

* 在同一租户上托管不同的 API。
* 在不同租户上托管相同的 API。

### <a name="can-i-use-soap-with-api-management"></a>是否可将 SOAP 用于 API 管理？
当前已提供 [SOAP 传递](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/)支持。 管理员可以导入其 SOAP 服务的 WSDL，以便 Azure API 管理创建一个 SOAP 前端。 开发人员门户文档、测试控制台、策略和分析都可用于 SOAP 服务。

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>是否可以使用 AD FS 安全配置 OAuth 2.0 授权服务器？
若要了解如何使用 Active Directory 联合身份验证 (AD FS) 安全配置 OAuth 2.0 授权服务器，请参阅[在 API 管理中使用 ADFS](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)。

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>API 管理使用何种路由方法部署到多个地理位置？
API 管理使用[性能流量路由方法](../traffic-manager/traffic-manager-routing-methods.md#performance)部署到多个地理位置。 传入流量路由到最近的 API 网关。 如果一个区域处于脱机状态，则传入流量自动路由到下一个最近的网关。 在[流量管理器路由方法](../traffic-manager/traffic-manager-routing-methods.md)中了解有关路由方法的详细信息。

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>是否可以使用 Azure 资源管理器模板创建 API 管理服务实例？
是的。 请参阅[AZURE API 管理服务](https://aka.ms/apimtemplate)快速入门模板。

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>是否可以为后端使用自签名 SSL 证书？
是的。 可以通过 PowerShell 或直接提交到 API 完成此操作。 这将禁用证书链验证，并允许在从 API 管理与后端服务进行通信时使用自签名证书或私人签名证书。

#### <a name="powershell-method"></a>Powershell 方法 ####
使用 [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend)（适用于新后端）或 [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend)（适用于现有后端）PowerShell cmdlet 并将 `-SkipCertificateChainValidation` 参数设置为 `True`。 

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>直接 API 更新方法 ####
1. 使用 API 管理创建[后端](/rest/api/apimanagement/)实体。     
2. 将“skipCertificateChainValidation”属性设置为“true”。     
3. 如果不再希望允许自签名证书，请删除后端实体，或将“skipCertificateChainValidation”属性设置为“false”。

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>为何在尝试克隆 Git 存储库时得到身份验证失败？
如果使用 Git 凭据管理器，或者正在尝试使用 Visual Studio 克隆 Git 存储库，可能遇到“Windows 凭据”对话框的已知问题。 该对话框将密码长度限制为 127 个字符，并截断 Microsoft 生成的密码。 我们正致力于缩短密码。 暂时请使用 Git Bash 克隆 Git 存储库。

### <a name="does-api-management-work-with-azure-expressroute"></a>API 管理是否适用于 Azure ExpressRoute？
是的。 API 管理适用于 Azure ExpressRoute。

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>为什么在 Resource Manager 样式 VNET 中部署 API 管理时需要专用子网？
API 管理需要专用子网，因为它建立在经典（PAAS V1 层）部署模型之上。 虽然也可以在 Resource Manager VNET（V2 层）中进行部署，但这样会导致后续问题。 Azure 中的经典部署模型与 Resource Manager 模型结合不紧密，因此如果在 V2 层中创建资源，V1 层会毫不知情，这样会发生问题，例如 API 管理会尝试使用已经分配给 NIC（在 V2 上构建）的 IP。
若要深入了解 Azure 中经典模型和 Resource Manager 模型的差异，请参阅[部署模型的差异](../azure-resource-manager/resource-manager-deployment-model.md)。

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>在 VNET 中部署 API 管理时所需的最小子网大小是多少？
部署 API 管理所需的最小子网大小为 [/29](../virtual-network/virtual-networks-faq.md#configuration)，这是 Azure 支持的最小子网大小。

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>是否可将 API 管理服务从一个订阅移到另一个订阅？
是的。 要了解操作方法，请参阅[将资源移动到新资源组或订阅](../azure-resource-manager/resource-group-move-resources.md)。

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>导入 API 是否存在限制或已知问题？
[Open API(Swagger)、WSDL 和 WADL 格式的已知问题和限制](api-management-api-import-restrictions.md)。
