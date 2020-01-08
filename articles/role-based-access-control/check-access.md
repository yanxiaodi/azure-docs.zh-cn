---
title: 快速入门 - 查看用户对 Azure 资源的访问权限 | Microsoft Docs
description: 了解如何使用基于角色的访问控制 (RBAC) 和 Azure 门户查看用户或其他安全主体对 Azure 资源的访问权限。
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f388215b2829066906ee7faf41abb17307bf3fff
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337939"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>快速入门：查看用户对 Azure 资源的访问权限

可以使用[基于角色的访问控制 (RBAC)](overview.md) 中的“访问控制(IAM)”边栏选项卡来查看用户或其他安全主体对 Azure 资源的访问权限。 但是，有时你只需要快速查看单个用户或其他安全主体的访问权限。 执行此操作的最简单方法是使用 Azure 门户中的**检查访问权限**功能。

## <a name="view-role-assignments"></a>查看角色分配

 你查看用户访问权限的方式是列出其角色分配。 按照以下步骤查看订阅范围内单个用户、组、服务主体或托管标识的角色分配。

1. 在 Azure 门户中，依次单击“所有服务”、“订阅”。

1. 单击你的订阅。

1. 单击“访问控制(IAM)”。

1. 单击“检查访问权限”选项卡。

    ![“访问控制”-“检查访问权限”选项卡](./media/check-access/access-control-check-access.png)

1. 在“查找”列表中，选择要检查访问权限的安全主体类型。

1. 在搜索框中，输入字符串以在目录中搜索显示名称、电子邮件地址或对象标识符。

    ![“检查访问权限”选择列表](./media/check-access/check-access-select.png)

1. 单击安全主体以打开“分配”窗格。

    ![分配窗格](./media/check-access/check-access-assignments.png)

    在此窗格中，可以看到分配给所选安全主体和范围的角色。 如果此范围内有任何拒绝分配或继承到此范围的角色，则会将其列出。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 RBAC 和 Azure 门户授予用户对 Azure 资源的访问权限](quickstart-assign-role-user-portal.md)
