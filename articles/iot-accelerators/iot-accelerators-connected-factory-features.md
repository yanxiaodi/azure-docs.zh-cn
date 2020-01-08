---
title: 连接的工厂解决方案功能 - Azure | Microsoft Docs
description: 连接的工厂预配置解决方案的功能概述。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: 2a11640959a8c7fdd0d238aba92698eb47934969
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080451"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>什么是连接的工厂 IoT 解决方案加速器？

连接的工厂是 Microsoft 的 Azure 工业 IoT 参考体系结构的实现，打包为开源解决方案。 可以使用它作为商业产品的起点。 可以通过 [Azure IoT 解决方案加速器](https://www.azureiotsolutions.com/#solutions/types/CF)将连接的工厂解决方案的预构建版本部署到 Azure 订阅。

![连接的工厂解决方案仪表板](./media/iot-accelerators-connected-factory-features/dashboard.png)

[GitHub](https://github.com/Azure/azure-iot-connected-factory) 上提供了连接的工厂解决方案加速器。

连接的工厂包括以下功能：

## <a name="industrial-device-interoperability"></a>工业设备互操作性

- 连接到具有 OPC UA 接口的工业资产。
- 使用模拟的生产线（在 Docker 容器中运行 OPC UA 服务器）查看来自它们的实时遥测数据。
- 从云仪表板浏览 OPC UA 服务器的 OPC UA 信息模型。

## <a name="remote-management"></a>远程管理

- 从云仪表板配置 OPC UA 资产（调用方法、读写数据）。
- 从云仪表板发布和取消发布来自 OPC UA 资产的遥测数据。

## <a name="cloud-dashboard"></a>云仪表板

- 直接在云仪表板中查看遥测数据预览。
- 使用时序见解资源管理器仪表板查看遥测数据中的趋势并创建关联。
- 从云仪表板查看计算得到的整体设备效率 (OEE) 和关键绩效指标 (KPI)。
- 在树拓扑以及互动地图中查看工业资产层次结构。
- 从云仪表板中查看、确认和关闭警报。

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure 时序见解](../time-series-insights/time-series-insights-overview.md)是为存储、可视化和查询大量时序数据而开发的服务。 连接的工厂利用了此服务。
- 连接的工厂集成了此服务，从而可以对设备数据执行深入的实时分析。

## <a name="rules-and-alerts"></a>规则和警报

[为警报配置基于阈值的规则](iot-accelerators-connected-factory-configure.md)。

## <a name="end-to-end-security"></a>端到端安全性

- 使用基于角色的访问控制 (RBAC) 为用户配置安全权限。
- 端到端加密是使用 OPC UA 身份验证（使用 X.509 证书）以及安全令牌实现的。

## <a name="customizability"></a>可定制性

- 自定义解决方案来满足特定的业务需求。
- GitHub 上提供了完整的解决方案源代码。 请参阅[连接的工厂预配置解决方案](https://github.com/Azure/azure-iot-connected-factory)存储库。

## <a name="next-steps"></a>后续步骤

若要了解有关连接工厂解决方案加速器的详细信息，请参阅快速入门[尝试基于云的解决方案来管理我的工业 IoT 设备](quickstart-connected-factory-deploy.md)。
