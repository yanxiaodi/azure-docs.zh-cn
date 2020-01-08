---
title: Visual Studio Data Lake 工具中的顶点执行视图
description: 本文介绍如何使用顶点执行视图测试 Data Lake Analytics 作业。
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309715"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>在用于 Visual Studio 的 Data Lake 工具中使用顶点执行视图
了解如何使用顶点执行视图测试 Data Lake Analytics 作业。


## <a name="open-the-vertex-execution-view"></a>打开顶点执行视图
在用于 Visual Studio 的 Data Lake 工具中打开 U-SQL 作业。 在左下角单击“顶点执行视图”。 系统可能提示先加载配置文件，此操作可能需要一些时间，具体取决于网络连接状况。

![Data Lake Analytics 工具顶点执行视图](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>了解顶点执行视图
顶点执行视图由三个部分组成：

![Data Lake Analytics 工具顶点执行视图](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

可在左边的“顶点选择器”按功能选择顶点（例如：前 10 个数据读取，或按阶段选择）。 其中一个最常用筛选器是查看“关键路径上的顶点”。 “关键路径”是 U-SQL 作业顶点中最长的链。 了解关键路径有助于通过检查哪个顶点耗时最长来优化作业。
  
![Data Lake Analytics 工具顶点执行视图](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

顶部中心窗格显示“所有顶点的运行状态”。
  
![Data Lake Analytics 工具顶点执行视图](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

底部中心窗格显示每个顶点的信息：
* 进程名称：顶点实例的名称。 它由 StageName|VertexName|VertexRunInstance 中的不同部分构成。 例如，SV7_Split[62].v1 顶点表示阶段 SV7_Split 中顶点数 62 的第二个运行实例（.v1，从 0 开始索引）。
* 读取/写入的总数据：此顶点读取/写入的数据。
* 状态/退出状态：顶点结束时的最终状态。
* 退出代码/失败类型：顶点失败时的错误。
* 创建原因：顶点的创建原因。
* 资源延迟/进程延迟/PN 队列延迟：顶点等待资源、处理数据和在队列中等待所需的时间。
* 进程/创建者 GUID：当前正在运行的顶点或其创建者的 GUID。
* 版本：运行中顶点的第 N 个实例（系统可能因故障转移、计算冗余等多种原因安排顶点的新实例）
* 版本创建时间。
* 进程创建开始时间/进程排队时间/进程开始时间/进程完成时间：顶点进程开始创建的时间；顶点进程开始排队的时间；特定顶点进程开始时间；特定顶点完成时间。

## <a name="next-steps"></a>后续步骤
* 若要记录诊断信息，请参阅[访问 Azure Data Lake Analytics 的诊断日志](data-lake-analytics-diagnostic-logs.md)
* 若要查看更复杂的查询，请参阅[使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 若要查看作业详细信息，请参阅[对 Azure Data Lake Analytics 作业使用作业浏览器和作业视图](data-lake-analytics-data-lake-tools-view-jobs.md)
