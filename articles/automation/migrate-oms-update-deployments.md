---
title: 将 OMS 更新部署迁移到 Azure
description: 本文介绍如何将现有的 OMS 更新部署迁移到 Azure
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 07/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2cc05dc0970211b066d9e431cc7aba06cbd126eb
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478308"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>将 OMS 更新部署迁移到 Azure

Operations Management Suite (OMS) 门户已被[弃用](../azure-monitor/platform/oms-portal-transition.md)。 Azure 门户提供了 OMS 门户中可用于更新管理的所有功能。 本文提供迁移到 Azure 门户所需的信息。

## <a name="key-information"></a>重要信息

* 现有部署将继续发挥作用。 在 Azure 中重新创建部署后，即可从 OMS 中删除旧部署。
* Azure 提供 OMS 中的所有现有功能，若要详细了解更新管理，请参阅[更新管理概述](automation-update-management.md)。

## <a name="access-the-azure-portal"></a>访问 Azure 门户

在 OMS 工作区中，单击“在 Azure 中打开”  。 这将导航到 OMS 使用的 Log Analytics 工作区。

![在 Azure 中打开 - OMS 门户](media/migrate-oms-update-deployments/link-to-azure-portal.png)

在 Azure 门户中，单击“自动化帐户” 

![Azure Monitor 日志](media/migrate-oms-update-deployments/log-analytics.png)

在自动化帐户中，单击“更新管理”以打开更新管理  。

![更新管理](media/migrate-oms-update-deployments/azure-automation.png)

将来，你可以直接转到 Azure 门户，在“所有服务”下，选择“管理工具”下的“自动化帐户”，选择相应的自动化帐户，然后单击“更新管理”     。

## <a name="recreate-existing-deployments"></a>重新创建现有部署

在 OMS 门户中创建的所有更新部署有一个[保存的搜索结果](../azure-monitor/platform/computer-groups.md)，也称为计算机组，其名称与现有更新部署的名称相同。 保存的搜索结果包含在更新部署中计划的计算机列表。

![更新管理](media/migrate-oms-update-deployments/oms-deployment.png)

若要使用此现有的保存搜索结果，请执行以下步骤：

若要创建新的更新部署，请转到 Azure 门户，选择所用的自动化帐户，单击“更新管理”  。 单击“计划更新部署”  。

![计划更新部署](media/migrate-oms-update-deployments/schedule-update-deployment.png)

此时将打开“新建更新部署”窗格。  为下表中介绍的属性输入值，然后单击“创建”  ：

对于要更新的计算机，请选择现有 OMS 部署所用的保存的搜索结果。

| 属性 | Description |
| --- | --- |
|Name |用于标识更新部署的唯一名称。 |
|操作系统| 选择 **Linux** 或 **Windows**。|
|要更新的计算机 |选择已保存的搜索、已导入的组或者从下拉列表中选择“计算机”并选择单个计算机。 如果选择“计算机”，则计算机的就绪状态将在“更新代理商准备情况”列中显示   。</br> 要了解在 Azure Monitor 日志中创建计算机组的不同方法，请参阅 [Azure Monitor 日志中的计算机组](../azure-monitor/platform/computer-groups.md) |
|更新分类|选择所需的所有更新分类。 CentOS 不能现成地支持此功能。|
|要排除的更新|输入要排除的更新。 对于 Windows，输入不带 **KB** 前缀的知识库文章。 对于 Linux，输入包名称或使用通配符。  |
|计划设置|选择启动时间，对于“定期”，然后“一次”或“重复”。   | 
| 维护时段 |为更新设置的分钟数。 该值不能小于 30 分钟，也不能大于 6 小时。 |
| 重新启动控制| 确定应如何处理重新启动。</br>可用选项包括：</br>需要时重新启动(默认)</br>始终重新启动</br>从不重新启动</br>仅重启 - 不安装更新|

单击“计划的更新部署”以查看新建更新部署的状态  。

![新建更新部署](media/migrate-oms-update-deployments/new-update-deployment.png)

如前所述，通过 Azure 门户配置新部署后，即可从 OMS 门户中删除现有部署。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 中的更新管理，请参阅[更新管理](automation-update-management.md)。
