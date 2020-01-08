---
title: 使用 Azure 数据资源管理器查询 Azure Monitor 中的数据（预览版）
description: 在本主题中，通过使用 Application Insights 和 Log Analytics 为跨产品查询创建 Azure 数据资源管理器代理来查询 Azure Monitor 中的数据
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 43d91bff6b8b67e79a9549c1524f918166c9adc4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933999"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>使用 Azure 数据资源管理器查询 Azure Monitor 中的数据（预览版）

Azure 数据资源管理器代理群集（ADX 代理）是一个实体，使你能够在[Azure Monitor](/azure/azure-monitor/)服务中的 Azure 数据资源管理器、 [Application Insights （AI）](/azure/azure-monitor/app/app-insights-overview)和[Log Analytics （LA）](/azure/azure-monitor/platform/data-platform-logs)之间执行跨产品查询。 可以将 Azure Monitor Log Analytics 工作区或 Application Insights 应用映射为代理群集。 然后，你可以使用 Azure 数据资源管理器工具查询代理群集并在跨群集查询中引用它。 本文介绍如何连接到代理群集、将代理群集添加到 Azure 数据资源管理器 Web UI，以及如何从 Azure 数据资源管理器为 AI 应用或 LA 工作区运行查询。

Azure 数据资源管理器代理流： 

![ADX 代理流](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>先决条件

> [!NOTE]
> ADX 代理处于预览模式。 若要启用此功能，请联系[ADXProxy](mailto:adxproxy@microsoft.com)团队。

## <a name="connect-to-the-proxy"></a>连接到代理

1. 在连接到 Log Analytics 或 Application Insights 群集之前，验证 Azure 数据资源管理器本地群集（如*帮助*群集）是否出现在左侧菜单上。

    ![ADX 本机群集](media/adx-proxy/web-ui-help-cluster.png)

1. 在 Azure 数据资源管理器 UI （ https://dataexplorer.azure.com/clusters) ，选择 "**添加群集**"。

1. 在 "**添加群集**" 窗口中：

    * 向 LA 或 AI 群集添加 URL。 例如： `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * 选择 **添加** 。

    ![添加群集](media/adx-proxy/add-cluster.png)

    如果向多个代理群集添加连接，请为每个代理群集指定一个不同的名称。 否则，它们将在左窗格中具有相同的名称。

1. 建立连接后，将在左窗格中显示你的 "拉" 或 "AI" 群集，其中包含本机 ADX 群集。 

    ![Log Analytics 和 Azure 数据资源管理器群集](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>运行查询

可以使用 Kusto 资源管理器、ADX web 资源管理器、Jupyter Kqlmagic 或 REST API 来查询代理群集。 

> [!TIP]
> * 数据库名称的名称应与代理群集中指定的资源的名称相同。 名称区分大小写。
> * 在 "跨群集查询" 中，请确保 Application Insights 应用和 Log Analytics 工作区的命名是正确的。
>     * 如果名称包含特殊字符，则会将其替换为代理群集名称中的 URL 编码。 
>     * 如果名称包含的字符不符合[KQL 标识符名称规则](/azure/kusto/query/schema-entities/entity-names)，则会将它们替换为 **-** 短划线字符。

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>针对本机 Azure 数据资源管理器群集进行查询 

在 Azure 数据资源管理器群集（如*帮助*群集中的*StormEvents*表）上运行查询。 运行查询时，验证是否在左窗格中选择了本机 Azure 数据资源管理器群集。

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![查询 StormEvents 表](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>针对 LA 或 AI 群集进行查询

在 LA 或 AL 群集上运行查询时，请确保在左窗格中选择了 "LA" 或 "AI" 群集。 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Query LA 工作区](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>从 ADX 代理查询 LA 或 AI 群集  

从代理在 LA 或 AI 群集上运行查询时，请确认已在左窗格中选择 "ADX native 群集"。 下面的示例演示如何使用本机 ADX 群集查询 LA 工作区

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![从 Azure 数据资源管理器代理进行查询](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>从 ADX 代理交叉查询 LA 或 AI 群集和 ADX 群集 

当你从代理运行跨群集查询时，请验证是否已在左窗格中选择 "ADX native 群集"。 以下示例演示如何将 ADX 群集表（使用`union`）与 LA 工作区结合使用。

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![从 Azure 数据资源管理器代理进行交叉查询](media/adx-proxy/cross-query-adx-proxy.png)

使用运算符（而不是联合），可能需要[`hint`](/azure/kusto/query/joinoperator#join-hints)在 Azure 数据资源管理器本地群集（而不是代理）上运行它。 [ `join` ](/azure/kusto/query/joinoperator) 

## <a name="additional-syntax-examples"></a>其他语法示例

调用 Application Insights （AI）或 Log Analytics （LA）群集时，可以使用以下语法选项：

|语法说明  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 群集中仅包含此订阅中定义的资源的数据库（**建议跨群集查询使用**） |   cluster （`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`） | cluster （`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`）     |
| 包含此订阅中的所有应用/工作区的群集    |     cluster （`https://ade.applicationinsights.io/subscriptions/<subscription-id>`）    |    cluster （`https://ade.loganalytics.io/subscriptions/<subscription-id>`）     |
|包含订阅中的所有应用/工作区并属于此资源组成员的群集    |   cluster （`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`）      |    cluster （`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`）      |
|仅包含此订阅中定义的资源的群集      |    cluster （`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`）    |  cluster （`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`）     |

## <a name="next-steps"></a>后续步骤

[编写查询](write-queries.md)
