---
title: 在 Azure CLI 中使用 RBAC 管理 Azure AD 对 Blob 和队列数据的访问权限 - Azure 存储
description: 通过 Azure CLI 使用基于角色的访问控制 (RBAC) 分配容器和队列的访问权限。 Azure 存储支持通过 Azure AD 使用内置和自定义的 RBAC 角色进行身份验证。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 606dd88fbad8cbd5c7e24d47dcf71199a25b49a2
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673205"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-azure-cli"></a>在 Azure CLI 中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限

Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 存储定义了一组内置的 RBAC 角色，它们包含用于访问 Blob 或队列数据的通用权限集。

将 RBAC 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

本文介绍如何使用 Azure CLI 列出内置的 RBAC 角色并将其分配给用户。 若要详细了解如何使用 Azure CLI，请参阅 [Azure 命令行界面 (CLI)](https://docs.microsoft.com/cli/azure)。

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 和队列的 RBAC 角色

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>确定资源范围 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>列出可用的 RBAC 角色

若要通过 Azure CLI 列出可用的内置 RBAC 角色，请使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list) 命令：

```azurecli-interactive
az role definition list --out table
```

会看到列出了内置的 Azure 存储数据角色以及 Azure 的其他内置角色：

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>向安全主体分配 RBAC 角色

若要向安全主体分配 RBAC 角色，请使用 [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) 命令。 命令的格式因分配范围而异。 以下示例显示如何在各种范围内为用户分配角色，但可以使用相同的命令将角色分配给任何安全主体。

### <a name="container-scope"></a>容器范围

若要分配容器范围的角色，请为 `--scope` 参数指定一个包含容器范围的字符串。 容器的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

以下示例为用户分配**存储 Blob 数据参与者**角色，其范围为容器级别。 请务必将括号中的示例值和占位符值替换为你自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>队列范围

若要分配队列范围的角色，请为 `--scope` 参数指定一个包含队列范围的字符串。 队列的范围采用以下格式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

以下示例为用户分配**存储队列数据参与者**角色，其范围为队列级别。 请务必将括号中的示例值和占位符值替换为你自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>存储帐户范围

若要分配存储帐户范围的角色，请为 `--scope` 参数指定存储帐户资源的范围。 存储帐户的范围采用以下形式：

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

以下示例演示如何在存储帐户级别向用户分配“存储 Blob 数据读取者”角色。 请务必将示例值替换为你自己的值：\

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>资源组范围

若要分配资源组范围的角色，请为 `--resource-group` 参数指定资源组名称或 ID。 以下示例在资源组级别向用户分配“存储队列数据读取者”角色。 请务必将括号中的示例值和占位符值替换为你自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>订阅范围

若要分配订阅范围的角色，请为 `--scope` 参数指定订阅的范围。 订阅的范围采用以下格式：

```
/subscriptions/<subscription>
```

以下示例演示如何在存储帐户级别向用户分配“存储 Blob 数据读取者”角色。 请务必将示例值替换为你自己的值： 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>后续步骤

- [使用 RBAC 和 Azure PowerShell 管理对 Azure 资源的访问权限](../../role-based-access-control/role-assignments-powershell.md)
- [在 Azure PowerShell 中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-powershell.md)
- [在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac-portal.md)
