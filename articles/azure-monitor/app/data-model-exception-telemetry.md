---
title: Azure Application Insights 遥测数据模型 - 异常遥测 | Microsoft Docs
description: 适用于异常遥测的 Application Insights 数据模型
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: efd7ad43ee9a2206f474621612eca7dfe5079f99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60908056"
---
# <a name="exception-telemetry-application-insights-data-model"></a>异常遥测：Application Insights 数据模型

在 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 中，异常实例表示在受监视应用程序的执行过程中出现的已处理或未经处理的异常。

## <a name="problem-id"></a>问题 ID

代码中引发异常的标识符。 用于对异常进行分组。 通常为异常类型和调用堆栈中某个函数的组合。

最大长度：1024 个字符

## <a name="severity-level"></a>严重性级别

跟踪严重性级别。 值可以是 `Verbose`、`Information`、`Warning`、`Error`、`Critical`。

## <a name="exception-details"></a>异常详细信息

（将进行扩展）

## <a name="custom-properties"></a>自定义属性

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>自定义度量值

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>后续步骤

- 请参阅[数据模型](data-model.md)，了解 Application Insights 的类型和数据模型。
- 了解如何[使用 Application Insights 诊断 Web 应用中的异常](../../azure-monitor/app/asp-net-exceptions.md)。
- 查看 Application Insights 支持的[平台](../../azure-monitor/app/platforms.md)。
