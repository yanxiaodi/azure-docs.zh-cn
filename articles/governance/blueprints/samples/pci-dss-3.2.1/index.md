---
title: 示例 - PCI-DSS v3.2.1 蓝图 - 概述
description: 支付卡行业数据安全标准 v3.2.1 蓝图示例的概述。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 390d07a473cff86d8870a7ec8229c6343f62c4e7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232648"
---
# <a name="overview-of-the-pci-dss-v321-blueprint-sample"></a>PCI-DSS v3.2.1 蓝图示例概述

PCI-DSS v3.2.1 蓝图示例是一组帮助实现 PCI-DSS v3.2.1 合规性的策略。 此蓝图可帮助客户治理带有 PCI-DSS工作负荷且基于云的环境。
PCI-DSS 蓝图为 Azure 部署的任何需要此认证的体系结构部署一组核心策略。

## <a name="control-mapping"></a>控制映射

控件映射部分提供了有关包含在此计划内的策略的详细信息，以及这些策略如何帮助满足由 PCI-DSS v3.2.1 定义的各种控制要求。 分配给一个体系结构时，资源由 Azure Policy 评估是否不符合已分配的策略。

分配此蓝图后，请在 Azure Policy 合规性仪表板中查看Azure 环境合规性级别。

## <a name="next-steps"></a>后续步骤

你已查看了 PCI-DSS v3.2.1 蓝图示例概述。 接下来，请访问以下文章，了解控制映射以及如何部署此示例：

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 蓝图 - 控制映射](./control-mapping.md)
> [PCI-DSS v3.2.1 蓝图 - 部署步骤](./deploy.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。