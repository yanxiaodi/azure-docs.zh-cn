---
title: Azure Application Insights 遥测数据模型 - 依赖项遥测 | Microsoft Docs
description: 适用于依赖项遥测的 Application Insights 数据模型
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 3e3d6b8fdc9ac8dd28f73fecd6231e97a5645407
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60901016"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>依赖项遥测：Application Insights 数据模型

[Application Insights](../../azure-monitor/app/app-insights-overview.md) 中的依赖项遥测表示受监视组件与远程组件（如 SQL 或 HTTP 终结点）的交互。

## <a name="name"></a>Name

通过此依赖项调用启动的命令的名称。 低基数值。 示例包括存储过程名称和 URL 路径模板。

## <a name="id"></a>ID

依赖项调用实例的标识符。 用于与此依赖项调用所对应的请求遥测项关联。 有关详细信息，请参阅[关联](../../azure-monitor/app/correlation.md)页。

## <a name="data"></a>数据

此依赖项调用启动的命令。 示例包括使用所有查询参数的 SQL 语句和 HTTP URL。

## <a name="type"></a>Type

依赖项类型名称。 低基数值，用于对依赖项进行逻辑分组和解释 commandName 和 resultCode 等其他字段。 示例包括 SQL、Azure 表和 HTTP。

## <a name="target"></a>确定目标

依赖项调用的目标站点。 示例包括服务器名称、主机地址。 有关详细信息，请参阅[关联](../../azure-monitor/app/correlation.md)页。

## <a name="duration"></a>Duration

请求持续时间，格式为：`DD.HH:MM:SS.MMMMMM`。 必须小于 `1000` 天。

## <a name="result-code"></a>结果代码

依赖项调用的结果代码。 示例包括 SQL 错误代码和 HTTP 状态代码。

## <a name="success"></a>Success

指示调用成功还是失败。

## <a name="custom-properties"></a>自定义属性

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>自定义度量值

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>后续步骤

- 为 [.NET](../../azure-monitor/app/asp-net-dependencies.md) 设置依赖项跟踪。
- 为 [Java](../../azure-monitor/app/java-agent.md) 设置依赖项跟踪。
- [编写自定义依赖项遥测](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](data-model.md)。
- 查看 Application Insights 支持的[平台](../../azure-monitor/app/platforms.md)。
