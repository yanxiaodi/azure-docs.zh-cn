---
title: Windows 虚拟桌面中的委托访问-Azure
description: 如何在 Windows 虚拟桌面部署上委派管理功能，包括示例。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: e8d1ba00043f43f626043d78ce0ab8953a0b3fbe
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679562"
---
# <a name="delegated-access-in-windows-virtual-desktop"></a>Windows 虚拟桌面中的委托访问

Windows 虚拟桌面具有委派的访问模型，使你可以通过为特定用户分配角色来定义允许该用户拥有的访问量。 角色分配有三个组件：安全主体、角色定义和作用域。 Windows 虚拟桌面委托访问模型基于 Azure RBAC 模型。 若要详细了解特定的角色分配及其组件，请参阅[Azure 基于角色的访问控制概述](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)。

Windows 虚拟桌面委托访问为角色分配的每个元素支持以下值：

* 安全主体
    * 用户
    * 服务主体
* 角色定义
    * 内置角色
* 范围
    * 租户组
    * 租户
    * 主机池
    * 应用组

## <a name="built-in-roles"></a>内置角色

Windows 虚拟桌面中的委派访问权限包含若干内置角色定义，你可以将其分配给用户和服务主体。

* RDS 所有者可以管理所有内容，包括对资源的访问权限。
* RDS 参与者可以管理除访问资源之外的所有内容。
* RDS 读者可以查看所有内容，但不能进行任何更改。
* RDS 操作员可以查看诊断活动。

## <a name="powershell-cmdlets-for-role-assignments"></a>用于角色分配的 PowerShell cmdlet

可以运行以下 cmdlet 来创建、查看和删除角色分配：

* **RdsRoleAssignment**显示角色分配的列表。
* **RdsRoleAssignment**创建新的角色分配。
* **RdsRoleAssignment**删除角色分配。

### <a name="accepted-parameters"></a>接受的参数

可以用以下参数修改基本的三个 cmdlet：

* **AadTenantId**：指定服务主体所属的 AZURE ACTIVE DIRECTORY 租户 ID。
* **AppGroupName**：远程桌面应用组的名称。
* **诊断**：指示诊断范围。 （必须与**基础结构**或**租户**参数配对。）
* **HostPoolName**：远程桌面主机池的名称。
* **基础结构**：指示基础结构作用域。
* **RoleDefinitionName**：分配给用户、组或应用远程桌面服务基于角色的访问控制角色的名称。 （例如，远程桌面服务所有者、远程桌面服务读取器等。）
* **ServerPrincipleName**： Azure Active Directory 应用程序的名称。
* **SignInName**：用户的电子邮件地址或用户主体名称。
* **TenantName**：远程桌面租户的名称。

## <a name="next-steps"></a>后续步骤

有关每个角色可以使用的 PowerShell cmdlet 的更完整列表，请参阅[powershell 参考](/powershell/windows-virtual-desktop/overview)。

有关如何设置 Windows 虚拟桌面环境的指导，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
