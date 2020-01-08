---
title: Azure 资源的自定义角色 | Microsoft Docs
description: 了解如何使用基于角色的访问控制 (RBAC) 创建自定义角色，以便对 Azure 资源进行精细的访问权限管理。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fbea0567ec125ce12acd8f757b32df723876fe09
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338571"
---
# <a name="custom-roles-for-azure-resources"></a>Azure 资源的自定义角色

如果 [Azure 资源的内置角色](built-in-roles.md)不能满足组织的特定需求，则可以创建自定义角色。 与内置角色一样，可以将自定义角色分配到订阅、资源组和资源范围内的用户、组和服务主体。

自定义角色存储在 Azure Active Directory (Azure AD) 目录中，可以在订阅之间共享。 每个目录最多可以有 **5000** 个自定义角色。 （Azure 政府、Azure 德国、Azure 中国世纪互联等专用云的限制为 2000 个自定义角色。）可以使用 Azure PowerShell、Azure CLI 或 REST API 创建自定义角色。

## <a name="custom-role-example"></a>自定义角色示例

下面展示了以 JSON 格式显示的自定义角色的样子。 自定义角色可以用于监视和重新启动虚拟机。

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

创建自定义角色后，该角色会显示在 Azure 门户中，并带有一个橙色资源图标。

![自定义角色图标](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>创建自定义角色的步骤

1. 确定如何创建自定义角色

    可以使用 [Azure PowerShell](custom-roles-powershell.md)、[Azure CLI](custom-roles-cli.md) 或 [REST API](custom-roles-rest.md) 创建自定义角色。

1. 确定所需的权限

    创建自定义角色时，需要知道可用于定义权限的资源提供程序操作。 若要查看操作列表，请参阅 [Azure 资源管理器资源提供程序操作](resource-provider-operations.md)。 你将操作添加到[角色定义](role-definitions.md)的 `Actions` 或 `NotActions` 属性。 如果有数据操作，请将这些操作添加到 `DataActions` 或 `NotDataActions` 属性。

1. 创建自定义角色

    通常，我们会从一个现有的内置角色着手，并根据需要对其进行修改。 然后，使用 [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) 或 [az role definition create](/cli/azure/role/definition#az-role-definition-create) 命令创建自定义角色。 若要创建自定义角色，必须拥有所有 `AssignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 权限，例如[所有者](built-in-roles.md#owner)或[用户访问权限管理员](built-in-roles.md#user-access-administrator)。

1. 测试自定义角色

    创建自定义角色后，必须对其进行测试，以验证它是否按预期工作。 如果以后需要进行调整，可以更新自定义角色。

有关如何创建自定义角色的分步教程，请参阅[教程：使用 Azure PowerShell 创建自定义角色](tutorial-custom-role-powershell.md)或[教程：使用 Azure CLI 创建自定义角色](tutorial-custom-role-cli.md)。

## <a name="custom-role-properties"></a>自定义角色属性

自定义角色具有以下属性。

| 属性 | 必填 | 类型 | 描述 |
| --- | --- | --- | --- |
| `Name` | 是 | 字符串 | 自定义角色的显示名称。 虽然角色定义是订阅级资源，但角色定义可以在共享同一 Azure AD 目录的多个订阅中使用。 此显示名称在 Azure AD 目录范围内必须是唯一的。 可以包含字母、数字、空格和特殊字符。 最多包含 128 个字符。 |
| `Id` | 是 | 字符串 | 自定义角色的唯一 ID。 如果使用 Azure PowerShell 和 Azure CLI，在创建新角色时会自动生成此 ID。 |
| `IsCustom` | 是 | 字符串 | 指示此角色是否为自定义角色。 设置为 `true` 表示是自定义角色。 |
| `Description` | 是 | 字符串 | 自定义角色的说明。 可以包含字母、数字、空格和特殊字符。 最多包含 1024 个字符。 |
| `Actions` | 是 | String[] | 一个字符串数组，指定该角色允许执行的管理操作。 有关详细信息，请参阅 [Actions](role-definitions.md#actions)。 |
| `NotActions` | 否 | String[] | 一个字符串数组，指定要从允许的 `Actions` 中排除的管理操作。 有关详细信息，请参阅 [NotActions](role-definitions.md#notactions)。 |
| `DataActions` | 否 | String[] | 一个字符串数组，指定该角色允许对该对象中的数据执行的数据操作。 有关详细信息，请参阅 [DataActions](role-definitions.md#dataactions)。 |
| `NotDataActions` | 否 | String[] | 一个字符串数组，指定要从允许的 `DataActions` 中排除的数据操作。 有关详细信息，请参阅 [NotDataActions](role-definitions.md#notdataactions)。 |
| `AssignableScopes` | 是 | String[] | 一个字符串数组，指定自定义角色的可分配范围。 对于自定义角色，当前不能将 `AssignableScopes` 设置为根范围 (`"/"`) 或管理组范围。 有关详细信息，请参阅 [AssignableScopes](role-definitions.md#assignablescopes) 和[使用 Azure 管理组来组织资源](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)。 |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>谁可以创建、删除、更新或查看自定义角色

与在内置角色中一样，`AssignableScopes` 属性指定角色的可配置范围。 自定义角色的 `AssignableScopes` 属性还控制谁可以创建、删除、更新或查看自定义角色。

| 任务 | 操作 | 描述 |
| --- | --- | --- |
| 创建/删除自定义角色 | `Microsoft.Authorization/ roleDefinitions/write` | 在自定义角色的所有 `AssignableScopes` 上被允许此操作的用户可以创建（或删除）用于这些范围的自定义角色。 例如，订阅、资源组和资源的[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)。 |
| 更新自定义角色 | `Microsoft.Authorization/ roleDefinitions/write` | 被授权在自定义角色的所有 `AssignableScopes` 上执行此操作的用户可以更新这些范围中的自定义角色。 例如，订阅、资源组和资源的[所有者](built-in-roles.md#owner)和[用户访问管理员](built-in-roles.md#user-access-administrator)。 |
| 查看自定义角色 | `Microsoft.Authorization/ roleDefinitions/read` | 在某个范围内被允许此操作的用户可以查看可在该范围内分配的自定义角色。 所有内置角色都允许自定义角色可用于分配。 |

## <a name="next-steps"></a>后续步骤
- [使用 Azure PowerShell 为 Azure 资源创建自定义角色](custom-roles-powershell.md)
- [使用 Azure CLI 为 Azure 资源创建自定义角色](custom-roles-cli.md)
- [了解 Azure 资源的角色定义](role-definitions.md)
- [ Azure 资源 RBAC 故障排除](troubleshooting.md)
