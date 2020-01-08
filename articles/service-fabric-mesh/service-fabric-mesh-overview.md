---
title: Azure Service Fabric 网格概述 | Microsoft Docs
description: 了解 Azure Service Fabric 网格。 使用 Service Fabric 网格可以部署和缩放应用程序，而无需考虑应用程序的基础结构需求。
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/1/2018
ms.topic: overview
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: d315ca0702b1d76e0f990d4d33a3807a1dc57935
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428186"
---
# <a name="what-is-service-fabric-mesh"></a>什么是 Service Fabric 网格？

此视频提供了 Service Fabric 网格的快速概述。
> [!VIDEO https://www.youtube.com/embed/7qWeVGzAid0]

Azure Service Fabric 网格是一个完全托管的服务，由此开发者可部署微服务应用程序，而无需管理虚拟机、存储或网络。 无需考虑赋能基础结构，就能省心地运行和缩放 Service Fabric 网格上托管的应用程序。  Service Fabric 网格包含由数千台计算机组成的群集。  开发人员察觉不到所有的群集操作。 上传代码，并指定所需的资源、可用性要求和资源限制。  Service Fabric 网格会自动分配基础结构，并处理基础结构故障，从而确保应用程序高度可用。 你只需关心应用程序的运行状况和响应能力，而不必考虑基础结构。  

[!INCLUDE [preview note](./includes/include-preview-note.md)]

本文提供 Service Fabric 网格关键优势的概述。

## <a name="great-developer-experience"></a>极佳的开发人员体验

Service Fabric 网格支持可在容器中运行的任何编程语言或框架。 Visual Studio 2019 和 Visual Studio Code 工具支持针对 .NET 和 .NET Core 应用程序提供强大的编辑和调试体验。 

使用 Service Fabric 网格可以：

- 将现有的应用程序“直接迁移”到容器中，以大规模地现代化和运行当前应用程序。
- 在 Azure 中大规模构建和部署新的微服务应用程序。  与其他 Azure 服务或者容器中运行的现有应用程序集成。 每个微服务都是安全的网络隔离应用程序的一部分。 微服务具有为 CPU 核心、内存、磁盘空间等定义的资源治理策略。
- 无需对现有的应用程序进行更改，就能集成和扩展这些应用程序。 使用自己的虚拟网络将现有应用程序连接到新应用程序。  
- 通过迁移到 Service Fabric 网格，来现代化现有的云服务应用程序。  

## <a name="simple-operational-lifecycle"></a>简单的操作生命周期

轻松管理正在运行的应用程序，包括监视应用程序，以及在生产环境中进行调试。 这种管理包括应用程序升级和版本控制。 这些应用程序可以包括单个微服务，或者在其自身网络中隔离的多个微服务。 应用程序高效运行，部署、定位和故障转移时间很短。

使用 Service Fabric 网格可以：

- 无需显式预配和管理基础结构，即可部署和管理应用程序。  Service Fabric 网格会自动预配、升级、修补和维护底层基础结构。
- 使用集成式工具设置持续集成，以轻松打包和部署应用程序。
- 利用 Azure 资源管理器资源的所有功能。 这些功能的示例包括审核线索和[基于角色的访问控制](/azure/role-based-access-control/overview))。 在 Azure 中部署到 Service Fabric 网格服务的所有资源都是 Azure 资源管理器资源。 这些资源包括应用程序、服务、机密，等等。
- 使用 [Azure 门户](https://portal.azure.com)、资源管理器模板或 Azure CLI/PowerShell 库部署和管理资源。
- 使用 [Application Insights](/azure/application-insights/)（或所选工具）设置操作监视和警报，以从平台捕获操作和诊断跟踪。
- 使用 [Application Insights](/azure/application-insights/) 或所选工具访问应用程序模型发出的应用程序诊断信息。
- 通过为应用程序定义中的服务指定自动缩放规则来优化资源使用率。

## <a name="mission-critical-platform-capabilities"></a>任务关键型平台功能

Service Fabric 网格可创建跨越 [Azure 可用性区域](/azure/availability-zones/az-overview)和/或地缘政治区域边界的群集集合。 Service Fabric 网格使用一组意向（例如规模、硬件要求、持久性要求和安全策略）描述应用程序。  部署应用程序时，Service Fabric 网格会查找该应用程序的最佳运行位置。

使用 Service Fabric 网格可以：

- 利用高可用性、扩展/缩减功能、可发现性、业务流程、消息路由、可靠消息传递、无需停机升级、安全/机密管理、灾难恢复、状态管理、配置管理和分布式事务。
- 创建应用程序时在多个应用程序模型之间进行选择。
- 使用通过 Swagger 生成的语言特定绑定，利用通过 REST 终结点公开的平台功能。
- 跨[可用性区域](/azure/availability-zones/az-overview)和多个区域部署应用程序，以实现异地可靠性。
- 使用 Azure 提供的所有安全性和符合性功能。

## <a name="next-steps"></a>后续步骤

在 Visual Studio 中，只需执行几个步骤就能部署一个示例项目。 有关详细信息，请参阅[创建 ASP.NET Core 网站](service-fabric-mesh-quickstart-dotnet-core.md)。 

找到[常见问题](service-fabric-mesh-faq.md)的答案。


<!-- Links -->

[service-fabric-overview]: ../service-fabric/service-fabric-overview.md
