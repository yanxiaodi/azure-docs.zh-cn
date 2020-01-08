---
title: Azure Database for MySQL 中支持的版本
description: 说明 Azure Database for MySQL 中支持的版本。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 25251b617522840412a4868331e155285f64a18c
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962584"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>支持的 Azure Database for MySQL 服务器版本

使用 InnoDB 引擎通过 [MySQL Community Edition](https://www.mysql.com/products/community/) 开发 Azure Database for MySQL。

MySQL 使用 X.Y.Z 命名方案。 X 是主版本，Y 是次版本，Z 是 bug 修复版本。 有关方案的详细信息，请参阅 [MySQL 文档](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)。

> [!NOTE]
> 在服务中，网关用于将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MySQL 版本，而不是 MySQL 服务器实例上运行的实际版本。 若要确定 MySQL 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。

Azure Database for MySQL 目前支持以下版本：

## <a name="mysql-version-56"></a>MySQL 版本 5.6

Bug 修复版本：5.6.44

请参阅 MySQL[发行说明](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-44.html)，详细了解此版本中的改进和修复。

## <a name="mysql-version-57"></a>MySQL 版本 5.7

Bug 修复版本：5.7.26

请参阅 MySQL[发行说明](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-26.html)，详细了解此版本中的改进和修复。

## <a name="mysql-version-80"></a>MySQL 版本 8.0

> [!IMPORTANT]
> MySQL 8.0 目前以预览版提供。

Bug 修复版本：8.0.15

请参阅 MySQL[发行说明](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html)，详细了解此版本中的改进和修复。

## <a name="managing-updates-and-upgrades"></a>管理更新和升级
该服务会自动管理针对 Bug 修复版本更新的修补。 例如，5.7.20 到 5.7.21。  

目前，不支持次版本和主版本升级。 例如，不支持从 MySQL 5.6 升级到 MySQL 5.7。 如果要从 5.6 升级到 5.7，请将其[转储和还原](./concepts-migrate-dump-restore.md)到使用新引擎版本创建的服务器。

## <a name="next-steps"></a>后续步骤

有关基于服务层级的具体资源配额和限制的信息，请参阅[服务层级](./concepts-pricing-tiers.md)
