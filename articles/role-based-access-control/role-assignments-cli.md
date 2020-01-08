---
title: 使用 RBAC 和 Azure CLI 管理对 Azure 资源的访问权限 | Microsoft Docs
description: 了解如何使用基于角色的访问控制 (RBAC) 和 Azure CLI 来管理用户、组和应用程序对 Azure 资源的访问权限。 这包括如何列出访问权限、授予访问权限以及删除访问权限。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3420374e90790bd1ffe4c845c19de1bfed317302
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173728"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>使用 RBAC 和 Azure CLI 管理对 Azure 资源的访问权限

可以通过[基于角色的访问控制 (RBAC)](overview.md) 方式管理对 Azure 资源的访问权限。 本文介绍如何使用 RBAC 和 Azure CLI 来管理用户、组和应用程序的访问权限。

## <a name="prerequisites"></a>先决条件

若要管理访问，需要具有以下任一项：

* [Bash in Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>列出角色

若要列出所有可用的角色定义，请使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list)：

```azurecli
az role definition list
```

以下示例列出了所有可用的角色定义的名称和说明：

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

下面的示例列出了所有内置的角色定义：

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>列出角色定义

若要列出角色定义，请使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list)：

```azurecli
az role definition list --name <role_name>
```

下面的示例列出了“参与者”角色定义：

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>列出角色的操作

以下示例仅列出了“参与者”角色的 actions 和 notActions：

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

以下示例仅列出了“虚拟机参与者”角色的 actions：

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>列出访问权限

在 RBAC 中，若要列出访问权限，请列出角色分配。

### <a name="list-role-assignments-for-a-user"></a>为用户列出角色分配

若要列出特定用户的角色分配，请使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --assignee <assignee>
```

默认情况下，将仅显示范围为订阅的直接分配。 若要查看按资源或组划分作用域`--all`的分配，请使用和查看`--include-inherited`继承的分配，使用。

以下示例列出的角色分配直接分配给 *patlong\@contoso.com* 用户：

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>列出资源组范围内的角色分配

若要列出资源组作用域中存在的角色分配，请使用[az role 指配 list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --resource-group <resource_group>
```

以下示例列出 *pharma-sales* 资源组的角色分配：

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>列出订阅范围内的角色分配

若要列出订阅范围内的所有角色分配，请使用[az role 分配 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要获取订阅 ID，可以在 Azure 门户中的 "**订阅**" 边栏选项卡上找到，也可以使用[az account list](/cli/azure/account#az-account-list)。

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>列出管理组范围内的角色分配

若要列出管理组作用域的所有角色分配，请使用[az role 分配 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要获取管理组 ID，你可以在 Azure 门户的 "**管理组**" 边栏选项卡中找到它，也可以使用[az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)。

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="grant-access"></a>授予访问权限

在 RBAC 中，若要授予访问权限，请创建角色分配。

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>在资源组范围内为用户创建角色分配

若要在资源组范围内授予用户访问权限，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

以下示例将“虚拟机参与者”角色分配给 *pharma-sales* 资源组范围内的 *patlong\@contoso.com* 用户：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>使用唯一角色 ID 创建角色分配

很多时候角色名称可能会更改，例如：

- 你使用的是自己的自定义角色，你决定更改名称。
- 你使用的是预览版角色，其名称中有“(预览)”字样。 发布角色时重命名了角色。

> [!IMPORTANT]
> 预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

即使重命名了角色，角色 ID 也不会更改。 如果使用脚本或自动化来创建角色分配，最佳做法是使用唯一的角色 ID 而非角色名称。 这样一来，即使角色重命名，脚本仍可以使用。

若要使用唯一的角色 ID 而非角色名称来创建角色分配，请使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) 命令。

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

下面的示例将[虚拟机参与者](built-in-roles.md#virtual-machine-contributor)角色分配给*医药*资源组作用域上的 *\@patlong contoso.com*用户。 若要获取唯一的角色 ID，可以使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list) 命令，也可以参阅 [Azure 资源的内置角色](built-in-roles.md)。

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>为组创建角色分配

若要授予对组的访问权限，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。 若要获取组的 ID，可以使用 [az ad group list](/cli/azure/ad/group#az-ad-group-list) 或 [az ad group show](/cli/azure/ad/group#az-ad-group-show)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下面的示例将*读者*角色分配给订阅范围内 ID 为22222222-2222-2222-2222-222222222222 的*王 Mack 团队*组。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

下面的示例将*虚拟机参与者*角色分配给名为*医药*的虚拟网络的资源范围内 ID 为22222222-2222-2222-2222-222222222222 的*王 Mack 团队*组。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>为资源组范围内的应用程序创建角色分配

若要授予对应用程序的访问权限，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。 若要获取应用程序的对象 ID，可以使用 [az ad app list](/cli/azure/ad/app#az-ad-app-list) 或 [az ad app show](/cli/azure/ad/app#az-ad-app-show)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

以下示例将“虚拟机参与者”角色分配给 *pharma-sales* 资源组范围内对象 ID 为 44444444-4444-4444-4444-444444444444 的应用程序。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>为订阅范围内的用户创建角色分配

若要在订阅范围向用户授予访问权限，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。 若要获取订阅 ID，可以在 Azure 门户中的 "**订阅**" 边栏选项卡上找到，也可以使用[az account list](/cli/azure/account#az-account-list)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

下面的示例将*读取*者角色分配给订阅范围内的*annm\@example.com*用户。

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>为管理组范围内的用户创建角色分配

若要向管理组范围内的用户授予访问权限，请使用[az role create create](/cli/azure/role/assignment#az-role-assignment-create)。 若要获取管理组 ID，你可以在 Azure 门户的 "**管理组**" 边栏选项卡中找到它，也可以使用[az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

下面的示例将*计费读取*者角色分配给管理组作用域上的*alain\@example.com*用户。

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="create-a-role-assignment-for-a-new-service-principal"></a>创建新服务主体的角色分配

如果创建新的服务主体并立即尝试将角色分配给该服务主体，则在某些情况下该角色分配可能会失败。 例如，如果使用脚本创建新的托管标识，然后尝试将角色分配给该服务主体，则角色分配可能会失败。 此故障的原因可能是复制延迟。 在一个区域中创建服务主体;但是，角色分配可能发生在尚未复制服务主体的其他区域中。 若要解决这种情况，应在创建角色分配时指定主体类型。

若要创建角色分配，请使用[az role 分配 create](/cli/azure/role/assignment#az-role-assignment-create)，为`--assignee-object-id`指定一个值，然后将设置`ServicePrincipal` `--assignee-principal-type`为。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

以下示例在*医药*资源组范围内将*虚拟机参与者*角色分配给*msi 测试*托管标识：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-access"></a>删除访问权限

在 RBAC 中，若要删除访问权限，请使用 [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) 删除角色分配：

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

以下示例在 *pharma-sales* 资源组上从 *patlong\@contoso.com* 用户删除“虚拟机参与者”角色分配：

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

下面的示例从订阅范围内的 ID 为22222222-2222-2222-2222-222222222222 的*王 Mack 团队*组中删除*Reader*角色。 若要获取组的 ID，可以使用 [az ad group list](/cli/azure/ad/group#az-ad-group-list) 或 [az ad group show](/cli/azure/ad/group#az-ad-group-show)。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

以下示例从管理组作用域中的*alain\@example.com*用户删除*计费读取*者角色。 若要获取管理组的 ID，你可以使用[az account management-group list](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)。

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>后续步骤

- [教程：使用 Azure CLI 为 Azure 资源创建自定义角色](tutorial-custom-role-cli.md)
- [使用 Azure CLI 管理 Azure 资源和资源组](../azure-resource-manager/cli-azure-resource-manager.md)
