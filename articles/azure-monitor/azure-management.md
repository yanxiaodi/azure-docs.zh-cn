---
title: Azure 管理和 Operations Management Suite (OMS) | Microsoft Docs
description: 概述针对 Azure 应用程序和资源的管理领域，并提供 Azure 管理工具上的内容的链接，这些工具以前以 Operations Management Suite (OMS) 形式捆绑在一起。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2018
ms.author: bwren
ms.openlocfilehash: 4096ee477dc1d40ff6b98b20dd384c6ffad17e5f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779272"
---
# <a name="azure-management---monitoring"></a>Azure 管理 - 监视

Azure 中的监视是 Azure 管理的一个方面。  本文简要介绍了在 Azure 中部署和维护应用程序与资源所需的不同管理方面，并提供了相应的入门文档链接。

## <a name="management-in-azure"></a>Azure 中的管理

管理指的是维护业务应用程序以及为其提供支持的资源所需的任务和流程。  Azure 包含多个服务和工具，它们协同工作，不仅为 Azure 中运行的应用程序提供全面管理，而且还为其他云中以及本地运行的应用程序提供全面管理。  了解可用的不同工具以及如何将它们一起用于各种管理方案，是设计一个完整管理环境的第一步。

下图说明了维护任何应用程序或资源所需的不同管理方面。  这些不同方面可被视为一个生命周期，其中每个方面都是资源生命期的连续交替所必需的。  这始于其初始部署，贯穿于其持续运转，在其停用时结束。

![管理功能](media/management-overview/management-capabilities.png)


下列部分简要介绍了不同的管理领域，并提供了用于处理这些领域的主要 Azure 服务的详细内容链接。

## <a name="monitor"></a>监视器
监视是一种数据收集和分析操作，用于确定商业应用程序的性能、运行状况及应用程序和其所依赖的资源的可用性。 通过实施有效的监视策略，可了解应用程序中不同组件的具体操作情况，通过主动发出有关关键问题的通知，可帮助在问题发生前解决问题，从而延长运行时间。 Azure 中的监视功能主要由 [Azure Monitor](../azure-monitor/overview.md) 提供，后者可以提供常用存储来存储监视数据、提供多个数据源从支持应用程序的不同层来收集数据，以及提供多项功能来分析和响应所收集的数据。

## <a name="configure"></a>配置
配置指的是应用程序和资源的初始部署和配置，以及其通过修补程序和更新进行的当前维护。  通过脚本和策略自动执行这些任务，可以消除冗余，最大限度地节省时间和工作量，以及提高准确性和效率。  [Azure 自动化](../automation/automation-intro.md)提供了大量用于自动执行配置任务的服务。  除了用于自动执行流程的 runbook 之外，它还提供了配置和更新管理，以帮助你通过策略管理配置以及识别和部署更新。

## <a name="govern"></a>治理
“治理”提供了机制和流程来保持对 Azure 中的应用程序和资源的控制。  它涉及规划计划和设置战略优先级。  Azure 中的治理主要是通过两个服务实现的。  [Azure Policy](../governance/policy/overview.md) 可用于创建、分配和管理策略定义，用以对资源强制实施不同的规则和操作，从而使这些资源保持符合公司标准和服务级别协议。 使用[Azure 成本管理](../cost-management/overview-cost-mgt.md), 可以跟踪 azure 资源和其他云提供商 (包括 AWS 和 Google) 的云使用情况和支出。

## <a name="secure"></a>安全
管理应用程序、资源和数据的安全性涉及以下事项的组合：评估威胁、收集和分析安全数据，以及确保应用程序和资源以安全方式设计并配置。  安全监视和威胁分析由 [Azure 安全中心](../security-center/security-center-intro.md)提供，该中心包括跨混合云工作负荷的统一安全管理和高级威胁防护。  另请参阅 [Azure 安全性简介](../security/fundamentals/overview.md)以了解有关 Azure 中的安全性的全面信息，以及有关安全配置 Azure 资源的指南。


## <a name="protect"></a>保护
保护指的是确保应用程序和数据始终可用（即使在发生你无法控制的故障时也是如此）。  Azure 中的保护由两个服务提供。  [Azure 备份](../backup/backup-introduction-to-azure-backup.md)提供数据备份和恢复（在云中或本地）。    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) 通过在发生灾难时提供业务连续性和即时恢复，来确保应用程序的高可用性。

## <a name="migrate"></a>迁移 
迁移指的是将当前在本地运行的工作负荷转换到 Azure 云中。  [Azure Migrate](../migrate/migrate-overview.md) 是一项服务，可帮助评估本地虚拟机到 Azure 的迁移适用性，包括基于性能的大小调整和成本估计。  Azure Site Recovery 可帮助执行[从本地](../site-recovery/migrate-tutorial-on-premises-azure.md)或[从 Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md) 实际迁移虚拟机。  [Azure 数据库迁移](../dms/dms-overview.md)会帮助你将多个数据库源迁移到 Azure 数据平台。

