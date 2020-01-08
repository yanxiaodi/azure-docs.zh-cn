---
title: 规划 Azure 时序见解预览版环境 | Microsoft Docs
description: 计划 Azure 时序见解预览版环境。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6141f898a33b4b37c2a1f16e115b184e21163a5a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300693"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>计划 Azure 时序见解预览版环境

本文介绍有关快速计划和开始使用 Azure 时序见解预览版的最佳做法。

> [!NOTE]
> 有关规划正式版时序见解实例的最佳做法，请参阅[规划 Azure 时序见解正式版环境](time-series-insights-environment-planning.md)。

## <a name="best-practices-for-planning-and-preparation"></a>有关计划和准备的最佳实践

若要开始使用时序见解，最好是了解：

* 在[预配时序见解预览版环境](#the-preview-environment)时可用的功能。
* [时序 ID 和时间戳属性](#configure-time-series-ids-and-timestamp-properties)是什么。
* [新时序模型](#understand-the-time-series-model)是什么，以及如何生成自己的模型。
* 如何[在 JSON 中高效地发送事件](#shape-your-events)。
* 时序见解[业务灾难恢复选项](#business-disaster-recovery)。

Azure 时序见解采用即用即付业务模型。 有关费用和容量的详细信息，请参阅[时序见解定价](https://azure.microsoft.com/pricing/details/time-series-insights/)。

## <a name="the-preview-environment"></a>预览版环境

预配 Azure 时序见解预览版环境时，会创建两个 Azure 资源：

* Azure 时序见解预览版环境
* Azure 存储常规用途 V1 帐户

若要开始，需要三个附加项：

* [时序模型](./time-series-insights-update-tsm.md)
* [连接到时序见解的事件源](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [事件源的事件](./time-series-insights-send-events.md)它们映射到模型并且采用有效 JSON 格式

## <a name="configure-time-series-ids-and-timestamp-properties"></a>配置时序 ID 和时间戳属性

若要创建新的时序见解环境，请选择时序 ID。 此操作用作数据的逻辑分区。 如前所述，请确保时序 ID 已准备就绪。

> [!IMPORTANT]
> 时序 ID 不可变并且以后无法更改。 在进行最终选择和首次使用之前验证每个 ID。

可以选择最多三个键以唯一区分资源。 有关详细信息，请阅读[选择时序 ID 的最佳做法](./time-series-insights-update-how-to-id.md)和[存储和入口](./time-series-insights-update-storage-ingress.md)。

时间戳属性也十分重要。 可以在添加事件源时指定此属性。 每个事件源都有一个可选的时间戳属性，它用于随时间推移跟踪事件源。 时间戳值区分大小写，并且必须按照每个事件源的单独规范设置格式。

> [!TIP]
> 验证事件源的格式设置和分析要求。

如果留空，则事件源的事件排队时间会用作事件时间戳。 如果发送历史数据或批处理事件，则自定义时间戳属性比默认事件排队时间更有帮助。 有关详细信息，请阅读如何[在 Azure IoT 中心中添加事件源](./time-series-insights-how-to-add-an-event-source-iothub.md)。

## <a name="understand-the-time-series-model"></a>了解时序模型

现在可以配置时序见解环境的时序模型。 通过新模型可以轻松查找和分析 IoT 数据。 它可实现时序数据的特选、维护和扩充，并可帮助准备供使用者使用的数据集。 该模型使用时序 Id，该 Id 映射到将唯一资源与变量（称为类型和层次结构）关联的实例。 了解新的[时序模型](./time-series-insights-update-tsm.md)。

模型是动态的，因此可以随时生成。 若要快速开始，请先生成并上传它，然后再将数据推送到时序见解。 若要生成模型，请参阅[使用时序模型](./time-series-insights-update-how-to-tsm.md)。

对于许多客户而言，时序模型映射到已实施的现有资产模型或 ERP 系统。 如果没有现有模型，则[提供](https://github.com/Microsoft/tsiclient)了预生成用户体验以快速启动并运行。 若要了解模型可如何帮助你，请查看[示例演示环境](https://insights.timeseries.azure.com/preview/demo)。

## <a name="shape-your-events"></a>塑造事件

可以验证将事件发送到时序见解的方法。 理想情况下，事件非规范化且高效。

一个好的经验法则是：

* 将元数据存储在时序模型中。
* 时序模式、实例字段和事件仅包括必要信息，例如：时序 ID 或时间戳。

有关详细信息，请参[塑造事件](./time-series-insights-send-events.md#json)。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>后续步骤

- 若要规划业务恢复配置选项，请查看 [Azure 顾问](../advisor/advisor-overview.md)。

- 详细了解时序见解预览版中的[存储和流入量](./time-series-insights-update-storage-ingress.md)。

- 了解时序见解预览版中的[数据建模](./time-series-insights-update-tsm.md)。