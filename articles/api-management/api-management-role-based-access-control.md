---
title: 如何在 Azure API 管理中使用基于角色的访问控制 | Microsoft Docs
description: 了解如何在 Azure API 管理中使用内置角色和创建自定义角色
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: e33d981429f0e79accbe47ea0edea5f3c7a2157b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072213"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>如何在 Azure API 管理中使用基于角色的访问控制

Azure API 管理依赖于 Azure 基于角色的访问控制 (RBAC) 来为 API 管理服务和实体（例如，API 和策略）启用精细访问管理。 本文概述 API 管理中的内置角色和自定义角色。 有关 Azure 门户中的访问管理的详细信息，请参阅 [Azure 门户中的访问管理入门](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>内置角色

API 管理目前提供了三个内置角色，不久之后会再添加两个角色。 可在不同的范围（包括订阅、资源组和单个 API 管理实例）分配这些角色。 例如，如果在资源组级别将“Azure API 管理服务读取者”角色分配给某个用户，则该用户将对该资源组中的所有 API 管理实例拥有读取访问权限。 

下表提供内置角色的简短说明。 可以使用 Azure 门户或其他工具（包括 Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 和 [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)）分配这些角色。 有关如何分配内置角色的详细信息，请参阅[使用角色分配管理对 Azure 订阅资源的访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

| Role          | 读取访问权限<sup>[1]</sup> | 写入访问权限<sup>[2]</sup> | 服务创建、删除、缩放，VPN 和自定义域配置 | 对旧版发布者门户拥有访问权限 | 描述
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Azure API 管理服务参与者 | ✓ | ✓ | ✓ | ✓ | 超级用户。 对 API 管理服务和实体（例如，API 和策略）拥有完全 CRUD 访问权限。 对旧版发布者门户拥有访问权限。 |
| Azure API 管理服务读取者 | ✓ | | || 对 API 管理服务和实体拥有只读访问权限。 |
| Azure API 管理服务操作员 | ✓ | | ✓ | | 可以管理 API 管理服务，但不能管理实例。|
| Azure API 管理服务编辑者<sup>*</sup> | ✓ | ✓ | |  | 可以管理 API 管理实体，但不能管理服务。|
| Azure API 管理内容管理员<sup>*</sup> | ✓ | | | ✓ | 可以管理开发人员门户。 对服务和实体拥有只读访问权限。|

<sup>[1] 对 API 管理服务和实体（例如，API 和策略）拥有读取访问权限。</sup>

<sup>[2] 对 API 管理服务和实体拥有写入访问权限，但以下操作除外：实例创建、删除和缩放；VPN 配置；以及自定义域设置。</sup>

<sup>\* 将所有管理 UI 从现有发布者门户迁移到 Azure 门户后，将提供“服务编辑者”角色。将发布者门户重建为只包含与管理开发人员门户相关的功能后，将提供“内容管理员”角色。</sup>  

## <a name="custom-roles"></a>自定义角色

如果没有任何内置角色可以满足具体需要，可以创建自定义角色，针对 API 管理实体提供更精细的访问管理。 例如，可以创建一个对 API 管理服务拥有只读访问权限，但只对某个特定 API 拥有写入访问权限的自定义角色。 若要详细了解自定义角色，请参阅 [Azure RBAC 中的自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。 

> [!NOTE]
> 若要在 Azure 门户中查看 API 管理实例，自定义角色必须包含 ```Microsoft.ApiManagement/service/read``` 操作。

创建自定义角色时，从某个内置角色着手会更为轻松。 编辑属性以添加 **Actions**、**NotActions** 或 **AssignableScopes**，然后将所做的更改保存为新角色。 以下示例从“Azure API 管理服务读取者”角色着手，创建名为“计算器 API 编辑者”的自定义角色。 可以将自定义角色分配给特定的 API。 因此，此角色仅有权访问该 API。 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

[Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)一文提供了可以在 API 管理级别授予的权限的列表。

## <a name="video"></a>视频


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 中基于角色的访问控制，请参阅以下文章：
  * [Azure 门户中的访问管理入门](../role-based-access-control/overview.md)
  * [使用角色分配管理对 Azure 订阅资源的访问权限](../role-based-access-control/role-assignments-portal.md)
  * [Azure RBAC 中的自定义角色](../role-based-access-control/custom-roles.md)
  * [Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)