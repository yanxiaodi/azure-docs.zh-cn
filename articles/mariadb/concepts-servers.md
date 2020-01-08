---
title: Azure Database for MariaDB 中的服务器概念
description: 本主题提供使用 Azure Database for MariaDB 服务器的注意事项和指南。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f61f8740c9514f6276afb2ee84bcdccdc54c0710
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61040907"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的服务器概念
本文提供使用 Azure Database for MariaDB 服务器的注意事项和指南。

## <a name="what-is-an-azure-database-for-mariadb-server"></a>什么是 Azure Database for MariaDB 服务器？

Azure Database for MariaDB 服务器是多个数据库的中心管理点。 它的 MariaDB 服务器构造与本地环境中用户可能比较熟悉的构造相同。 具体而言，Azure Database for MariaDB 服务是托管的服务，它提供性能保证，并公开服务器级访问权限和功能。

Azure Database for MariaDB 服务器：

- 在 Azure 订阅中创建。
- 是数据库的父资源。
- 为数据库提供了一个命名空间。
- 是具有强生存期语义的容器 - 删除服务器时会删除所包含的数据库。
- 并置区域中的资源。
- 为服务器和数据库访问提供连接终结点。
- 提供应用于其数据库的管理策略的作用域：登录名、防火墙、用户、角色、配置等。
- 可在 MariaDB 引擎版本 10.2 中使用。 有关详细信息，请参阅[支持的 Azure Database for MariaDB 数据库版本](./concepts-supported-versions.md)。

在 Azure Database for MariaDB 服务器中，可创建一个或多个数据库。 可以选择为每个服务器创建单一数据库来使用所有资源，也可以选择创建多个数据库来共享资源。 按服务器根据定价层、vCore 和存储 (GB) 的配置采用结构化定价。 有关详细信息，请参阅[定价层](./concepts-pricing-tiers.md)。

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>如何保护 Azure Database for MariaDB 服务器？

以下元素有助于确保安全地访问数据库。

|||
| :--| :--|
| **身份验证和授权** | Azure Database for MariaDB 服务器支持本机 MySQL 身份验证。 可使用服务器的管理员登录名连接到服务器并进行身份验证。 |
| 协议  | 该服务支持 MySQL 使用的基于消息的协议。 |
| TCP/IP  | 通过 TCP/IP 和 Unix 域套接字支持该协议。 |
| **防火墙** | 为了帮助保护数据，在用户指定具有访问权限的计算机之前，防火墙规则将禁止所有对数据库服务器的访问。 请参阅 [Azure Database for MariaDB 服务器防火墙规则](./concepts-firewall-rules.md)。 |
| SSL  | 该服务支持在应用程序和数据库服务器之间强制进行 SSL 连接。 请参阅[配置应用程序的 SSL 连接性以安全连接到 Azure Database for MariaDB](./howto-configure-ssl.md)。 |

## <a name="how-do-i-manage-a-server"></a>如何管理服务器？
可通过使用 Azure 门户或 Azure CLI 来管理 Azure Database for MariaDB 服务器。

## <a name="next-steps"></a>后续步骤
- 有关该服务的概述，请参阅 [Azure Database for MariaDB 概述](./overview.md)
- 有关基于服务层级  的具体资源配额和限制的信息，请参阅[服务层级](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
