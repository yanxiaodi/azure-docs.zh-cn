---
title: Azure Database for PostgreSQL - 单一服务器中的高可用性概念
description: 本文提供了使用 Azure Database for PostgreSQL - 单一服务器时的高可用性信息。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f54c83099957b4d8795c4049be52d70e8a0e2a61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073437"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 单一服务器中的高可用性概念
Azure Database for PostgreSQL 服务提供有保证的高级别可用性。 财务备份服务级别协议 (SLA) 一定是通用版本。 使用此服务期间，几乎没有应用程序故障时间。

## <a name="high-availability"></a>高可用性
高可用性 (HA) 模型以节点级中断发生时的内置故障转移机制为依据。 硬件故障或响应服务部署都有可能会导致节点级中断发生。

在任何时候，对 Azure Database for PostgreSQL 数据库服务器做出的更改都发生在事务的上下文中。 更改会在事务提交时同步记录到 Azure 存储中。 如果发生节点级中断，数据库服务器会自动新建节点，并将数据存储附加到新节点。 这会删除任何活动连接，并且不会提交任何即时事务。

## <a name="application-retry-logic-is-essential"></a>应用程序重试逻辑至关重要
必须构建 PostgreSQL 数据库应用程序，以检测和重试已删除的连接和失败的事务，这一点很重要。 当应用程序重试时，应用程序的连接会在透明状态下重定向到新创建的实例，以取代失败实例。

在 Azure 内部，网关用于将连接重定向到新建实例。 发生中断后，整个故障转移过程一般需要几十秒才能完成。 因为重定向由网关内部处理，所以对于客户端应用程序，外部连接字符串也是这样。

## <a name="scaling-up-or-down"></a>横向扩展或缩减
与 HA 模型类似，在 Azure Database for PostgreSQL 横向扩展或缩减时，将创建具有指定大小的新服务器实例。 现有数据存储从原始实例中分离出来，并被附加到新实例中。

执行缩放操作期间，数据库连接会中断。 客户端应用程序的连接中断，未提交的未结事务也会遭取消。 在客户端应用程序重试连接或建立新连接后，网关便会将连接定向到新设置大小的实例。 

## <a name="next-steps"></a>后续步骤
- 了解如何[处理暂时性连接错误](concepts-connectivity.md)
- 了解如何[使用只读副本复制数据](howto-read-replicas-portal.md)
