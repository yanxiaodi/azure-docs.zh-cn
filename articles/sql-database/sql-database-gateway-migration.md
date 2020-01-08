---
title: 适用于 Azure SQL 数据库的网关流量迁移通知 |Microsoft Docs
description: 本文向用户提供有关迁移 Azure SQL 数据库网关 IP 地址的通知
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: a2064e0012abea237c1b4c0c6f7e35119c1aeff5
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686806"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>将 Azure SQL 数据库流量迁移到更新的网关

Azure 基础结构改进后，Microsoft 会定期刷新硬件，以确保提供最佳的客户体验。 在接下来的几个月中，我们计划添加基于较新的硬件代构建的网关，将流量迁移到这些网关，并最终解除在某些区域中的旧硬件上构建网关。  

在每个区域中提供的任何网关更改之前，将通过电子邮件和 Azure 门户获取客户通知。 在[AZURE SQL 数据库网关 IP 地址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)表中将保留最新的信息。

## <a name="impact-of-this-change"></a>此更改的影响

第一轮流量迁移到较新的网关的计划时间为**2019 年10月14日**以下区域：
- 巴西南部
- 美国西部
- 西欧
- East US
- 美国中部
- 东南亚
- 美国中南部
- 北欧
- 美国中北部
- 日本西部
- 日本东部
- 美国东部 2
- 东亚

流量迁移将更改 DNS 为你的 SQL 数据库解析的公共 IP 地址。
如果有以下情况，将会受到影响：
- 硬编码本地防火墙中任何特定网关的 IP 地址
- 使用 Microsoft .SQL 作为服务终结点但无法与网关 IP 地址通信的任何子网

如果有以下情况，则不会受到影响：
- 作为连接策略的重定向
- 从 Azure 内部连接到 SQL 数据库并使用服务标记
- 使用受支持的 JDBC Driver for SQL Server 所建立的连接将不会产生任何影响。 有关支持的 JDBC 版本，请参阅[下载 MICROSOFT JDBC Driver for SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。

## <a name="what-to-do-you-do-if-youre-affected"></a>如果受到影响，应如何操作

对于 TCP 端口1433和端口范围11000-11999 上区域中的所有[AZURE SQL 数据库网关 ip 地址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)，建议你允许到 ip 地址的出站流量。 此建议适用于从本地连接的客户端，以及通过服务终结点连接的客户端。 有关端口范围的详细信息，请参阅[连接策略](sql-database-connectivity-architecture.md#connection-policy)。

使用低于版本4.0 的 Microsoft JDBC 驱动程序从应用程序进行的连接可能无法通过证书验证。 较低版本的 Microsoft JDBC 依赖证书的 "使用者" 字段中的公用名（CN）。 缓解措施是确保将 hostNameInCertificate 属性设置为 *. database.windows.net。 有关如何设置 hostNameInCertificate 属性的详细信息，请参阅[与 SSL 加密连接](/sql/connect/jdbc/connecting-with-ssl-encryption)。

如果上述缓解不起作用，请使用以下 URL 为 SQL 数据库提供支持请求： https://aka.ms/getazuresupport

## <a name="next-steps"></a>后续步骤

- 了解有关[AZURE SQL 连接体系结构](sql-database-connectivity-architecture.md)的详细信息
