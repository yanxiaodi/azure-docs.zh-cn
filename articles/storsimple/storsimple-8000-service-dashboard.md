---
title: 使用 StorSimple 8000 系列设备摘要 | Microsoft Docs
description: 介绍 StorSimple 服务摘要边栏选项卡并说明如何使用它来监视 StorSimple 解决方案的运行状况。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: c174f6ce0fb3d40af953be205a7bfcca60fbfeec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60632925"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>使用 StorSimple 8000 系列设备的服务摘要边栏选项卡

## <a name="overview"></a>概述

StorSimple 设备管理器服务摘要边栏选项卡提供连接到 StorSimple 设备管理器服务的所有设备的摘要视图，突出显示那些需要系统管理员注意的设备。 本教程介绍了服务摘要边栏选项卡，说明了仪表板内容和功能，并描述了可从此页执行的任务。

![服务摘要](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>管理命令

在 StorSimple 服务摘要边栏选项卡中，可以看到用于管理 StorSimple 设备管理器服务以及注册到该服务的 StorSimple 8000 系列设备的选项。 在该边栏选项卡的顶部和左侧可看到管理命令。

![命令栏](./media/storsimple-8000-service-dashboard/service-summary2.png)

使用这些选项可以执行各种操作，例如添加共享或卷，或者监视运行在 StorSimple 设备上的各种作业。


## <a name="essentials"></a>概要

概要区域捕获了一些重要的属性，例如资源组、位置，以及在其中创建了 StorSimple 设备管理器的订阅。

![概要](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>StorSimple 设备管理器服务摘要

* “警报”磁贴提供所有设备上所有活动警报的快照，并按警报的严重性进行分组。 

    ![“警报”磁贴](./media/storsimple-8000-service-dashboard/service-summary4.png)

    单击该磁贴可打开“警报”边栏选项卡  ，在其中可单击单个警报以查看该警报的其他详细信息，包括任何建议的操作。 如果已解决此问题，还可清除警报。

    ![单击“警报”磁贴](./media/storsimple-8000-service-dashboard/service-summary8.png)

* “容量”磁贴  显示相对于所有设备上可用的总存储，在所有设备上预配的以及剩余的主存储。 “已预配”  是指已准备且已分配供使用的存储量，“剩余”  是指可以在所有设备上预配的剩余容量。

    ![“容量”磁贴](./media/storsimple-8000-service-dashboard/service-summary6.png)

    “剩余分层”容量是可以预配的可用容量（包括云），而“剩余本地”是附加到 StorSimple 8000 系列设备的磁盘上剩余的容量。  


* 在“使用情况”  图表中，可以看到设备的相关指标。 可查看所有设备使用的主存储，以及在过去 7 天（默认时间段）由设备使用的云存储。 

    ![“使用情况”磁贴](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    若要选择其他时间范围，请使用图表右上角的“编辑”  选项。

     ![单击“使用情况”磁贴](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![导出图表数据](./media/storsimple-8000-service-dashboard/service-summary11.png)

* “设备”  磁贴提供了 StorSimple 设备管理器中按设备状态分组的 StorSimple 8000 系列设备数量的汇总。 

    ![“设备”磁贴](./media/storsimple-8000-service-dashboard/service-summary5.png)

    可以单击此磁贴打开“设备”列表边栏选项卡，再单击单个设备，查看特定于该设备的设备摘要。  也可以从给定的设备摘要边栏选项卡执行设备特定的操作。 有关设备摘要边栏选项卡的详细信息，[请转到“设备摘要边栏选项卡”](storsimple-8000-device-dashboard.md) 。

    ![单击“设备”磁贴](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>查看活动日志

若要查看在 StorSimple Device Manager 中执行的各种操作，请单击 StorSimple“服务摘要”边栏选项卡左侧的“活动日志”链接。  此时会转到“活动日志”边栏选项卡，其中会显示最近执行的操作的摘要。 

![活动日志](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>后续步骤

* 详细了解如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。

