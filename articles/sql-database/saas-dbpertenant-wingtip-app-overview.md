---
title: Azure SQL 数据库多租户应用示例 - Wingtip SaaS | Microsoft Docs
description: 借助使用 Azure SQL 数据库的示例多租户应用程序，了解 Wingtip SaaS 示例
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 16f4bb946af4720a327a8755c6bf9187f3b71ba6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570338"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>多租户 SaaS 应用简介，该应用通过“每个租户各有数据库”模式使用 SQL 数据库

Wingtip SaaS 应用程序是一个示例多租户应用。 该应用使用“每个租户各有数据库”（一种 SaaS 应用程序模式）为多个租户提供服务。 该应用使用多个 SaaS 设计及管理模式，展示支持 SaaS 方案的 Azure SQL 数据库功能。 Wingtip SaaS 应用的部署时间不到五分钟，可快速启动并运行。

[WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 存储库提供了应用程序源代码和管理脚本。 开始操作前，请先参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)，了解下载和取消阻止 Wingtip Tickets 管理脚本的步骤。

## <a name="application-architecture"></a>应用程序体系结构

Wingtip SaaS 应用使用“每个租户各有数据库”模型。 它使用 SQL 弹性池最大程度提高效率。 为了预配租户并将其映射到租户数据，需使用目录数据库。 核心 Wingtip SaaS 应用程序使用一个池和三个示例租户，外加目录数据库。 已使用 DNS 别名预配目录和租户服务器。 这些别名用于维持对 Wingtip 应用程序使用的活动资源的引用。 这些别名已更新以指向灾难恢复教程中的恢复资源。 完成许多 Wingtip SaaS 教程都会导致在初始部署中使用加载项。 将介绍分析数据库和跨数据库架构管理等加载项。


![Wingtip SaaS 体系结构](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


学习教程及使用应用时，应关注 SaaS 模式，因为这些模式与数据层相关。 换句话说，请专注于数据层，不要将过多精力放在分析应用本身上。 了解这些 SaaS 模式的实现方式是在应用程序中实现这些模式的关键。 另外，请考虑根据特定业务要求进行任何必要的修改。

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL 数据库 Wingtip SaaS 教程

部署应用后，请浏览基于初始部署制作的以下教程。 这些教程探索常见 SaaS 模式，这些模式利用 SQL 数据库、Azure SQL 数据仓库和其他 Azure 服务的内置功能。 教程包括 PowerShell 脚本及详细说明。 这些说明可简化对应用程序中相同 SaaS 管理模式的理解和实现。


| 教程 | 描述 |
|:--|:--|
| [SQL 数据库多租户 SaaS 应用示例指南和提示](saas-tenancy-wingtip-app-guidance-tips.md) | 下载并运行 PowerShell 脚本，准备应用程序部件。 |
|[部署和浏览 Wingtip SaaS 应用程序](saas-dbpertenant-get-started-deploy.md)|  使用 Azure 订阅部署并浏览 Wingtip SaaS 应用程序。 |
|[预配和编录租户](saas-dbpertenant-provision-and-catalog.md)| 了解应用程序如何使用目录数据库连接到租户，以及目录如何将租户映射到其数据。 |
|[监视和管理性能](saas-dbpertenant-performance-monitoring.md)| 了解如何使用 SQL 数据库的监视功能，以及如何设置在超出性能阈值时发出警报。 |
|[监视 Azure Monitor 日志](saas-dbpertenant-log-analytics.md) | 了解如何使用[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)来监视跨多个池的大量资源。 |
|[还原单个租户](saas-dbpertenant-restore-single-tenant.md)| 了解如何将租户数据库还原到先前的时间点。 此外，请了解如何还原到并行数据库，这会使现有租户数据库保持联机。 |
|[管理租户数据库架构](saas-tenancy-schema-management.md)| 了解如何跨所有租户数据库更新架构和更新参考数据。 |
|[运行跨租户分布式查询](saas-tenancy-cross-tenant-reporting.md) | 创建即席分析数据库, 并跨所有租户运行实时分布式查询。  |
|[对提取的租户数据运行分析](saas-tenancy-tenant-analytics.md) | 将租户数据提取到分析数据库或数据仓库，以运行脱机分析查询。 |


## <a name="next-steps"></a>后续步骤

- [部署和使用 Wingtip Tickets SaaS 应用示例的常规指南和提示](saas-tenancy-wingtip-app-guidance-tips.md)
- [部署 Wingtip SaaS 应用程序](saas-dbpertenant-get-started-deploy.md)
