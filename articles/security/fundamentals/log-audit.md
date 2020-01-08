---
title: Azure 日志记录和审核 | Microsoft Docs
description: 了解如何利用日志记录数据深入了解应用程序的情况。
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: d64cdce34127b066aedc8a5fcd6ec3a891b38c5e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262851"
---
# <a name="azure-logging-and-auditing"></a>Azure 日志记录和审核

Azure 提供多种不同的可配置安全审核和日志记录选项，帮助你识别安全策略和机制方面的差距。 本文介绍如何生成、收集和分析 Azure 上托管的服务的安全日志。

> [!Note]
> 本文中的某些建议可能会导致数据、网络或计算资源使用量增加，还可能导致许可或订阅成本增加。

## <a name="types-of-logs-in-azure"></a>Azure 中的日志类型

云应用程序很复杂，包含很多移动部件。 日志可以提供数据，从而帮助确保应用程序始终处于正常运行状态。 日志可帮助排查过去的问题，或避免潜在的问题。 此外，日志有助于改进应用程序的性能或可维护性，或者实现本来需要手动干预的操作的自动化。

Azure 日志划分为以下类型：
* **控制/管理日志**提供有关 Azure 资源管理器 CREATE、UPDATE 和 DELETE 操作的信息。 有关详细信息，请参阅 [Azure 活动日志](../../azure-monitor/platform/activity-logs-overview.md)。

* **数据平面日志**提供作为 Azure 资源使用情况的一部分引发的事件的相关信息。 此类日志的示例是虚拟机 (VM) 中的 Windows 事件系统、安全性、应用程序日志以及通过 Azure Monitor 配置的[诊断日志](../../azure-monitor/platform/resource-logs-overview.md)。

* **已处理的事件**提供已以用户名义处理的分析事件/警报的相关信息。 此类日志的示例是 [Azure 安全中心警报](../../security-center/security-center-managing-and-responding-alerts.md)，[Azure 安全中心](../../security-center/security-center-intro.md)已处理和分析了订阅，并提供简明的安全警报。

下表列出了 Azure 中最重要的日志类型：

| 日志类别 | 日志类型 | 用法 | 集成 |
| ------------ | -------- | ------ | ----------- |
|[活动日志](../../azure-monitor/platform/activity-logs-overview.md)|Azure 资源管理器资源上的控制平面事件|  提供见解，方便用户了解对订阅中的资源执行的操作。|    Rest API、[Azure Monitor](../../azure-monitor/platform/activity-logs-overview.md)|
|[Azure 诊断日志](../../azure-monitor/platform/resource-logs-overview.md)|关于订阅中 Azure 资源管理器资源操作频繁生成的数据|    提供见解，以便深入了解资源本身执行的操作。| Azure Monitor，[Stream](../../azure-monitor/platform/resource-logs-overview.md)|
|[Azure AD 报告](../../active-directory/reports-monitoring/overview-reports.md)|日志和报告 | 报告有关用户和组管理的用户登录活动和系统活动信息。|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[虚拟机和云服务](../../azure-monitor/learn/quick-collect-azurevm.md)|Windows 事件日志服务和 Linux Syslog|  在虚拟机上捕获系统数据和日志记录数据，并将这些数据传输到所选的存储帐户中。|   Azure Monitor 中的 Windows（使用 Windows Azure 诊断 [[WAD](../../monitoring-and-diagnostics/azure-diagnostics.md)] 存储）和 Linux|
|[Azure 存储分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|存储执行日志记录并为存储帐户提供指标数据|提供相关信息，以便深入了解如何跟踪请求、分析使用情况趋势以及诊断存储帐户的问题。|   REST API 或[客户端库](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[网络安全组 (NSG) 流日志](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|采用 JSON 格式，并根据规则显示出站和入站流|显示有关通过网络安全组的入口和出口 IP 流量的信息。|[Azure 网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Application Insights](../../azure-monitor/app/app-insights-overview.md)|日志、异常和自定义诊断|  提供多个平台上面向 Web 开发人员的应用程序性能监视 (APM) 服务。| REST API，[Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|处理数据/安全警报|    Azure 安全中心警报, Azure Monitor 日志警报|    提供安全信息和警报。|  REST API，JSON|

### <a name="activity-logs"></a>活动日志

[Azure 活动日志](../../azure-monitor/platform/activity-logs-overview.md)提供有关对订阅中的资源执行的操作的见解。 活动日志以前称为“审核日志”或“操作日志”，因为它们报告订阅的[控制平面事件](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/)。 

活动日志可帮助确定写入操作（即 PUT、POST 或 DELETE）的“内容、执行者和时间”。 活动日志还可以帮助了解该操作和其他相关属性的状态。 活动日志不包括读取 (GET) 操作。

在本文中，PUT、POST 和 DELETE 是指活动日志中包含的针对资源的所有写入操作。 例如，活动日志可用于在排查问题时查找错误，或用于监视组织内用户对资源的修改。

![活动日志示意图](./media/log-audit/azure-log-audit-fig1.png)

可以通过 Azure 门户、[Azure CLI](../../storage/common/storage-azure-cli.md)、PowerShell cmdlet 和 [Azure Monitor REST API](../../azure-monitor/platform/rest-api-walkthrough.md) 从活动日志检索事件。 活动日志的数据保留期为 90 天。

活动日志事件的集成方案：

* [创建活动日志事件触发的电子邮件或 webhook 警报](../../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md)。

* [将活动日志流式传输到事件中心](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)，方便第三方服务或自定义分析解决方案（例如 PowerBI）引入。

* 在 PowerBI 中使用 [PowerBI 内容包](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)分析活动日志。

* [将活动日志保存到存储帐户进行存档或手动检查](../../azure-monitor/platform/archive-activity-log.md)。 可以使用日志配置文件指定保留时间（天）。

* 在 Azure 门户中查询和查看活动日志。

* 通过 PowerShell Cmdlet、Azure CLI 或 REST API 查询活动日志。

* 将活动日志与日志配置文件一起导出到[Azure Monitor 日志](../../log-analytics/log-analytics-queries.md)。

可以使用与发出日志的订阅不同的订阅中的存储帐户或[事件中心命名空间](../../event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-capture.md)。 配置此设置的任何人必须对两个订阅都具有适当的[基于角色的访问控制 (RBAC)](../../role-based-access-control/role-assignments-portal.md) 访问权限。

### <a name="azure-diagnostics-logs"></a>Azure 诊断日志

Azure 诊断日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 这些日志的内容因资源类型而异。 例如，[Windows 事件系统日志](../../azure-monitor/platform/data-sources-windows-events.md)是适用于 VM 的一个诊断日志类别，而 [Blob、表和队列日志](../../storage/common/storage-monitor-storage-account.md)是适用于存储帐户的诊断日志类别。 诊断日志不同于活动日志，后者用于提供有关对订阅中的资源执行的操作的见解。

![Azure 诊断日志示意图](./media/log-audit/azure-log-audit-fig2.png)

Azure 诊断日志提供多个配置选项，例如 Azure 门户、PowerShell、Azure CLI 和 REST API。

**集成方案**

* 将诊断日志保存到[存储帐户](../../azure-monitor/platform/archive-diagnostic-logs.md)进行审核或手动检查。 可以使用诊断设置指定保留时间（天）。

* 将诊断日志流式传输到[事件中心](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)，方便第三方服务或自定义分析解决方案（例如 [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)）引入。

* 用[Azure Monitor 日志](../../log-analytics/log-analytics-queries.md)分析它们。

**诊断日志支持的服务和架构以及每种资源类型支持的日志类别**


| 服务 | 架构和文档 | 资源类型 | 类别 |
| ------- | ------------- | ------------- | -------- |
|Azure 负载均衡器| [Azure Monitor 负载均衡器的日志 (预览版)](../../load-balancer/load-balancer-monitor-log.md)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|网络安全组|[网络安全组 Azure Monitor 日志](../../virtual-network/virtual-network-nsg-manage-log.md)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure 应用程序网关|[应用程序网关的诊断日志记录](../../application-gateway/application-gateway-diagnostics.md)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Key Vault 日志](../../key-vault/key-vault-logging.md)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure 搜索|[允许并使用搜索流量分析](../../search/search-traffic-analytics.md)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[访问 Data Lake Store 的诊断日志](../../data-lake-store/data-lake-store-diagnostic-logs.md)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|审核<br>请求|
|Azure Data Lake Analytics|[访问 Data Lake Analytics 的诊断日志](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|审核<br>请求|
|Azure 逻辑应用|[逻辑应用 B2B 自定义跟踪架构](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch 诊断日志](../../batch/batch-diagnostics.md)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure 自动化|[Azure 自动化 Azure Monitor 日志](../../automation/automation-manage-send-joblogs-log-analytics.md)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure 事件中心|[事件中心诊断日志](../../event-hubs/event-hubs-diagnostic-logs.md)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure 流分析|[作业诊断日志](../../stream-analytics/stream-analytics-job-diagnostic-logs.md)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|执行<br>创作|
|Azure 服务总线|[服务总线诊断日志](../../service-bus-messaging/service-bus-diagnostic-logs.md)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Azure Active Directory 报告

Azure Active Directory (Azure AD) 包括针对用户目录的安全报告、活动报告和审核报告。 [Azure AD 审核报告](../../active-directory/active-directory-reporting-azure-portal.md)可帮助识别用户 Azure AD 实例中发生的特权操作。 特权操作包括提升更改（例如，创建角色或密码重置）、更改策略配置（例如密码策略）或更改目录配置（例如，对域联合身份验证设置的更改）。

报告提供事件名称、执行操作的用户、更改影响的目标资源，以及日期和时间 (UTC) 的审核记录。 用户可以根据[查看审核日志](../../active-directory/reports-monitoring/overview-reports.md)中所述，通过 [Azure 门户](https://portal.azure.com/)检索 Azure AD 的审核事件列表。 

下表列出了包含的报告：

| 安全报表 | 活动报表 | 审核报表 |
| :--------------- | :--------------- | :------------ |
|从未知源登录| 应用程序使用情况：摘要| 目录审核报表|
|多次失败后登录|  应用程序使用情况：详细信息||
|从多个地理区域登录|    应用程序仪表板||
|从具有可疑活动的 IP 地址登录|   帐户设置错误||
|异常登录活动|    单个用户设备||
|从可能受感染的设备登录|   单个用户活动||
|具有异常登录活动的用户| 组活动报表||
||密码重置注册活动报告||
||密码重置活动||

对于应用程序（例如安全信息和事件管理 (SIEM) 系统）、审核和商业智能工具而言，这些报告中的数据可能非常有用。 Azure AD 报告 API 通过一组基于 REST 的 API，可提供对该数据的编程访问权限。 可从各种编程语言和工具中调用这些 [API](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md)。

Azure AD 审核报告中的事件保留期在7-90 天内有所不同, 具体取决于与租户关联的许可证类型。 

> [!Note]
> 有关报告保留期的详细信息，请参阅 [Azure AD 报告保留策略](../../active-directory/reports-monitoring/reference-reports-data-retention.md)。

如果想要将审核事件保留更久，可以使用报告 API 定期将[审核事件](../../active-directory/active-directory-reporting-activity-audit-logs.md)提取到独立的数据存储中。

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>使用 Azure 诊断的虚拟机日志

[Azure 诊断](../../monitoring-and-diagnostics/azure-diagnostics.md)是 Azure 中可对部署的应用程序启用诊断数据收集的功能。 可以从多个源中的任意源使用诊断扩展。 目前支持的源是 [Azure 云服务 Web 角色和辅助角色](../../cloud-services/cloud-services-choose-me.md)。

![使用 Azure 诊断的虚拟机日志](./media/log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabricservice-fabricservice-fabric-overviewmd"></a>运行 Microsoft Windows 和 [Service Fabric](../../service-fabric/service-fabric-overview.md) 的 [Azure 虚拟机](/learn/paths/deploy-a-website-with-azure-virtual-machines/)。

可以通过以下方式在虚拟机上启用 Azure 诊断：

* [使用 Visual Studio 跟踪 Azure 虚拟机](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [在 Azure 虚拟机上远程设置 Azure 诊断](../../virtual-machines/virtual-machines-dotnet-diagnostics.md)

* [使用 PowerShell 在 Azure 虚拟机上设置诊断](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [使用 Azure 资源管理器模板创建具有监视和诊断功能的 Windows 虚拟机](../../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>存储分析

[Azure 存储分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)记录并提供存储帐户的指标数据。 可以使用此数据跟踪请求、分析使用情况趋势以及诊断存储帐户的问题。 存储分析日志记录适用于 [Azure Blob、Azure 队列和 Azure 表存储服务](../../storage/common/storage-introduction.md)。 存储分析记录成功和失败的存储服务请求的详细信息。

可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。 仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果存储帐户的 Blob 终结点中存在活动，而表或队列终结点中没有该活动，则仅创建与 Blob 存储服务有关的日志。

若要使用存储分析，请为每个要监视的服务单独启用它。 可在 [Azure 门户](https://portal.azure.com/)中启用该功能。 有关详细信息，请参阅[在 Azure 门户中监视存储帐户](../../storage/common/storage-monitor-storage-account.md)。 还可以通过 REST API 或客户端库以编程方式启用存储分析。 使用“设置服务属性”操作分别为每项服务启用存储分析。

聚合数据存储在众所周知的 Blob（用于日志记录）和众所周知的表（用于度量）中，可以使用 Blob 存储服务和表存储服务 API 对其进行访问。

存储分析对存储数据的数量限制为 20 TB，这与存储帐户的总限制无关。 所有日志以[块 Blob](../../storage/common/storage-analytics.md) 的形式存储在一个名为 $logs 的容器中，为存储帐户启用存储分析时会自动创建该容器。

> [!Note]
> * 有关计费和数据保留策略的详细信息，请参阅[存储分析和计费](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)。
> * 有关存储帐户限制的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](../../storage/common/storage-scalability-targets.md)。

存储分析记录以下类型的已经过身份验证的匿名请求：

| 已经过身份验证  | 匿名|
| :------------- | :-------------|
| 成功的请求数 | 成功的请求 |
|失败的请求，包括超时、限制、网络、授权和其他错误 | 使用共享访问签名的请求，包括失败和成功的请求 |
| 使用共享访问签名的请求，包括失败和成功的请求 |客户端和服务器的超时错误 |
|   分析数据请求 |    失败的 GET 请求，错误代码为 304（未修改） |
| 不会记录存储分析本身发出的请求，如创建或删除日志。 若要查看所记录数据的完整列表，请参阅[存储分析记录的操作和状态消息](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)和[存储分析日志格式](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)。 | 不会记录所有其他失败的匿名请求。 若要查看所记录数据的完整列表，请参阅[存储分析记录的操作和状态消息](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)和[存储分析日志格式](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)。 |

### <a name="azure-networking-logs"></a>Azure 网络日志

Azure 中的网络日志记录和监视是一个综合性的功能，包括两大类别：

* [网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)：网络观察程序中的功能提供基于方案的网络监视。 此服务包括数据包捕获、下一跃点、IP 流验证、安全组视图和 NSG 流日志。 与单独网络资源的监视不同，方案级监视提供网络资源的端到端视图。

* [资源监视](../../network-watcher/network-watcher-monitoring-overview.md)：资源级监视包括四项功能：诊断日志、指标、故障排除和资源运行状况。 所有这些功能都在网络资源级别构建。

![Azure 网络日志](./media/log-audit/azure-log-audit-fig4.png)

网络观察程序是一个区域性服务，可用于在网络方案级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。

### <a name="network-security-group-flow-logging"></a>网络安全组流日志记录

[NSG 流日志](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)是网络观察程序的一项功能，可用于查看有关通过 NSG 的入口和出口 IP 流量的信息。 这些流日志以 JSON 格式编写，并显示：
* 根据规则显示出站和入站流。
* 该流所适用的 NIC。
* 有关流的 5 元组信息：源或目标 IP、源或目标端口，以及协议。
* 是允许流还是拒绝了流。

虽然流日志针对的是 NSG，但其显示方式不同于其他日志。 流日志仅存储在存储帐户中。

适用于其他日志的保留策略也适用于流日志。 日志的保留策略可以设置为 1 到 365 天。 如果未设置保留策略，则会永久保留日志。

**诊断日志**

定期事件和自发事件由网络资源创建并登录到存储帐户, 并发送到事件中心或 Azure Monitor 日志。 这些日志提供对资源运行状况的见解。 可以在 Power BI 和 Azure Monitor 日志等工具中查看它们。 若要了解如何查看诊断日志, 请参阅[Azure Monitor 日志](../../azure-monitor/insights/azure-networking-analytics.md)。

![诊断日志](./media/log-audit/azure-log-audit-fig5.png)

诊断日志适用于[负载均衡器](../../load-balancer/load-balancer-monitor-log.md)、[网络安全组](../../virtual-network/virtual-network-nsg-manage-log.md)、路由和[应用程序网关](../../application-gateway/application-gateway-diagnostics.md)。

网络观察程序提供诊断日志视图。 此视图包含所有支持诊断日志记录的网络资源。 从此视图中，可以快速方便地启用和禁用网络资源。


除了上述日志记录功能以外，网络观察程序目前还提供以下功能：
- [拓扑](../../network-watcher/view-network-topology.md)：提供网络级视图，显示资源组中网络资源之间的各种互连和关联。

- [可变数据包捕获](../../network-watcher/network-watcher-packet-capture-overview.md)：捕获传入和传出虚拟机的数据包数据。 高级筛选选项和精细控制（例如时间与大小限制设置）提供了多样性。 数据包数据可以存储在 Blob 存储中，或者以 *.cap* 文件格式存储在本地磁盘上。

- [IP 流验证](../../network-watcher/network-watcher-ip-flow-verify-overview.md)：根据流信息 5 元组数据包参数（即目标 IP、源 IP、目标端口、源端口和协议）检查数据包是被允许还是被拒绝。 如果数据包被安全组拒绝，则返回拒绝数据包的规则和组。

- [下一跃点](../../network-watcher/network-watcher-next-hop-overview.md)：确定 Azure 网络结构中路由的数据包的下一跃点，以便能够诊断任何配置不正确的用户定义的路由。

- [安全组视图](../../network-watcher/network-watcher-security-group-view-overview.md)：获取在 VM 上应用的有效安全规则。

- [虚拟网络网关和连接故障排除](../../network-watcher/network-watcher-troubleshoot-manage-rest.md)：帮助排查虚拟网络网关和连接问题。

- [网络订阅限制](../../network-watcher/network-watcher-monitoring-overview.md)：用于查看网络资源用量与限制。

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 是多个平台上面向 Web 开发人员的可扩展 APM 服务。 使用它可以监视实时 Web 应用程序。 它会自动检测性能异常。 其中包含强大的分析工具来帮助诊断问题，了解用户在应用中实际执行了哪些操作。

Application Insights 旨在帮助持续提高性能与可用性。

它适用于各种平台（包括 .NET、Node.js 和 Java EE）中的应用，不管这些应用是托管在本地还是云中。 它与 DevOps 流程集成，并具有与各种开发工具的连接点。

![Application Insights 示意图](./media/log-audit/azure-log-audit-fig6.png)

Application Insights 主要面向开发团队，旨在帮助用户了解应用的运行性能和使用方式。 监视：

* **请求率、响应时间和失败率**：了解最受欢迎的页面、时段以及用户的位置。 查看哪些页面效果最好。 当有较多请求时，如果响应时间长且失败率高，则可能存在资源问题。

* **依赖项速率、响应时间和失败率**：了解外部服务是否正拖慢速度。

* **异常**：分析聚合的统计信息，或选择特定实例并钻取堆栈跟踪和相关请求。 报告服务器和浏览器异常。

* **页面查看次数和负载性能**：获取用户浏览器生成的报告。

* **AJAX 调用**：获取网页速率、响应时间和失败率。

* **用户和会话计数**。

* **性能计数器**：获取 Windows 或 Linux 服务器计算机中的数据，例如 CPU、内存和网络使用情况。

* **主机诊断**：获取 Docker 或 Azure 中的数据。

* **诊断跟踪日志**：获取应用中的数据，以便能够将跟踪事件与请求相关联。

* **自定义事件和指标**：获取在客户端或服务器代码中自行写入的数据，以跟踪业务事件（例如销售的商品或赢得的游戏）。

下表列出并描述了集成方案：

| 集成方案 | 描述 |
| --------------------- | :---------- |
|[应用程序映射](../../azure-monitor/app/app-map.md)|应用的组件，包含关键指标和警报。|
|[实例数据的诊断搜索](../../azure-monitor/app/diagnostic-search.md)| 搜索和筛选事件，例如请求、异常、依赖项调用、日志跟踪和页面视图。|
|[聚合数据的指标资源管理器](../../azure-monitor/app/metrics-explorer.md)|浏览、筛选和细分聚合的数据，例如请求率、故障率和异常率；响应时间、页面加载时间。|
|[仪表板](../../azure-monitor/app/overview-dashboard.md)|混合使用来自多个资源的数据并与他人共享。 对于多组件应用程序和在团队聊天室中连续显示很有用。|
|[实时指标流](../../azure-monitor/app/live-stream.md)|部署新的生成时，观看这些准实时性能指示器，确保一切按预期工作。|
|[分析](../../azure-monitor/app/analytics.md)|使用此功能强大的查询语言，回答有关应用的性能和使用情况的疑难问题。|
|[自动和手动警报](../../azure-monitor/app/alerts.md)|当某些内容处于异常模式时，自动警报适应应用的遥测和触发器正常模式。 还可以在自定义或标准指标的特定级别上设置警报。|
|[Visual Studio](../../azure-monitor/app/visual-studio.md)|查看代码中的性能数据。 从堆栈跟踪转到代码。|
|[Power BI](../../azure-monitor/app/export-power-bi.md)|将使用指标与其他商业智能集成。|
|[REST API](https://dev.applicationinsights.io/)|编写代码以对指标和原始数据运行查询。|
|[连续导出](../../azure-monitor/app/export-telemetry.md)|原始数据到达后，将其批量导出到存储。|

### <a name="azure-security-center-alerts"></a>Azure 安全中心警报

Azure 安全中心可以自动从 Azure 资源、网络以及连接的合作伙伴解决方案收集安全信息，对威胁进行检测。 分析该信息（通常需将多个来源的信息关联起来）即可确定威胁。 安全中心会对安全警报进行重要性分类，并提供威胁处置建议。 有关详细信息，请参阅 [Azure 安全中心](../../security-center/security-center-intro.md)。

![Azure 安全中心示意图](./media/log-audit/azure-log-audit-fig7.png)

安全中心使用各种高级安全分析，远不止几种基于攻击特征的方法。 安全中心运用大数据的突破性技术和[机器学习](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/)技术对整个云结构中的事件进行评估， 以便检测那些使用手动方式不可能发现的威胁，并预测攻击的演变方式。 此类安全分析包括：

* **集成威胁智能**：应用 Microsoft 产品和服务、Microsoft 数字犯罪部门 (DCU)、Microsoft 安全响应中心 (MSRC) 以及外部源提供的全球威胁智能，搜寻已知的行为不端的攻击者。

* **行为分析**：运用已知模式发现恶意行为。

* **异常检测**：使用统计分析生成历史基线。 如果出现与已知基线偏离的情况，并且这些情况符合潜在攻击载体的行为，则会发出警报。

许多安全操作和事件响应团队依靠 SIEM 解决方案作为会审和调查安全警报的起始点。 使用 Azure 日志集成, 你可以将 Azure 诊断和审核日志收集的安全中心警报和虚拟机安全事件与 Azure Monitor 的日志或 SIEM 解决方案进行近乎实时的同步。

## <a name="azure-monitor-logs"></a>Azure Monitor 日志

Azure Monitor 日志是 Azure 中的一项服务, 可帮助收集和分析云和本地环境中资源生成的数据。 使用集成的搜索和自定义仪表板，轻松分析所有工作负荷和服务器上的数百万记录，而无需考虑它们的物理位置，从而获得实时见解。

![Azure Monitor 日志关系图](./media/log-audit/azure-log-audit-fig8.png)

Azure Monitor 日志的中心是托管在 Azure 中的 Log Analytics 工作区。 Azure Monitor 日志通过配置数据源和向订阅添加解决方案, 从连接的源收集工作区中的数据。 数据源和解决方案分别创建具有自身属性集的不同记录类型。 但是，仍可在对工作区的查询中同时对这些记录进行分析。 借助此功能，可以使用相同的工具和方法来处理不同的源收集的各种数据。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

连接的源是生成 Azure Monitor 日志收集的数据的计算机和其他资源。 源可能包括直接连接的 [Windows](../../log-analytics/log-analytics-agent-windows.md) 和 [Linux](../../log-analytics/log-analytics-quick-collect-linux-computer.md) 计算机上安装的代理或[连接的 System Center Operations Manager 管理组](../../azure-monitor/platform/om-agents.md)中的代理。 Azure Monitor 日志还可以从[Azure 存储帐户](../../azure-monitor/platform/resource-logs-collect-storage.md)收集数据。

[数据源](../../azure-monitor/platform/agent-data-sources.md)是从每个连接的源收集的各种数据。 源包括来自 [Windows](../../azure-monitor/platform/data-sources-windows-events.md) 和 Linux 代理的事件和[性能数据](../../azure-monitor/platform/data-sources-performance-counters.md)，此外，还包括 [IIS 日志](../../azure-monitor/platform/data-sources-iis-logs.md)和[自定义文本日志](../../azure-monitor/platform/data-sources-custom-logs.md)等源。 可以配置要收集的各个数据源，配置会自动传递到各个连接的源。

可通过四种方式[收集 Azure 服务的日志和指标](../../azure-monitor/platform/resource-logs-collect-storage.md)：

* Azure 诊断定向到 Azure Monitor 日志 (下表中的**诊断**)

* 将 Azure 存储 Azure 诊断到 Azure Monitor 日志 (下表中的**存储**)

* Azure 服务的连接器（下表中的“连接器”）

* 用于收集数据, 然后将数据发布到 Azure Monitor 日志中的脚本 (下表中的空白单元格和未列出的服务)

| 服务 | 资源类型 | 日志 | 指标 | 解决方案 |
| :------ | :------------ | :--- | :------ | :------- |
|Azure 应用程序网关| Microsoft.Network/<br>applicationGateways|  诊断|诊断|    [Azure 应用程序](../../azure-monitor/insights/azure-networking-analytics.md)[网关分析](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor)|
|Application Insights||     连接器|  连接器|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [连接器（预览版）](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Azure 自动化帐户| Microsoft.Automation/<br>AutomationAccounts|    诊断||       [详细信息](../../automation/automation-manage-send-joblogs-log-analytics.md)|
|Azure Batch 帐户|  Microsoft.Batch/<br>batchAccounts|  诊断|    诊断||
|经典云服务||       存储||       [详细信息](../../azure-monitor/platform/azure-storage-iis-table.md)|
|认知服务|    Microsoft.CognitiveServices/<br>帐户|       诊断|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>accounts|   诊断|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>帐户|   诊断|||
|Azure 事件中心命名空间| Microsoft.EventHub/<br>namespaces|  诊断|    诊断||
|Azure IoT 中心| Microsoft.Devices/<br>IotHubs||     诊断||
|Azure Key Vault|   Microsoft.KeyVault/<br>vaults|  诊断  || [密钥保管库分析](../../azure-monitor/insights/azure-key-vault.md)|
|Azure 负载均衡器|   Microsoft.Network/<br>loadBalancers|    诊断|||
|Azure 逻辑应用|  Microsoft.Logic/<br>workflows|  诊断|    诊断||
||Microsoft.Logic/<br>integrationAccounts||||
|网络安全组|   Microsoft.Network/<br>networksecuritygroups|诊断||   [Azure 网络安全组分析](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-and-network-security-group-analytics)|
|恢复保管库|   Microsoft.RecoveryServices/<br>vaults|||[Azure 恢复服务分析（预览版）](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|搜索服务|   Microsoft.Search/<br>searchServices|    诊断|    诊断||
|服务总线命名空间| Microsoft.ServiceBus/<br>命名空间|    诊断|诊断|    [Service Fabric 分析（预览版）](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       存储||    [Service Fabric 分析（预览版）](../../service-fabric/service-fabric-diagnostics-oms-setup.md)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>databses||       诊断||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|存储|||         脚本| [Azure 存储分析（预览版）](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure 虚拟机|    Microsoft.Compute/<br>virtualMachines|  扩展|  扩展||
||||诊断||
|虚拟机规模集|    Microsoft.Compute/<br>virtualMachines    ||诊断||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Web 服务器场|Microsoft.Web/<br>serverfarms||   诊断
|网站|  Microsoft.Web/<br>sites ||      诊断|    [详细信息](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots||||


## <a name="log-integration-with-on-premises-siem-systems"></a>与本地 SIEM 系统进行日志集成

可以使用 Azure 日志集成将来自 Azure 资源的原始日志与本地 SIEM 系统（安全信息和事件管理系统）相集成。 AzLog 下载已在 2018 年 6 月 27 日被禁用。 有关下一步该怎么做的指导，请查看文章[使用 Azure Monitor 与 SIEM 工具集成](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![日志集成示意图](./media/log-audit/azure-log-audit-fig9.png)

日志集成从 Windows 虚拟机、Azure 活动日志、Azure 安全中心警报和 Azure 资源提供程序日志收集 Azure 诊断。 此集成为所有资产（不管是位于本地还是云中）提供统一的仪表板，以便可以针对安全事件进行聚合、关联、分析和发出警报。

日志集成目前支持集成 Azure 活动日志、Azure 订阅中 Windows 虚拟机上的 Windows 事件日志、Azure 安全中心警报、Azure 诊断日志和 Azure AD 审核日志。

| 日志类型 | 支持 JSON 的 Azure Monitor 日志 (Splunk、ArcSight 和 IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Azure AD 审核日志|   是|
|活动日志| 是|
|安全中心警报 |是|
|诊断日志（资源日志）|  是|
|VM 日志|   是，通过转发的事件，而不是通过 JSON|

[Azure 日志集成入门](azure-log-integration-get-started.md)：本教程逐步讲解如何安装 Azure 日志集成，以及如何集成来自 Azure 存储的日志、Azure 活动日志、Azure 安全中心警报和 Azure AD 审核日志。

SIEM 的集成方案：

* [合作伙伴配置步骤](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)：此博客文章介绍如何配置 Azure 日志集成，以使用 Splunk、HP ArcSight 和 IBM QRadar 合作伙伴解决方案。

* [Azure 日志集成常见问题解答](azure-log-integration-faq.md)：本文回答了关于 Azure 日志的问题。

* [使用 Azure 日志集成来集成 Azure 安全中心警报](../../security-center/security-center-export-data-to-siem.md)：本文介绍如何将安全中心警报、Azure 诊断日志收集的虚拟机安全事件与 Azure Monitor 日志或 SIEM 解决方案的 Azure 审核日志同步。

## <a name="next-steps"></a>后续步骤

- [审核和日志记录](management-monitoring-overview.md)：通过维护可视性和快速响应及时安全警报来保护数据。

- [Azure 中的安全日志记录和审核日志收集](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)：实施这些设置可确保 Azure 实例收集正确的安全和审核日志。

- [配置网站集的审核设置](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)：如果你是网站集管理员，请检索单个用户的操作历史记录，以及在特定日期范围内执行的操作的历史记录。 

- [在 Office 365 安全与合规中心中搜索审核日志](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)：使用 Office 365 安全与合规中心搜索统一的审核日志，并查看 Office 365 组织中的用户和管理员活动。


