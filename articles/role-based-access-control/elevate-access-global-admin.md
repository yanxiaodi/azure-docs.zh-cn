---
title: 提升访问权限以管理所有 Azure 订阅和管理组 | Microsoft Docs
description: 介绍如何使用 Azure 门户或 REST API 提升全局管理员的访问权限，以管理 Azure Active Directory 中的所有订阅和管理组。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 984284fa185d4d8454b1689a62ca9e08c342e33b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195122"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>提升访问权限以管理所有 Azure 订阅和管理组

Azure Active Directory (Azure AD) 中的全局管理员不一定对目录中的所有订阅和管理组拥有访问权限。 本文介绍如何自我提升对所有订阅和管理组的访问权限。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>为何需要提升访问权限？

全局管理员有时可能需要执行以下操作：

- 在用户失去访问权限时重新获取对 Azure 订阅或管理组的访问权限
- 授予其他用户或自己对 Azure 订阅或管理组的访问权限
- 查看组织中的所有 Azure 订阅或管理组
- 允许自动化应用（例如发票或审计应用）访问所有 Azure 订阅或管理组

## <a name="how-does-elevate-access-work"></a>提升访问权限的工作原理是什么？

Azure AD 和 Azure 资源彼此独立保护。 也就是说，Azure AD 角色分配不授予对 Azure 资源的访问权限，Azure 角色分配页不授予对 Azure AD 的访问权限。 但是，Azure AD 中的[全局管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions)可为自己分配对目录中所有 Azure 订阅和管理组的访问权限。 如果无权访问 Azure 订阅资源（如虚拟机或存储帐户），并且想使用全局管理员权限来获取这些资源的访问权限，则请使用此功能。

提升访问权限时，将分配到 Azure 中根范围 (`/`) 的[用户访问管理员](built-in-roles.md#user-access-administrator)角色。 此角色可查看所有资源，并且可用于分配目录中任何订阅或管理组中的访问权限。 可以使用 PowerShell 删除用户访问管理员角色分配。

完成需在根范围执行的更改后，应删除此提升的访问权限。

![提升访问权限](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Azure 门户

请按照这些步骤，使用 Azure 门户为全局管理员提升访问权限。

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com) 或 [Azure Active Directory 管理中心](https://aad.portal.azure.com)。

1. 在导航列表中，单击“Azure Active Directory”，然后单击“属性”。

   ![Azure AD 属性 - 屏幕截图](./media/elevate-access-global-admin/aad-properties.png)

1. 在“Azure 资源的访问管理”下，将开关设置为“是”。

   ![Azure 资源的访问管理 - 屏幕截图](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   将开关设为“是”时，你将分配到 Azure RBAC 中根范围 (/) 的用户访问管理员角色。 这将授予你在与此 Azure AD 目录关联的所有 Azure 订阅和管理组中分配角色的权限。 此开关仅适用于分配到 Azure AD 中全局管理员角色的用户。

   将开关设为“否”时，会从用户帐户中删除 Azure RBAC 中的用户访问管理员角色。 将无法再分配在与此 Azure AD 目录关联的所有 Azure 订阅和管理组中的角色。 只能查看和管理已获取访问权限的 Azure 订阅和管理组。

1. 单击“保存”，保存设置。

   此设置不是全局属性，仅适用于当前已登录的用户。 无法提升所有全局管理员角色成员的访问权限。

1. 注销然后重新登录可以刷新访问权限。

    现在，你应该有权访问目录中的所有订阅和管理组。 你会注意到，系统为你分配了根范围的“用户访问管理员”角色。

   ![根范围的订阅角色分配 - 屏幕截图](./media/elevate-access-global-admin/iam-root.png)

1. 以提升的访问权限做出所需的更改。

    有关如何分配角色的信息，请参阅[使用 RBAC 和 Azure 门户管理访问权限](role-assignments-portal.md)。 如果使用 Azure AD Privileged Identity Management (PIM)，请参阅[在 PIM 中发现要管理的 Azure 资源](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md)或[在 PIM 中分配 Azure 资源角色](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)。

1. 完成后，将“Azure 资源的访问管理”切换回到“否”。 由于此设置特定于用户，因此，必须以提升访问权限时所用的同一用户登录。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-the-root-scope-"></a>列出根范围 (/) 处的角色分配

若要列出用户在根范围 (`/`) 内的用户访问管理员角色分配，请使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 命令。

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>删除根范围 (/) 处的角色分配

若要在根范围 (`/`) 删除用户的用户访问管理员角色分配，请遵循以下步骤。

1. 以能够删除提升访问权限的用户身份登录。 此用户可以是提升访问权限时所用的同一用户，也可以是在根范围拥有提升访问权限的另一个全局管理员。


1. 使用 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 命令删除用户访问管理员角色分配。

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>REST API

### <a name="elevate-access-for-a-global-administrator"></a>为局管理员提升访问权限

使用以下基本步骤，通过 REST API 为全局管理员提升访问权限。

1. 使用 REST 调用 `elevateAccess`，这将授予你根范围 (`/`) 内的用户访问管理员角色。

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. 创建[角色分配](/rest/api/authorization/roleassignments)，以便在任意范围分配任意角色。 以下示例显示用于在根范围 (`/`) 内分配 {roleDefinitionID} 角色的属性：

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. 作为用户访问管理员，还可以在根范围 (`/`) 删除角色分配。

1. 撤销用户访问管理员特权一直到再次需要时。

### <a name="list-role-assignments-at-the-root-scope-"></a>列出根范围 (/) 处的角色分配

可以在根范围 (`/`) 列出用户的所有角色分配。

- 调用 [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope)，其中 `{objectIdOfUser}` 是要检索其角色分配的用户的对象 ID。

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>列出根范围 (/) 处的拒绝分配

可以在根范围 (`/`) 列出用户的所有拒绝分配。

- 调用 GET denyAssignments，其中 `{objectIdOfUser}` 是要检索其拒绝分配的用户的对象 ID。

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>撤消提升的访问权限

调用 `elevateAccess` 即为自己创建角色分配，因此若要撤销这些特权，需要删除分配。

1. 调用 [GET roleDefinitions](/rest/api/authorization/roledefinitions/get)，其中 `roleName` = 用户访问管理员，由此确定用户访问管理员角色的名称 ID。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    保存 `name` 参数中的 ID，在本例中为 `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`。

2. 还需要列出目录管理员在目录范围的角色分配。 对于执行了提升访问权限调用的目录管理员，列出其 `principalId` 在目录范围内的所有分配。 这将为 objectid 列出目录中的所有分配。

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >目录管理员不应拥有多个分配，如果前面的查询返回过多分配，你也可以只在目录范围级别查询所有分配，然后筛选结果：`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
   1. 上述调用将返回角色分配列表。 在范围 `"/"` 查找以下角色分配：`roleDefinitionId` 以第 1 步中的角色名称 ID 结尾，并且 `principalId` 与目录管理员的 objectId 一致。 
    
      示例角色分配：

       ```json
       {
         "value": [
           {
             "properties": {
               "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
               "principalId": "{objectID}",
               "scope": "/",
               "createdOn": "2016-08-17T19:21:16.3422480Z",
               "updatedOn": "2016-08-17T19:21:16.3422480Z",
               "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
               "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
             },
             "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
             "type": "Microsoft.Authorization/roleAssignments",
             "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
           }
         ],
         "nextLink": null
       }
       ```
        
      同样，保存 `name` 参数的 ID，在本例中为 e7dd75bc-06f6-4e71-9014-ee96a929d099。

   1. 最后，使用角色分配 ID 删除 `elevateAccess` 添加的分配：

      ```http
      DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
      ```

## <a name="next-steps"></a>后续步骤

- [了解 Azure 中的不同角色](rbac-and-directory-admin-roles.md)
- [使用 RBAC 和 REST API 管理对 Azure 资源的访问权限](role-assignments-rest.md)
