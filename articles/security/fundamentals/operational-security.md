---
title: Azure 操作安全性 |Microsoft Docs
description: 了解 Microsoft Azure 监视日志、服务以及其工作原理。
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
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 69b25f22d448436db5e906a3b56b5e50ec412b40
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934680"
---
# <a name="azure-operational-security"></a>Azure 操作安全性
## <a name="introduction"></a>简介

### <a name="overview"></a>概述
我们知道，安全是云中的首要任务，及时找到有关 Azure 安全性的准确信息极其重要。 针对应用程序和服务使用 Azure 的最合理原因之一是可以利用各种可用的安全工具和功能。 这些工具和功能可帮助在安全的 Azure 平台上创建安全的解决方案。 Microsoft Azure 必须提供保密、完整且可用的客户数据，并实现透明问责。

为帮助客户更好地从客户和 Microsoft 操作这两个角度了解 Microsoft Azure 中实施的一系列安全控件，特编写本白皮书《Azure 操作安全性》，全面介绍通过 Microsoft Azure 实现的操作安全性。

### <a name="azure-platform"></a>Azure 平台
Azure 是一个公有云服务平台，支持多种操作系统、编程语言、框架、工具、数据库和设备。 它可运行与 Docker 集成的 Linux 容器；使用 JavaScript、Python、.NET、PHP、Java 和 Node.js 生成应用；生成适用于 iOS、Android 和 Windows 设备的后端。 Azure 云服务支持数百万开发人员和 IT 专业人士依赖并信任的技术。

构建 IT 资产或将其迁移到公有云服务提供商处时，需要借助该组织的能力来保护应用程序和数据，并使用该组织提供的服务和控制机制来管理基于云的资产的安全性。

Azure 的基础结构（从设备到应用程序）经过设计，可同时托管数百万的客户，并为企业提供可靠的基础，使之能够满足其安全要求。 此外，Azure 还提供广泛的可配置安全选项以及对这些选项进行控制的功能，方便用户自定义安全措施来满足组织部署的独特要求。 本文档可帮助用户了解 Azure 安全功能如何帮助满足这些要求。

### <a name="abstract"></a>摘要
Azure 操作安全性是指用户可用于在 Microsoft Azure 中保护其数据、应用程序和其他资产的服务、控件和功能。 Azure 操作安全性建立在一个框架上，该框架融合了通过 Microsoft 独有的各种功能获得的知识，包括 Microsoft 安全开发生命周期 (SDL)、Microsoft 安全响应中心计划以及对网络安全威胁形态的深刻认识。

本白皮书概述了 Microsoft 在 Microsoft Azure 云平台中的 Azure 操作安全性方法，并介绍了以下服务：
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure 安全中心](../../security-center/security-center-intro.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Azure 网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Azure 存储分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure 监视日志

Microsoft Azure 监视器日志是适用于混合云的 IT 管理解决方案。 Azure Monitor 日志单独使用或扩展现有的 System Center 部署, 为你的基础结构的基于云的管理提供最大的灵活性和控制。

![Azure Monitor 日志](./media/operational-security/azure-operational-security-fig1.png)

使用 Azure Monitor 日志, 可以管理任何云中的任何实例, 包括本地、Azure、AWS、Windows Server、Linux、VMware 和 OpenStack, 成本低于竞争性解决方案。 Azure Monitor 日志是针对云优先环境构建的, 它提供了一种新的方式来管理企业, 这是最快、最经济高效的方式, 可满足新的业务挑战并适应新的工作负载、应用程序和云环境。

### <a name="azure-monitor-services"></a>Azure Monitor 服务

Azure Monitor 日志的核心功能由在 Azure 中运行的一组服务提供。 每个服务提供特定的管理功能，可以组合这些服务来实现不同的管理方案。

| 服务  | 描述|
| :------------- | :-------------|
| Azure Monitor 日志 | 监视和分析不同资源（包括物理机和虚拟机）的可用性与性能。 |
|自动化 | 将手动过程自动化，实施物理机和虚拟机的配置。 |
| 备份 | 备份和还原关键数据。 |
| Site Recovery | 为关键应用程序提供高可用性。 |

### <a name="azure-monitor-logs"></a>Azure Monitor 日志

[Azure Monitor 日志](https://azure.microsoft.com/documentation/services/log-analytics)通过将托管资源的数据收集到中心存储库来提供监视服务。 这些数据可能包括事件、性能数据或通过 API 提供的自定义数据。 收集后，可以分析、导出数据或针对它们发出警报。


使用这种方法可以整合来自各种源的数据，因此可将 Azure 服务中的数据合并到现有的本地环境。 此外，它还能将数据收集与针对该数据执行的操作明确区分开来，以便能够针对所有类型的数据执行所有操作。


![Azure Monitor 日志](./media/operational-security/azure-operational-security-fig2.png)

Azure Monitor 服务使用以下方法安全地管理你的基于云的数据:
-   数据隔离
-   数据保留
-   物理安全性
-   事件管理
-   合规性
-   安全标准认证

### <a name="azure-backup"></a>Azure 备份

[Azure 备份](https://azure.microsoft.com/documentation/services/backup)提供数据备份和还原服务, 是 Azure Monitor 的产品和服务套件的一部分。
它可以保护应用程序数据，并且无需资本投资、只需最低的运行成本，即可将这些数据保留多年。 除 SQL Server 和 SharePoint 等应用程序工作负荷之外，它还可以备份物理和虚拟 Windows 服务器中的数据。 [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) 也可使用它将受保护的数据复制到 Azure，以实现冗余和长期存储。


Azure 备份中的受保护数据存储在位于特定地理区域的备份保管库。 数据在同一区域内复制，并且根据保管库类型，可能还会复制到其他区域以进一步实现复原。

### <a name="management-solutions"></a>管理解决方案
[Azure Monitor](../../security-center/security-center-intro.md)是 Microsoft 的基于云的 IT 管理解决方案, 可帮助你管理和保护本地和云基础结构。


[管理解决方案](../../monitoring/monitoring-solutions.md)是一组预打包的逻辑, 可使用一个或多个 Azure Monitor 服务实现特定的管理方案。 可从 Microsoft 和合作伙伴那里获取不同的解决方案, 你可以轻松地将其添加到你的 Azure 订阅, 以提高你在 Azure Monitor 中的投资价值。 合作伙伴可以创建自己的解决方案来支持自己的应用程序和服务，并通过 Azure 市场或快速启动模板将它们提供给用户。


![管理解决方案](./media/operational-security/azure-operational-security-fig4.png)

[更新管理解决方案](../../automation/automation-update-management.md)就是使用多个服务提供更多功能的解决方案的一个好例子。 此解决方案使用适用于 Windows 和 Linux 的[Azure Monitor 日志](../../log-analytics/log-analytics-queries.md)代理来收集有关每个代理上所需的更新的信息。 它将此数据写入 Azure Monitor 日志存储库, 可在其中使用包含的仪表板对其进行分析。

创建部署时，可以使用 [Azure 自动化](../../automation/automation-intro.md)中的 runbook 来安装所需的更新。 可在管理门户中管理整个过程，无需担心如何获取基础详细信息。

## <a name="azure-security-center"></a>Azure 安全中心

Azure 安全中心可帮助保护 Azure 资源。 它为 Azure 订阅提供集成的安全监控和策略管理。 在该服务中，不仅可以针对 Azure 订阅定义策略，还可针对[资源组](../../azure-resource-manager/resource-group-overview.md#resource-groups)定义策略，这样可以提高精细度。

### <a name="security-policies-and-recommendations"></a>安全策略和建议

安全策略用于定义一组控制，这些控制是针对指定订阅或资源组中的资源建议的。

在安全中心定义策略时，需考虑到公司的安全要求和应用程序类型或数据的敏感性。

![安全策略和建议](./media/operational-security/azure-operational-security-fig5.png)


在订阅级别启用的策略会自动传播到该订阅中的所有资源组，如右图所示：


### <a name="data-collection"></a>数据收集

安全中心会收集来自虚拟机 (VM) 的数据，进而评估其安全状态、提供安全建议并发出威胁警报。 首次访问安全中心时，即会在订阅的所有 VM 上启用数据收集。 建议收集数据，但可关闭安全中心策略中的数据收集以选择退出。

### <a name="data-sources"></a>数据源

- Azure 安全中心将分析以下源中的数据，提供安全状态视图、识别漏洞、建议缓解措施，并检测现行的威胁：

-   Azure 服务：通过与 Azure 服务的资源提供程序通信，使用已部署的 Azure 服务的配置信息。

- 网络流量：使用从 Microsoft 基础结构中采样的网络流量元数据，例如源/目标 IP/端口、数据包大小以及网络协议。

-   合作伙伴解决方案：使用来自集成合作伙伴解决方案（例如防火墙和反恶意软件解决方案）的安全警报。

-   虚拟机：使用虚拟机中的配置信息，以及有关安全事件的信息，例如 Windows 事件和审核日志、IIS 日志、系统日志消息和故障转储文件。

### <a name="data-protection"></a>数据保护

为了帮助客户防止、检测和应对威胁，Azure 安全中心将收集和处理安全相关的数据，包括配置信息、元数据、事件日志、故障转储文件，等等。 从编程到服务运营，Microsoft 都严格遵守相关法规与安全准则。

-   **数据隔离**：服务中每个组件的数据都保持逻辑隔离。 所有数据均按组织进行标记。 此标记方式贯穿数据的整个生命周期，在服务的每个层强制实施。

-   **数据访问**：为了提供安全建议及调查潜在安全威胁，Microsoft 人员可能会访问 Azure 服务收集或分析的信息，包括故障转储文件、进程创建事件、VM 磁盘快照和项目，其中可能会意外地包括虚拟机中的客户数据或个人数据。 我们遵守 [Microsoft Online Services 条款和隐私声明](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)，其中指出，Microsoft 不会出于广告或类似的商业目的利用客户数据，或者从客户数据衍生信息。

-   **数据使用**：Microsoft 使用多个租户所使用的模式和威胁情报增强预防和检测威胁的能力；执行过程中遵循[隐私声明](https://www.microsoft.com/en-us/privacystatement/OnlineServices/)中所述的隐私承诺。

### <a name="data-location"></a>数据位置

Azure 安全中心收集故障转储文件的临时副本并对其进行分析，目的是找出攻击者尝试利用漏洞并成功进行了破坏的证据。 Azure 安全中心在工作区所在的同一地理位置执行此分析，分析完成后，将删除临时副本。 计算机项目集中存储在 VM 所在的同一区域。

-   **存储帐户**：为运行虚拟机的每个区域指定一个存储帐户。 这样即可将数据存储在一个区域，而从虚拟机收集数据时，虚拟机也位于该区域。

-   **Azure 安全中心存储**：有关安全警报（包括合作伙伴警报）、建议和安全运行状况的信息将集中存储，存储位置目前位于美国。 该信息可能包括根据需要从用户虚拟机收集的相关配置信息和安全事件，目的是为用户提供安全警报、建议或安全运行状况。


## <a name="azure-monitor"></a>Azure Monitor

利用[Azure Monitor 日志安全](../../security-center/security-center-monitoring.md)和审核解决方案, 它可以主动监视所有资源, 这有助于最大程度地降低安全事件的影响。 Azure Monitor 日志安全和审核具有可用于监视资源的安全域。 安全域支持快速访问用于安全监视的选项，详细介绍了以下域：

-   恶意软件评估
-   更新评估
-   标识和访问。

Azure Monitor 提供有关特定类型资源的信息的导航。 它对来自 Azure 基础结构（活动日志）和每个单独 Azure 资源（诊断日志）的数据提供可视化、查询、路由、警报、自动缩放和自动化功能。

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


云应用程序很复杂，包含很多移动部件。 监视可以为用户提供数据，确保应用程序始终处于健康运行状态。 监视还有助于避免潜在问题，或者解决过去的问题。

此外，还可以利用监视数据深入了解应用程序的情况。 了解这些情况有助于改进应用程序的性能或可维护性，或者实现本来需要手动干预的操作的自动化。

### <a name="azure-activity-log"></a>Azure 活动日志


该日志方便用户了解对订阅中的资源执行的操作。 活动日志此前称为“审核日志”或“操作日志”，因为它报告订阅的控制平面事件。

![Azure 活动日志](./media/operational-security/azure-operational-security-fig7.png)

通过活动日志，可确定订阅中资源上进行的任何写入操作 (PUT, POST, DELETE) 的“什么操作、谁操作和操作时间”等信息。 还可以了解该操作和其他相关属性的状态。 活动日志不包括读取 (GET) 操作或针对使用经典模型的资源的操作。

### <a name="azure-diagnostic-logs"></a>Azure 诊断日志

这些日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 这些日志的内容因资源类型而异。

例如，Windows 事件系统日志是适用于 VM 的一个诊断日志类别，而 Blob、表和队列日志是适用于存储帐户的诊断日志类别。

诊断日志不同于[活动日志（以前称为“审核日志”或“操作日志”）](../../azure-monitor/platform/activity-logs-overview.md)。 活动日志提供针对订阅中的资源执行的操作的深入信息。 诊断日志提供资源本身执行的操作的深入信息。

### <a name="metrics"></a>指标

在 Azure 监视器中可以使用遥测来查看 Azure 上的工作负荷的性能与运行状况。 最重要的 Azure 遥测数据类型是大多数 Azure 资源发出的指标（也称为性能计数器）。 Azure Monitor 提供多种方式来配置和使用这些[指标](../../monitoring/monitoring-data-collection.md)，以便进行监视与故障排除。 指标是重要的遥测来源，允许执行以下任务：

-   通过在门户图表上绘制资源（例如 VM、网站或逻辑应用）的指标并将该图表固定到仪表板，来**跟踪资源的性能**。

-   当某个指标超过特定的阈值，从而影响资源的性能时，可以**接收通知**。

-   配置自动化操作，例如自动缩放资源，或者在指标超过特定的阈值时触发 runbook。

-   针对资源的性能或使用趋势**执行高级分析**或报告。

-   出于符合性或审核目的，对资源的性能或运行状况历史记录进行存档。

### <a name="azure-diagnostics"></a>Azure 诊断

这是 Azure 中可对部署的应用程序启用诊断数据收集的功能。 可以使用各种不同源的诊断扩展。 目前支持的有 [Azure 云服务 Web 和辅助角色](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service)、运行 Microsoft Windows 的 [Azure 虚拟机](../../virtual-machines/windows/overview.md)，以及 [Service Fabric](../../azure-monitor/platform/diagnostics-extension-overview.md)。 其他 Azure 服务都有自身不同的诊断扩展。

## <a name="azure-network-watcher"></a>Azure 网络观察程序

审核网络安全性对于检测网络漏洞以及确保符合 IT 安全和监管治理模型至关重要。 使用安全组视图，用户可以检索配置的网络安全组和安全规则，以及有效的安全规则。 应用规则列表后，可以确定打开的端口并评估网络漏洞。

[网络观察程序](../../network-watcher/network-watcher-monitoring-overview.md)是一个区域性服务，可用于在网络级别监视和诊断 Azure 内部以及传入和传出 Azure 的流量的状态。 借助网络观察程序随附的网络诊断和可视化工具，可以了解、诊断和洞察 Azure 中的网络。 此服务包括数据包捕获、下一跃点、IP 流验证、安全组视图和 NSG 流日志。 与单独网络资源的监视不同，方案级监视提供网络资源的端到端视图。

![Azure 网络观察程序](./media/operational-security/azure-operational-security-fig8.png)

网络观察程序目前提供以下功能：

-   <a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">审核日志</a> - 记录网络配置过程中执行的操作。 可在 Azure 门户中查看，或者使用 Power BI 等 Microsoft 工具或第三方工具检索这些日志。 可通过门户、PowerShell、CLI 和 REST API 获取审核日志。 有关审核日志的详细信息，请参阅“使用资源管理器审核操作”。 针对所有网络资源执行的操作都有相应的审核日志。


-   <a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP 流验证</a> - 根据流信息 5 元组数据包参数（目标 IP、源 IP、目标端口、源端口和协议）检查数据包是被允许还是被拒绝。 如果网络安全组拒绝了数据包，则返回拒绝该数据包的规则和网络安全组。

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">下一跃点</a>** - 确定 Azure 网络结构中路由的数据包的下一跃点，以便诊断任何错误配置的用户定义路由。

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">安全组视图</a>** - 获取在 VM 上应用的有效安全规则。

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG 流日志记录</a>** - 使用网络安全组的流日志可以捕获被组中的安全规则允许或拒绝的流量的相关日志。 流由 5 元组信息（源 IP、目标 IP、源端口、目标端口和协议）定义。

## <a name="azure-storage-analytics"></a>Azure 存储分析

[存储分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)可存储一些指标，这些指标包括有关存储服务请求的聚合事务统计信息和容量数据。 在 API 操作级别以及存储服务级别报告事务，并在存储服务级别报告容量。 度量值数据可用于分析存储服务使用情况，诊断对存储服务所发出请求的问题以及提高使用服务的应用程序的性能。

[Azure 存储分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)执行日志记录并为存储帐户提供指标数据。 可以使用此数据跟踪请求、分析使用情况趋势以及诊断存储帐户的问题。 存储分析日志记录可用于 [Blob、队列和表服务](../../storage/common/storage-introduction.md)。 存储分析记录成功和失败的存储服务请求的详细信息。

可以使用该信息监视各个请求和诊断存储服务问题。 将最大程度地记录请求。 仅在针对服务终结点发出请求时才会创建日志条目。 例如，如果存储帐户的 Blob 终结点中存在活动，而表或队列终结点中没有该活动，则仅创建与 Blob 服务有关的日志。

若要使用存储分析，必须为每个要监视的服务单独启用它。 可以在 [Azure 门户](https://portal.azure.com/)中启用它；有关详细信息，请参阅[在 Azure 门户中监视存储帐户](../../storage/common/storage-monitor-storage-account.md)。 还可以通过 REST API 或客户端库以编程方式启用存储分析。 使用“设置服务属性”操作分别为每项服务启用存储分析。

聚合数据存储在众所周知的 Blob（用于日志记录）和众所周知的表（用于度量）中，可以使用 BLOB 服务和表服务 API 对其进行访问。

存储分析对存储数据的数量限制为 20 TB，这与存储帐户的总限制无关。 所有日志都以[块 Blob](../../storage/common/storage-analytics.md) 的形式存储在一个名为 $logs 的容器中，该容器是在为存储帐户启用存储分析时自动创建的。

存储分析执行的以下操作都是计费的：

-   为日志记录创建 Blob 的请求
-   为度量创建表实体的请求。

> [!Note]
> 有关计费和数据保留策略的详细信息，请参阅[Storage Analytics and Billing](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)（存储分析和计费）。
> 为了获得最佳性能，需要限制附加到虚拟机的、重度使用的磁盘数，以避免可能的性能限制。 只要不是在同一时间重度使用所有磁盘，存储帐户就可以支持更多的磁盘。

> [!Note]
> 有关存储帐户限制的详细信息，请参阅 [Azure 存储可伸缩性和性能目标](../../storage/common/storage-scalability-targets.md)。


将记录以下类型的已获得验证的匿名请求。

| 已获得验证  | 匿名|
| :------------- | :-------------|
| 成功的请求数 | 成功的请求 |
|失败的请求，包括超时、限制、网络、授权和其他错误 | 使用共享访问签名 (SAS) 的请求，包括失败和成功的请求 |
| 使用共享访问签名 (SAS) 的请求，包括失败和成功的请求 |客户端和服务器的超时错误 |
|   分析数据请求 |    失败的 GET 请求，错误代码为 304（未修改） |
| 不会记录存储分析本身发出的请求，如创建或删除日志。 [存储分析记录的操作和状态消息](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)及[存储分析日志格式](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)主题中提供了所记录数据的完整列表。 | 不会记录所有其他失败的匿名请求。 若要查看所记录数据的完整列表，请参阅 [Storage Analytics Logged Operations and Status Messages](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages)（存储分析记录的操作和状态消息）和 [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)（存储分析日志格式）。 |

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD 还包含整套标识管理功能，例如多重身份验证、设备注册、自助密码管理、自助组管理、特权帐户管理、基于角色的访问控制、应用程序使用情况监视、多样化审核以及安全监视和警报。

-   使用 Azure AD 多重身份验证和条件访问提高应用程序安全性。

-   利用安全报告和监视来监视应用程序使用情况并防止企业受到严重威胁。

Azure Active Directory (Azure AD) 包括针对目录的安全报表、活动报表和审核报表。 [Azure Active Directory 审核报告](../../active-directory/active-directory-reporting-azure-portal.md)可帮助客户识别其 Azure Active Directory 中发生的特权操作。 特权操作包括提升更改（例如，创建角色或密码重置）、更改策略配置（例如密码策略）或更改目录配置（例如，对域联合身份验证设置的更改）。

报告提供了事件名称的审核记录、操作执行者、更改影响的目标资源，以及日期和时间 (UTC)。 客户可以根据[查看审核日志](../../active-directory/reports-monitoring/overview-reports.md)中所述，通过 [Azure 门户](https://portal.azure.com/)检索其 Azure Active Directory 的审核事件列表。 下面是包含的报表列表：

| 安全报表  | 活动报表| 审核报表 |
| :------------- | :-------------| :-------------|
|从未知源登录 | 应用程序使用情况：摘要 | 目录审核报表 |
|多次失败后登录 | 应用程序使用情况：详细信息 |   |
|从多个地理区域登录 | 应用程序仪表板 |  |
|从具有可疑活动的 IP 地址登录 |帐户预配错误 |  |
|异常登录活动 |单个用户设备 |  |
|从可能受感染的设备登录 |单个用户活动 |   |
|具有异常登录活动的用户 |组活动报表 |   |
| |密码重置注册活动报表 |   |
| |密码重置活动 |   |



对于应用程序（如 SIEM 系统、审核）和商业智能工具而言，这些报表的数据可能非常有用。 Azure AD 报告 [API](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) 通过一组基于 REST 的 API，提供对该数据的编程访问权限。 可从各种编程语言和工具中调用这些 API。

Azure AD 审核报告中的事件将保留 180 天。

> [!Note]
> 有关保留报告的详细信息，请参阅 [Azure Active Directory 报告保留策略](../../active-directory/reports-monitoring/reference-reports-data-retention.md)。

对于想要更长时间存储其[审核事件](../../active-directory/active-directory-reporting-activity-audit-logs.md)的客户，可以使用报告 API 定期将审核事件提取到独立的数据存储中。

## <a name="summary"></a>总结

本文总结了如何保护隐私和数据安全，同时提供相关软件和服务来帮助用户管理组织的 IT 基础结构。 Microsoft 认识到，当用户授信他人访问自己的数据时，这种信任需要极高的安全性。 从编程到服务运营，Microsoft 都严格遵守相关法规与安全准则。 保护数据是 Microsoft 的头等大事。

本文介绍了

-   如何在 Azure Monitor 套件中收集、处理和保护数据。

-   跨多个数据源快速分析事件。 识别安全风险并了解威胁和攻击的范围和影响，降低违反安全的行为所带来的损害。

-   通过直观呈现出站恶意 IP 流量和恶意威胁类型，识别攻击模式。 了解整个环境（所有平台）的安全状况。

-   捕获安全性或符合性审核所需的所有日志和事件数据。 使用完整、可搜索和可导出的日志和事件数据集减少进行安全审核所需的时间和资源。

<ul>
<li>收集与安全相关的事件、审核和违规分析，密切关注资产：</li>
<ul>
<li>安全状况</li>
<li>值得注意的问题</li>
<li>汇总威胁</li>
</ul>
</ul>

## <a name="next-steps"></a>后续步骤

- [设计和运营安全性](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft 在设计其服务和软件时始终关注安全性，确保其云基础结构可复原，并能防御攻击。

- [Azure Monitor 日志 |安全性和符合性](https://www.microsoft.com/cloud-platform/security-and-compliance)

使用 Microsoft 安全数据和分析来执行更智能和有效的威胁检测。

- [Azure 安全中心规划和操作](../../security-center/security-center-planning-and-operations-guide.md) 提供一系列步骤和任务，用户可以按照这些步骤和任务，根据组织的安全要求和云管理模型优化安全中心的使用。

