---
title: Azure 维护计划（预览版）| Microsoft Docs
description: 维护计划使客户能够围绕 Azure SQL 数据仓库服务用于推出新功能、升级和修补程序的必要计划性维护事件进行规划。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: f0b9b59ec09445a170d1f93181c2b432eafb60bf
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839989"
---
# <a name="view-a-maintenance-schedule"></a>查看维护计划 

## <a name="portal"></a>门户

默认情况下，所有新创建的 Azure SQL 数据仓库实例在部署期间会有八小时的主维护时段和辅助维护时段。 部署完成后，你可以更改此时段。 在未事先通知的情况下，不会在指定的维护时段外进行维护。

若要查看已应用于你的数据仓库的维护计划，请完成以下步骤：

1.  登录到 [Azure 门户](https://portal.azure.com/)。
2.  选择要查看的数据仓库。 
3.  所选的数据仓库将在“概述”边栏选项卡上打开。 如下所示应用于数据仓库的维护日程安排**维护日程安排**。

![概述边栏选项卡](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>后续步骤
- [详细了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)如何使用 Azure Monitor 创建、查看和管理警报。
- [详细了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)用于日志警报规则的 Webhook 操作。
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)创建和管理操作组。
- [详细了解](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure 服务运行状况。


