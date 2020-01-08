---
title: 示例 - ISO 27001 ASE/SQL 工作负荷蓝图 - 概述
description: ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图示例的概述和体系结构。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: cee3867449aea1cb1c59ac5a7c3be57291d1aebc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000141"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图示例的概述

ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图示例为 [ISO 27001 共享服务](../iso27001-shared/index.md)蓝图示例提供了其他基础结构。
此蓝图帮助客户部署基于云的体系结构，以便为有认证或符合性要求的方案提供解决方案。

有两个 ISO 27001 蓝图示例：此示例和 [ISO 27001 共享服务](../iso27001-shared/index.md)蓝图示例。

> [!IMPORTANT]
> 此示例依赖于按 [ISO 27001 共享服务](../iso27001-shared/index.md)蓝图示例部署的基础结构。 它必须先部署。

## <a name="architecture"></a>体系结构

ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图示例部署一个基于平台即服务的 Web 环境。 该环境可以用来托管多个遵循 ISO 27001 标准的 Web 应用程序、Web API 和 SQL 数据库实例。 此蓝图示例依赖于 [ISO 27001 共享服务](../iso27001-shared/index.md)蓝图示例。

![ISO 27001 ASE/SQL 工作负荷蓝图示例设计](../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png)

此环境包括多项 Azure 服务，这些服务用于根据 ISO 27001 标准提供安全的、全面受监视的、面向企业的工作负荷基础结构。 此环境包括：

- 名为 DevOps 的[基于角色的访问控制](../../../../role-based-access-control/overview.md) (RBAC) 角色，该角色有权在按蓝图示例部署的 [Azure 应用服务环境](../../../../app-service/environment/intro.md)中部署和管理资源
- [Azure 策略](../../../policy/overview.md)，用于锁定哪些服务可以部署到该环境，以及拒绝创建任何公共 IP 地址 (PIP) 资源
- 一个虚拟网络，其中包含单个子网，与预先存在的[共享服务](../iso27001-shared/index.md)环境对等互连，并且强制所有流量经由[共享服务](../iso27001-shared/index.md)防火墙传递。 该虚拟网络托管以下资源：
  - 一个 [Azure 应用服务环境](../../../../app-service/environment/intro.md)，可用于托管一个或多个 Web 应用程序、Web API 或函数
  - 一个使用 VNet 服务终结点的 [Azure Key Vault](../../../../key-vault/key-vault-overview.md) 实例，用于存储由工作负荷环境中运行的应用程序使用的机密
  - 一个使用 VNet 服务终结点的 [Azure SQL 数据库](../../../../sql-database/sql-database-technical-overview.md)服务器实例，用于托管对工作负荷环境中运行的应用程序使用的数据库

## <a name="next-steps"></a>后续步骤

你已查看了 ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图示例的概述和体系结构。 接下来，请访问以下文章，了解控制映射以及如何部署此示例：

> [!div class="nextstepaction"]
> [ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图 - 控制映射](./control-mapping.md)
> [ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图 - 部署步骤](./deploy.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。