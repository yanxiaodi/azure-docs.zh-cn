---
title: 将来自 Azure 资源的日志与 SIEM 系统相集成 | Microsoft Docs
description: 了解 Azure 日志集成及其主要功能和工作原理。
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9ba4a64268bcc57f04e92be83edb2ac71f18bcaf
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727589"
---
# <a name="introduction-to-azure-log-integration"></a>Azure 日志集成简介

>[!IMPORTANT]
> Azure 日志集成功能将由06/15/2019 弃用。 AzLog 下载已于 2018 年 6 月 27 日禁用。 有关下一步该怎么做的指导，请查看文章[使用 Azure Monitor 与 SIEM 工具集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Azure 日志集成可用于简化将 Azure 日志与本地安全信息和事件管理 (SIEM) 系统集成的任务。

 建议的集成 Azure 日志的方法是使用 SIEM 供应商提供的连接器。 Azure Monitor 提供将日志流式传输到事件中心的功能，SIEM 供应商可以编写连接器以进一步将日志从事件中心集成到 SIEM 中。  有关此连接器工作原理的说明，请参阅[针对数据事件中心的监视器流监视](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)中的说明。 该文章还列出了直接 Azure 连接器已经可用的 SIEM。  

> [!IMPORTANT]
> 如果你的主要兴趣是收集虚拟机日志，那么大多数 SIEM 供应商会将此包含在其解决方案中。 使用 SIEM 供应商的连接器始终是首选替代方法。

有关 Azure 日志集成功能的文档仍将维护，直到该功能弃用。

进一步阅读以了解有关 Azure 日志集成功能的更多信息：

Azure 日志集成从来自 Azure 资源的 Windows 事件查看器日志、[Azure 活动日志](/azure/azure-monitor/platform/activity-logs-overview)、[Azure 安全中心警报](/azure/security-center/security-center-intro)和 [Azure 诊断日志](/azure/azure-monitor/platform/diagnostic-logs-overview)收集 Windows 事件。 此集成可帮助你的 SIEM 解决方案为你的所有资产（无论是本地的还是云中的）提供统一的仪表板。 可以使用仪表板接收、聚合、关联和分析安全事件的警报。

> [!NOTE]
> 当前，Azure 日志集成仅支持 Azure 商业版云和 Azure 政府版云。 不支持其他云。

![Azure 日志集成流程](media/azure-log-integration-overview/azure-log-integration.png)

## <a name="what-logs-can-i-integrate"></a>可以集成哪些日志？

Azure 针对每个 Azure 服务生成大量日志记录。 这些日志代表三种日志类型：

* **控制/管理日志**：提供 [Azure 资源管理器](/azure/azure-resource-manager/resource-group-overview)创建、更新和删除操作的相关信息。 Azure 活动日志是此类日志的示例。
* **数据平面日志**：提供使用 Azure 资源时引发的事件的相关信息。 此日志类型的一个示例是位于 Windows 虚拟机上 Windows 事件查看器的**系统**、**安全**和**应用程序**频道。 另一个示例是通过 Azure Monitor 配置的 Azure 诊断日志记录。
* **已处理的事件**：提供已为你处理的已分析事件和警报信息。 此类事件的一个示例是 Azure 安全中心警报。 Azure 安全中心处理并分析你的订阅来提供与你当前的安全状况相关的警报。

Azure 日志集成支持 ArcSight、QRadar 和 Splunk。 与你的 SIEM 供应商核实，确定该供应商是否具有原生连接器。 如果有原生连接器可用，则不要使用 Azure 日志集成。

如果没有其他选项可用，请考虑使用 Azure 日志集成。 下表列出了我们的建议：

|SIEM | 已在使用 Azure 日志集成器的客户 | 正在考察 SIEM 集成选项的客户|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | 开始迁移到[适用于 Splunk 的 Azure Monitor 加载项](https://splunkbase.splunk.com/app/3534/)。 | 使用 [Splunk 连接器](https://splunkbase.splunk.com/app/3534/)。 |
|**QRadar** | 迁移到或开始使用[将 Azure 监视数据流式传输到事件中心以便外部工具使用](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)的最后一部分中提到的 QRadar 连接器。 | 使用[将 Azure 监视数据流式传输到事件中心以便外部工具使用](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)的最后一部分中提到的 QRadar 连接器。 |
|**ArcSight** | 继续使用 Azure 日志集成器，直到有连接器可用，然后迁移到基于连接器的解决方案。  | 请考虑使用 Azure Monitor 日志作为替代方法。 除非你愿意在有连接器变得可用时经历迁移过程，否则不要采用 Azure 日志集成。 |

> [!NOTE]
> 虽然 Azure 日志集成是免费解决方案，但是存在与日志文件信息存储相关的 Azure 存储费用。

如果需要帮助，可以创建[支持请求](/azure/azure-supportability/how-to-create-azure-support-request)。 对于服务，请选择“日志集成”。

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 日志集成。 若要详细了解 Azure 日志集成和支持的日志类型，请参阅以下文章：

* [Azure 日志集成入门](azure-log-integration-get-started.md)。 本教程将指导你完成 Azure 日志集成的安装。 它还介绍了如何集成来自 Windows Azure 诊断 (WAD) 存储的日志、Azure 活动日志、Azure 安全中心警报和 Azure Active Directory 审核日志。
* [Azure 日志集成常见问题解答 (FAQ)](azure-log-integration-faq.md)。 此常见问题解答回答了关于 Azure 日志集成的常见问题。
* 详细了解如何[将 Azure 监视数据流式传输到事件中心以便外部工具使用](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)。

