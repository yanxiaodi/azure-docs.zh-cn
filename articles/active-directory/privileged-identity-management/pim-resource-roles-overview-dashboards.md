---
title: 使用资源仪表板在 PIM 中执行访问评审 - Azure Active Directory | Microsoft Docs
description: 介绍如何使用资源仪表板在 Azure AD Privileged Identity Management (PIM) 中执行访问评审。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e759ba47c16617aa1783ce6fb0e324aa62ee96d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804109"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>使用资源仪表板在 PIM 中执行访问评审

可以使用资源仪表板在 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 中执行访问评审。 “管理员视图”仪表板包含三个主要组件：

- 资源角色激活操作的图形表示形式。
- 两个图表按分配类型显示了角色分配分布情况。
- 与新角色分配相关的数据区域。

![管理员视图仪表板的屏幕截图，显示图形和图表](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![显示数据列表的“管理员视图”仪表板屏幕截图](media/pim-resource-roles-overview-dashboards/role-settings.png)

过去七天执行的资源角色激活操作的图形表示形式。 此数据对应于选定的资源，显示最常见角色（所有者、参与者、用户访问管理员）以及所有角色的激活情况。

在激活图形的右侧，有两个图表按分配类型显示了用户和组的角色分配分布情况。 选择图表中的切片会将值更改为百分比（或相反）。

在图表下方，可以看到在过去 30 天获得了新角色分配的用户和组数，以及按分配总数排序的角色列表（以降序排序）。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中启动 Azure 资源角色的访问评审](pim-resource-roles-start-access-review.md) 
