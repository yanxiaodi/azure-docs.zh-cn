---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: 03e4053b65cf39101e8cb5d35ce439a759ec11d6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173337"
---
1. 在要锁定的资源、资源组或订阅的“设置”边栏选项卡中，选择“锁定”  。
   
      ![选择锁](./media/resource-manager-lock-resources/select-lock.png)
2. 若要添加锁，请选择“添加”  。 如果要在父级别创建锁，请选择父级。 当前选定的资源将从父级继承锁。 例如，可以锁定资源组，以便向其所有资源应用锁。
   
      ![添加锁](./media/resource-manager-lock-resources/add-lock.png) 
3. 为该锁提供名称和锁级别。 （可选）可以添加注释来描述该锁。
   
      ![设置锁](./media/resource-manager-lock-resources/set-lock.png) 
4. 若要删除锁，请从可用选项中选择省略号中的“删除”  。
   
      ![删除锁](./media/resource-manager-lock-resources/delete-lock.png) 

