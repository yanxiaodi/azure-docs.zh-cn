---
title: 可以通过 Azure Site Recovery 保护哪些工作负荷？ | Microsoft Docs
description: 介绍可以通过将灾难恢复与 Azure Site Recovery 服务配合使用来保护的工作负荷。
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: raynew
ms.openlocfilehash: f3ff6e5e05cab9aab5257d810c6785e7691bae45
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232185"
---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>可以通过 Azure Site Recovery 保护哪些工作负荷？

本文介绍了可以使用 [Azure Site Recovery](site-recovery-overview.md) 服务为灾难恢复保护的工作负荷和应用程序。



## <a name="overview"></a>概述

组织需要制定业务连续性和灾难恢复 (BCDR) 策略来确保工作负荷和数据在计划内和计划外停机期间保持安全和可用，并尽快恢复正常运行。

站点恢复就是能够帮助实现 BCDR 策略的一个 Azure 服务。 使用站点恢复，可将应用程序感知的复制部署到云或辅助站点中。 无论应用是基于 Windows 还是 Linux，是在物理服务器、VMware 还是 Hyper-V 上运行，都可以使用站点恢复来协调复制，执行灾难恢复测试，以及运行故障转移和故障回复。

Site Recovery 集成 Microsoft 应用程序，其中包括 SharePoint、Exchange、Dynamics、SQL Server 和 Active Directory。 Microsoft 还与 Oracle、SAP 和 Red Hat 等领先供应商密切合作。 可以针对每个应用自定义复制解决方案。

## <a name="why-use-site-recovery-for-application-replication"></a>为何要使用站点恢复来复制应用程序？

Site Recovery 可帮助实现应用程序级的保护和恢复，如下所示：

* 不区分应用，为受支持计算机上运行的任何工作负荷提供复制。
* 几乎同步的复制，RPO 低至 30 秒，满足大多数关键业务应用的需要。
* 针对单层或多层应用程序的应用一致性快照。
* 集成 SQL Server AlwaysOn，纳入了其他应用程序级复制技术，其中包括 AD 复制、SQL AlwaysOn、Exchange 数据库可用性组 (DAG)。
* 灵活的恢复计划，一次单击即可恢复整个应用程序堆栈，包括在计划中使用外部脚本和手动操作。
* 站点恢复和 Azure 中的高级网络管理可以简化应用的网络要求，包括保留 IP 地址、配置负载均衡器或集成 Azure 流量管理器以降低 RTO 网络切换数。
* 丰富的自动化库，提供特定于应用程序的生产就绪型脚本，可以下载并与恢复计划集成。

## <a name="workload-summary"></a>工作负荷摘要
站点恢复可复制受支持计算机上运行的任何应用。 此外, 我们还与产品团队合作, 为表中指定的应用程序执行其他测试。

| **工作负载** |**将 Azure VM 复制到 Azure** |**将 Hyper-V VM 复制到辅助站点** | **将 Hyper-V VM 复制到 Azure** | **将 VMware VM 复制到辅助站点** | **将 VMware VM 复制到 Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory、DNS |Y |Y |Y |Y |Y|
| Web 应用（IIS、SQL） |Y |Y |Y |Y |Y|
| System Center Operations Manager |Y |Y |Y |Y |Y|
| SharePoint |Y |Y |Y |Y |Y|
| SAP<br/><br/>将非群集 SAP 站点复制到 Azure |Y（Microsoft 已测试） |Y（Microsoft 已测试） |Y（Microsoft 已测试） |Y（Microsoft 已测试） |Y（Microsoft 已测试）|
| Exchange（非 DAG） |Y |Y |Y |Y |Y|
| 远程桌面/VDI |Y |Y |Y |Y |Y|
| Linux（操作系统和应用） |Y（Microsoft 已测试） |Y（Microsoft 已测试） |Y（Microsoft 已测试） |Y（Microsoft 已测试） |Y（Microsoft 已测试）|
| Dynamics AX |Y |Y |Y |Y |Y|
| Windows 文件服务器 |Y |Y |Y |Y |Y|
| Citrix XenApp 和 XenDesktop |Y|不可用 |Y |不可用 |Y |

## <a name="replicate-active-directory-and-dns"></a>复制 Active Directory 和 DNS
Active Directory 和 DNS 基础结构对于大多数企业应用而言至关重要。 在灾难恢复过程中恢复工作负荷和应用之前，需要保护和恢复这些基础结构组件。

可以使用站点恢复，为 Active Directory 和 DNS 创建一个完整的自动化灾难恢复计划。 例如，要将 SharePoint 和 SAP 从主站点故障转移到辅助站点，可以先设置可故障转移 Active Directory 的恢复计划，然后设置额外的应用特定计划，以故障转移依赖于 Active Directory 的其他应用。

[详细了解](site-recovery-active-directory.md) 如何保护 Active Directory 和 DNS。

## <a name="protect-sql-server"></a>保护 SQL Server
SQL Server 是本地数据中心许多业务应用的数据服务基础。  站点恢复可与 SQL Server HA/DR 技术一起用于保护采用 SQL Server 的多层企业应用。 Site Recovery 提供：

* 为 SQL Server 提供简单且经济高效的灾难恢复解决方案。 将多个版本的 SQL Server 独立服务器和群集复制到 Azure 或辅助站点。  
* 集成 SQL AlwaysOn 可用性组，使用 Azure Site Recovery 恢复计划管理故障转移和故障回复。
* 适用于应用程序中所有层（包括 SQL Server 数据库）的端到端恢复计划。
* 在出现高峰负载时使用站点恢复扩展 SQL Server，让这些负载“迸发”到 Azure 中更大型的 IaaS 虚拟机中。
* 简单的 SQL Server 灾难恢复测试。 可以运行测试故障转移来分析数据，并可以运行合规性检查，且不影响生产环境。

[详细了解](site-recovery-sql.md) 如何保护 SQL Server。

## <a name="protect-sharepoint"></a>保护 SharePoint
Azure Site Recovery 可帮助保护 SharePoint 部署，如下所述：

* 消除对用于灾难恢复的备用场的需要以及相关的基础结构成本。 使用 Site Recovery 将整个场（Web 层、应用层和数据库层）复制到 Azure 或辅助站点。
* 简化应用程序部署和管理。 部署到主站点的更新会自动复制，因此可在故障转移和恢复辅助站点中的场之后使用。 此外，还可降低使后备场保持最新状态的管理复杂性和相关成本。
* 按照副本环境的需要创建一个与生产类似的副本来进行测试和调试，从而简化 SharePoint 应用程序的开发与测试。
* 使用站点恢复将 SharePoint 部署迁移到 Azure，从而简化从本地到云的过渡过程。

[详细了解](site-recovery-sharepoint.md) 如何保护 SharePoint。

## <a name="protect-dynamics-ax"></a>保护 Dynamics AX
Azure Site Recovery 可通过以下方式帮助保护 Dynamics AX ERP 解决方案：

* 协调整个 Dynamics AX 环境（Web 和 AOS 层、数据库层、SharePoint）到 Azure 或到辅助站点的复制。
* 简化 Dynamics AX 部署到云 (Azure) 的迁移。
* 通过按需创建一个与生产类似的副本来进行测试和调试，简化 Dynamics AX 应用程序的开发与测试。

[详细了解](site-recovery-dynamicsax.md) 如何保护 Dynamic AX。

## <a name="protect-rds"></a>保护 RDS
远程桌面服务 (RDS) 允许虚拟桌面基础结构 (VDI)、基于会话的桌面和应用程序，让用户能够在任何地方工作。 使用 Azure Site Recovery 可以：

* 将托管或非托管池虚拟桌面到辅助站点以及远程应用程序和会话复制到辅助站点或 Azure。

* 下面是可以复制的项：

| **RDS** |**将 Azure VM 复制到 Azure** | **将 Hyper-V VM 复制到辅助站点** | **将 Hyper-V VM 复制到 Azure** | **将 VMware VM 复制到辅助站点** | **将 VMware VM 复制到 Azure** | **将物理服务器复制到辅助站点** | **将物理服务器复制到 Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **入池虚拟桌面（非托管）** |否|是 |否 |是 |否 |是 |否 |
| **入池虚拟桌面（托管但不包含 UPD）** |否|是 |否 |是 |否 |是 |否 |
| **远程应用程序和桌面会话（不包含 UPD）** |是|是 |是 |是 |是 |是 |是 |

[Set up disaster recovery for RDS using Azure Site Recovery](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure)（使用 Azure Site Recovery 为 RDS 设置灾难恢复）。

[详细了解](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) 如何保护 RDS。

## <a name="protect-exchange"></a>保护 Exchange
站点恢复可通过以下方式帮助保护 Exchange：

* 对于小型 Exchange 部署，例如单一服务器或服务器单机，站点恢复可以复制和故障转移到 Azure 或辅助站点。
* 对于大型部署，站点恢复可与 Exchange DAGS 集成。
* 在企业中进行 Exchange 灾难恢复时，Exchange DAG 是建议的解决方案。  站点恢复中的恢复计划可以包含 DAG，以便跨站点协调 DAG 故障转移。

[详细了解](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) 如何保护 Exchange。

## <a name="protect-sap"></a>保护 SAP
按如下所述使用站点恢复来保护 SAP 部署：

* 将组件复制到 Azure，以便保护在本地运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序。
* 将组件复制到其他 Azure 数据中心，以便保护在 Azure 中运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序。
* 使用站点恢复将 SAP 部署迁移到 Azure，从而简化云迁移。
* 通过创建一个按需生产克隆来测试 SAP 应用程序，简化 SAP 项目的升级、测试和原型制作。

[详细了解](site-recovery-sap.md) 如何保护 SAP。

## <a name="protect-iis"></a>保护 IIS
按如下所述使用 Site Recovery 来保护 IIS 部署：

Azure Site Recovery 可以将环境中的关键组件复制到冷远程站点或公有云（例如 Microsoft Azure），从而提供灾难恢复。 由于包含 Web 服务器和数据库的虚拟机将复制到恢复站点，因此不需单独备份配置文件或证书。 依赖于环境变量（在故障转移后已更改）的应用程序映射和绑定可以通过集成到灾难恢复计划中的脚本进行更新。 仅当故障转移时，才会在恢复站点中启动虚拟机。 不仅如此，Azure Site Recovery 还提供以下功能，帮助你协调端到端故障转移：

-   顺序安排各层中虚拟机的关机和启动。
-   添加脚本，以便在虚拟机启动后更新其上的应用程序依赖项和绑定。 也可使用脚本更新 DNS 服务器，使之指向恢复站点。
-   通过映射主网络和恢复网络，在故障转移前向虚拟机分配 IP 地址，以便在故障转移后使用不需更新的脚本。
-   能够对 Web 服务器上的多个 Web 应用程序进行一键式故障转移，因此在发生灾难时不会造成混淆。
-   能够在适用于 DR 演练的隔离环境中测试恢复计划。

[详细了解](https://aka.ms/asr-iis)如何保护 IIS Web 场。

## <a name="protect-citrix-xenapp-and-xendesktop"></a>保护 Citrix XenApp 和 XenDesktop
使用 Site Recovery 保护 Citrix XenApp 和 XenDesktop 部署，如下所示：

* 通过将不同的部署层（包括 AD DNS 服务器、SQL 数据库服务器、Citrix 传递控制器、StoreFront 服务器、XenApp Master (VDA)、Citrix XenApp 许可证服务器）复制到 Azure 来启用保护 Citrix XenApp 和 XenDesktop 部署。
* 使用 Site Recovery 将 Citrix XenApp 和 XenDesktop 部署迁移到 Azure，从而简化云迁移。
* 按需创建一个与生产类似的副本来进行测试和调试，从而简化 Citrix XenApp/XenDesktop 测试。
* 此解决方案仅适用于 Windows Server 操作系统虚拟桌面，而不适用于客户端虚拟桌面，因为 Azure 中的授权尚不支持客户端虚拟桌面。
[了解](https://azure.microsoft.com/pricing/licensing-faq/) Azure 中的客户端/服务器桌面授权。

[了解](site-recovery-citrix-xenapp-and-xendesktop.md)如何保护 Citrix XenApp 和 XenDesktop 部署。 也可参阅 [Citrix 的白皮书](https://aka.ms/citrix-xenapp-xendesktop-with-asr)，其中提供了相同的详细信息。

## <a name="next-steps"></a>后续步骤

Azure VM 复制[入门](azure-to-azure-quickstart.md)。
