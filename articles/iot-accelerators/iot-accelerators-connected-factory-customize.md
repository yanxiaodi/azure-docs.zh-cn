---
title: 自定义连接的工厂解决方案 - Azure | Microsoft Docs
description: 介绍如何自定义连接的工厂解决方案加速器的行为。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 6062f8b3992732e0e0f9bbdae9549e69c393f4ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080483"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>自定义连接的工厂解决方案如何显示来自 OPC UA 服务器的数据

连接的工厂解决方案将聚合并显示来自连接到解决方案的 OPC UA 服务器的数据。 可以在解决方案中浏览命令并将其发送到 OPC UA 服务器。 有关 OPC UA 的详细信息，请参阅[连接的工厂常见问题解答](iot-accelerators-faq-cf.md)。

解决方案中聚合数据的示例包括设备综合效率 (OEE) 和关键性能指标 (KPI)，可在工厂的仪表板、生产线和工作站级别查看这些数据。 以下屏幕截图显示慕尼黑工厂中生产线 1 上装配工作站的 OEE 和 KPI 值    ：

![解决方案中 OEE 和 KPI 值的示例][img-oee-kpi]

在此解决方案中，可以查看来自名为“stations”的 OPC UA 服务器上特定数据项目的详细信息  。 以下屏幕截图显示特定工作站中已生产产品数的条形图：

![已生产产品数的条形图][img-manufactured-items]

单击其中一个图形，即可使用时序见解 (TSI) 进一步探索数据：

![使用时序见解探索数据][img-tsi]

本文介绍：

- 如何使数据适用于解决方案中的各种视图。
- 如何自定义解决方案显示数据的方式。

## <a name="data-sources"></a>数据源

连接的工厂解决方案显示来自连接到解决方案的 OPC UA 服务器的数据。 默认安装包括运行工厂模拟的多台 OPC UA 服务器。 可以添加你自己的 OPC UA 服务器的 [通过网关连接] [lnk-连接-cf] 到你的解决方案。

可以在仪表板中浏览已连接的 OPC UA 数据库可向解决方案发送的数据项目：

1. 选择“浏览器”以导航到“选择 OPC UA 服务器”视图   ：

    ![导航到“选择 OPC UA 服务器”视图][img-select-server]

1. 选择一台服务器，并单击“连接”。  出现安全警告时，单击“继续”  。

    > [!NOTE]
    > 此警告针对每个服务器仅出现一次，它在解决方案仪表板和服务器之间建立信任关系。

1. 现可浏览服务器可向解决方案发送的数据项目。 待发送到解决方案的项目都带有一个复选标记：

    ![已发布的项目][img-published]

1. 如果你是解决方案中的管理员，可以选择发布数据项，使其在连接的工厂解决方案中可用  。 管理员还可在 OPC UA 服务器中更改数据项目的值以及调用方法。

## <a name="map-the-data"></a>映射数据

连接的工厂解决方案将 OPC UA 服务器中已发布的数据项映射并聚合到解决方案中的各种视图。 预配解决方案时，连接的工厂解决方案会部署到 Azure 帐户。 Visual Studio 连接的工厂解决方案中的 JSON 文件会存储此映射信息。 可以在连接的工厂 Visual Studio 解决方案中查看和修改此 JSON 配置文件。 在进行更改后，可重新部署解决方案。

配置文件可用于：

- 编辑现有的模拟工厂、生产线和工作站。
- 映射来自连接到解决方案的实际 OPC UA 服务器的数据。

有关映射和聚合数据以满足特定要求的详细信息，请参阅[如何配置连接的工厂解决方案加速器](iot-accelerators-connected-factory-configure.md)。

## <a name="deploy-the-changes"></a>部署更改

完成对 ContosoTopologyDescription.json 文件的更改后，必须将连接的工厂解决方案重新部署到 Azure 帐户  。

azure-iot-connected-factory 存储库包含 build.ps1 PowerShell 脚本，该脚本可用于重新构建并部署解决方案   。

## <a name="next-steps"></a>后续步骤

通过阅读下列文章，详细了解连接的工厂解决方案加速器：

* [Azureiotsolutions.com 站点权限][lnk-permissions]
* [连接的工厂常见问题解答](iot-accelerators-faq-cf.md)
* [常见问题解答][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md