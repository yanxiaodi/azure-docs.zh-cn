---
title: 在 Azure 安全中心集成安全解决方案 | Microsoft Docs
description: 了解如何将 Azure 安全中心与合作伙伴集成，以增强 Azure 资源的总体安全性。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 0a3bc6bcae2f06173cbc334ffe80e2dfa001e407
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309266"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>在 Azure 安全中心集成安全解决方案
本文档介绍如何管理已连接到 Azure 安全中心的安全解决方案，以及如何添加新的安全解决方案。

> [!NOTE]
> 安全解决方案子集已于2019年7月31日停用。 有关详细信息和备用服务，请参阅用[安全中心功能的停用（2019 年 7 月）](security-center-features-retirement-july2019.md#menu_solutions)。

## <a name="integrated-azure-security-solutions"></a>集成式 Azure 安全解决方案
可以通过安全中心轻松地在 Azure 中启用集成式安全解决方案。 优势包括：

- **简化部署**：安全中心提供对集成式合作伙伴解决方案的简化预配。 对于反恶意软件和漏洞评估等解决方案，安全中心可以在虚拟机上设置代理。 对于防火墙设备，安全中心可以负责处理所需的大量网络配置。
- **集成检测**：自动收集、聚合合作伙伴解决方案中的安全事件，并将其作为安全中心警报和事件的一部分进行显示。 这些事件还与来自其他源的检测融合在一起，以提供高级威胁检测功能。
- **统一的运行状况监视和管理**：客户可以使用集成式运行状况事件，一目了然地监视所有合作伙伴解决方案。 可通过使用合作伙伴解决方案轻松地访问高级设置，进行基本管理。

目前，集成的安全解决方案包括通过[Qualys](https://www.qualys.com/public-cloud/#azure)和[Rapid7](https://www.rapid7.com/products/insightvm/)和 Microsoft 应用程序网关 Web 应用程序防火墙的漏洞评估。

> [!NOTE]
> 安全中心不会将 Microsoft Monitoring Agent 安装在合作伙伴虚拟设备上，因为大多数安全供应商都禁止在其设备上运行外部代理。
>
>

## <a name="how-security-solutions-are-integrated"></a>安全中心如何集成
从安全中心部署的 Azure 安全解决方案是自动连接的。 你还可以连接其他安全数据源，包括在本地或其他云中运行的计算机。

![合作伙伴解决方案集成](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>管理集成式 Azure 安全解决方案和其他数据源

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。

2. 在 **Microsoft Azure 菜单**上选择“安全中心”。 此时会打开“安全中心 - 概览”。

3. 在“安全中心”菜单下，选择“安全解决方案”。

   ![安全中心概述](./media/security-center-partner-integration/overview.png)

在 "**安全解决方案**" 中，可以查看集成的 Azure 安全解决方案的运行状况，并运行基本的管理任务。

### <a name="connected-solutions"></a>已连接的解决方案

"**已连接解决方案**" 部分包含当前连接到安全中心的安全解决方案。 它还显示每个解决方案的运行状况状态。  

![已连接的解决方案](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

合作伙伴解决方案的状态可能为：

* 正常（绿色）-没有运行状况问题。
* 不正常（红色）-存在需要立即关注的运行状况问题。
* 运行状况问题（橙色）- 解决方案已停止报告其运行状况。
* 未报告（灰色）-解决方案尚未报告任何内容，并且没有可用的运行状况数据。 如果某个解决方案刚刚连接并且仍在部署，则该解决方案的状态可能未报告。

> [!NOTE]
> 如果没有运行状况数据可用，则安全中心会显示上次收到事件的日期和时间以指示解决方案是否正在报告。 如果没有运行状况数据，并且在过去14天内未收到任何警报，则安全中心会指示该解决方案不正常或未进行报告。
>
>

1. 选择 "**查看**" 以获取其他信息和选项，例如：

   - **解决方案控制台**。 打开此解决方案的管理体验。
   - **链接 VM**。 打开 "链接应用程序" 页。 此处，可将资源连接到合作伙伴解决方案。
   - **删除解决方案**。
   - **配置**。

   ![合作伙伴解决方案详细信息](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>已发现的解决方案

安全中心会自动发现在 Azure 中运行但未连接到安全中心的安全解决方案，并显示 "**发现的解决方案**" 部分中的解决方案。 这些解决方案包括 Azure 解决方案，例如[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)和合作伙伴解决方案。

> [!NOTE]
> 在订阅级别，安全中心标准层是已发现解决方案功能所必需的。 有关定价层的详细信息，请参阅[定价](security-center-pricing.md)。
>
>

在解决方案下选择 "**连接**"，以与安全中心集成并通知安全警报。

![已发现的解决方案](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

### <a name="add-data-sources"></a>添加数据源

“添加数据源”部分包括其他可以连接的可用数据源。 如需从任何此类源添加数据的说明，请单击“添加”。

![数据源](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>将数据导出到 SIEM

可以配置 Siem 或其他监视工具来接收 Azure 安全中心事件。

Azure 安全中心的所有事件都将发布到 Azure Monitor 的 Azure[活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)。 Azure Monitor 使用[合并管道](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)将数据流式传输到事件中心，然后可将数据提取到监视工具中。

以下各节介绍如何将数据配置为流式传输到事件中心。 步骤假设 Azure 订阅中已配置了 Azure 安全中心。

### <a name="high-level-overview"></a>综合概述

![高级概述](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>公开给 SIEM 的 Azure 安全数据是什么？

在此版本中，我们将公开[安全警报。](../security-center/security-center-managing-and-responding-alerts.md) 在即将发布的版本中，我们将通过安全建议丰富数据集。

### <a name="how-to-set-up-the-pipeline"></a>如何设置管道

#### <a name="create-an-event-hub"></a>创建事件中心

在开始之前，请[创建事件中心命名空间](../event-hubs/event-hubs-create.md)-所有监视数据的目标。

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>将 Azure 活动日志流式传输到事件中心

请参阅以下文章：[将活动日志流式传输到事件中心](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>安装合作伙伴 SIEM 连接器 

通过 Azure Monitor 将监视数据路由到事件中心，可与合作伙伴 SIEM 和监视工具轻松集成。

请参阅以下文章，获取支持的[siem](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-resource-logs-sent-to-an-event-hub)列表

### <a name="example-for-querying-data"></a>查询数据示例 

下面是一些可用于拉取警报数据的 Splunk 查询：

| **查询说明** | **查询** |
|----|----|
| 所有警报| index=main Microsoft.Security/locations/alerts|
| 按名称汇总操作计数| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| 获取警报信息：时间、名称、状态、ID 和订阅 | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>后续步骤

本文介绍了如何在安全中心集成合作伙伴的解决方案。 若要详细了解安全中心，请参阅以下文章：

* [在安全中心进行安全运行状况监视](security-center-monitoring.md)。 了解如何监视 Azure 资源的运行状况。
* [Azure 安全中心常见问题解答](security-center-faq.md)。 获取安全中心使用方面的常见问题解答。
* [Azure 安全性博客](https://blogs.msdn.com/b/azuresecurity/)。 查找关于 Azure 安全性及合规性的博客文章。
