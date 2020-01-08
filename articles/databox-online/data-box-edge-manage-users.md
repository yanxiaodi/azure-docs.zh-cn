---
title: 在 Azure Data Box Edge 中管理用户 | Microsoft Docs
description: 介绍如何使用 Azure 门户管理 Azure Data Box Edge 上的用户。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 68f8ad903f967812c4a416c732b35fa1712404cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60756589"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>使用 Azure 门户管理 Azure Data Box Edge 上的用户

本文介绍如何在 Azure Data Box Edge 中管理用户。 可以通过 Azure 门户或本地 Web UI 管理 Azure Data Box Edge。 使用 Azure 门户来添加、修改或删除用户。

在本文中，学习如何：

> [!div class="checklist"]
> * 添加用户
> * 修改用户
> * 删除用户

## <a name="about-users"></a>关于用户

用户可以是只读的，也可以是全权的。 顾名思义，只读用户只能查看共享数据。 全权用户可以读取共享数据、向这些共享写入数据，以及修改或删除共享数据。

 - **全权用户** - 拥有完全访问权限的本地用户。
 - **只读用户** - 拥有只读访问权限的本地用户。 这些用户与允许只读操作的共享相关联。

在创建共享期间创建用户时，首先会定义用户权限。 定义与某个用户关联的权限后，可以使用文件资源管理器修改这些权限。 


## <a name="add-a-user"></a>添加用户

在 Azure 门户中执行以下步骤可以添加用户。

1. 在 Azure 门户中，转到自己的 Data Box Edge 资源，然后转到“概述”>“用户”。  选择命令栏上的“+ 添加用户”  。

    ![选择“添加用户”](media/data-box-edge-manage-users/add-user-1.png)

2. 指定要添加的用户的用户名和密码。 确认密码，然后选择“添加”。 

    ![指定用户名和密码](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > 以下用户由系统保留，不应使用：Administrator、EdgeUser、EdgeSupport、HcsSetupUser、WDAGUtilityAccount、CLIUSR、DefaultAccount、Guest。  

3. 用户创建过程开始和完成后，会显示通知。 创建用户后，在命令栏中选择“刷新”可查看更新的用户列表。 


## <a name="modify-user"></a>修改用户

创建用户后，可以更改与该用户关联的密码。 从用户列表中进行选择。 输入并确认新密码。 保存更改。
 
![修改用户](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>删除用户

在 Azure 门户中执行以下步骤可以删除用户。


1. 在 Azure 门户中，转到自己的 Data Box Edge 资源，然后转到“概述”>“用户”。 

    ![选择要删除的用户](media/data-box-edge-manage-users/delete-user-1.png)

2. 从用户列表中选择一个用户，然后选择“删除”。   

   ![选择“删除”](media/data-box-edge-manage-users/delete-user-2.png)

3. 出现提示时，确认删除。 

   ![确认删除](media/data-box-edge-manage-users/delete-user-3.png)

用户列表将会更新，以反映该用户已删除。

![更新的用户列表](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>后续步骤

- 了解如何[管理带宽](data-box-edge-manage-bandwidth-schedules.md)。
