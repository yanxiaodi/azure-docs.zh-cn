---
title: 如何在 Azure VM 上使用 Azure CLI 配置系统分配托管标识和用户分配托管标识
description: 分步说明如何使用 Azure CLI 在 Azure VM 上配置系统分和用户分配的托管标识。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 272315346091bacb15aef02184e1cc72d64ed49d
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309799"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>使用 Azure CLI 在 Azure VM 上配置 Azure 资源托管标识

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文介绍如何使用 Azure CLI 在 Azure VM 上执行以下 Azure 资源托管标识操作：

- 在 Azure VM 上启用和禁用系统分配托管标识
- 在 Azure VM 上添加和删除用户分配托管标识

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要运行 CLI 脚本示例，可以使用下列三种方法：
    - 在 Azure 门户中使用 [Azure Cloud Shell](../../cloud-shell/overview.md)（见下一部分）。
    - 单击各代码块右上角的“试运行”按钮，使用嵌入的 Azure Cloud Shell。
    - 如果喜欢使用本地 CLI 控制台，请[安装最新版的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 
      
      > [!NOTE]
      > 命令已更新，以反映最新版本的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。     

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

本部分介绍如何使用 Azure CLI 在 Azure VM 上启用和禁用系统分配托管标识。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>在创建 Azure VM 的过程中启用系统分配托管标识

若要创建启用了系统分配的托管标识的 Azure VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure。 使用与要在其下部署 VM 的 Azure 订阅关联的帐户：

   ```azurecli-interactive
   az login
   ```

2. 运行 [az group create](/cli/azure/group/#az-group-create)，创建用于容纳和部署 VM 及其相关资源的[资源组](../../azure-resource-manager/resource-group-overview.md#terminology)。 如果已有要改用的资源组，可以跳过这一步：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. 运行 [az vm create](/cli/azure/vm/#az-vm-create) 创建 VM。 以下示例按 `--assign-identity` 参数的要求，创建名为 myVM 且已启用系统分配托管标识的 VM。 `--admin-username` 和 `--admin-password` 参数指定用于登录虚拟机的管理用户名和密码帐户。 针对自己的环境相应地更新这些值： 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>在现有 Azure VM 上启用系统分配托管标识

若要在 VM 上启用系统分配的托管标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

1. 如果在本地控制台中使用 Azure CLI，首先请使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure。 使用与包含 VM 的 Azure 订阅关联的帐户。

   ```azurecli-interactive
   az login
   ```

2. 将 [az vm identity assign](/cli/azure/vm/identity/) 与 `identity assign` 命令配合使用，为现有 VM 启用系统分配标识：

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>从 Azure VM 中禁用系统分配标识

若要在 VM 上禁用系统分配的托管标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

如果某个虚拟机不再需要系统分配的标识，但仍需要用户分配的标识，请使用以下命令：

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

如果某个虚拟机不再需要系统分配的标识，且没有用户分配的标识，请使用以下命令：

> [!NOTE]
> 值 `none` 区分大小写。 它必须为小写。 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>用户分配的托管标识

本部分介绍如何使用 Azure CLI 在 Azure VM 中添加和删除用户分配托管标识。

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>在创建 Azure VM 的过程中分配用户分配托管标识

若要在 VM 创建期间将用户分配的标识分配给 VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)和[托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色分配。 无需其他 Azure AD 目录角色分配。

1. 如果已有要使用的资源组，可跳过此步骤。 使用 [az group create](/cli/azure/group/#az-group-create) 创建用于包含和部署用户分配托管标识的[资源组](~/articles/azure-resource-manager/resource-group-overview.md#terminology)。 请务必将 `<RESOURCE GROUP>` 和 `<LOCATION>` 参数值替换为自己的值。 :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. 使用 [az identity create](/cli/azure/identity#az-identity-create) 创建用户分配托管标识。  `-g` 参数指定要创建用户分配托管标识的资源组，`-n` 参数指定其名称。    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   响应包含所创建的用户分配托管标识的详细信息，与以下示例类似。 下一步会用到分配给用户分配托管标识的资源 ID 值。

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. 运行 [az vm create](/cli/azure/vm/#az-vm-create) 创建 VM。 以下示例创建与新用户分配标识关联的 VM，用 `--assign-identity` 参数指定。 请务必将 `<RESOURCE GROUP>`、`<VM NAME>`、`<USER NAME>`、`<PASSWORD>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为你自己的值。 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>向现有 Azure VM 分配用户分配托管标识

若要将用户分配的标识分配给 VM，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)和[托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色分配。 无需其他 Azure AD 目录角色分配。

1. 使用 [az identity create](/cli/azure/identity#az-identity-create) 创建用户分配标识。  `-g` 参数指定要创建用户分配标识的资源组，`-n` 参数指定其名称。 请务必将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值：

    > [!IMPORTANT]
    > 目前不支持创建名称中具有特殊字符（即下划线）的用户分配托管标识。 请使用字母数字字符。 稍后返回查看更新。  有关详细信息，请参阅 [FAQ 和已知问题](known-issues.md)

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   响应包含所创建的用户分配托管标识的详细信息，与以下示例类似。 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. 使用 [az vm identity assign](/cli/azure/vm) 将用户分配标识分配给 VM。 请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 `<USER ASSIGNED IDENTITY NAME>` 为上一步创建的用户分配托管标识的资源 `name` 属性：

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>从 Azure VM 中删除用户分配的托管标识

若要从 VM 中删除用户分配的标识，你的帐户需要[虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)角色分配。 

如果这是分配给虚拟机的唯一用户分配托管标识，则 `UserAssigned` 将从标识类型值中删除。  请务必将 `<RESOURCE GROUP>` 和 `<VM NAME>` 参数值替换为自己的值。 `<USER ASSIGNED IDENTITY>` 将为用户分配标识的 `name` 属性，可通过 `az vm identity show` 在虚拟机的标识部分中找到：

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

如果 VM 没有系统分配托管标识，并且你希望从中删除所有用户分配标识，请使用以下命令：

> [!NOTE]
> 值 `none` 区分大小写。 它必须为小写。

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

如果 VM 同时具有系统分配标识和用户分配标识，则可以切换为仅使用系统分配标识，从而删除所有用户分配标识。 请使用以下命令：

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>后续步骤
- [Azure 资源的托管标识概述](overview.md)
- 有关完整的 Azure VM 创建快速入门，请参阅： 
  - [使用 CLI 创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-cli.md)  
  - [使用 CLI 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-cli.md) 

















