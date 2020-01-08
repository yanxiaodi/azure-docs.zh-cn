---
title: Azure Database for PostgreSQL - 单一服务器中支持的版本
description: 介绍了 Azure Database for PostgreSQL - 单一服务器中支持的版本。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b77fd082be43b8cbdedf7cbe5875a8931eb0474a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837889"
---
# <a name="supported-postgresql-database-versions"></a>支持的 PostgreSQL 数据库版本
Microsoft 旨在支持 Azure Database for PostgreSQL 单服务器中的 PostgreSQL 引擎的 n-2 版本。 这些版本将是 Azure 上的当前主版本 (n) 和之前的两个主版本 (-2)。

Azure Database for PostgreSQL 当前支持以下主要版本:

## <a name="postgresql-version-11"></a>PostgreSQL 版本11
当前次要版本为11.4。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/11/static/release-11-4.html)。

## <a name="postgresql-version-10"></a>PostgreSQL 版本10
当前次要版本为10.9。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/10/static/release-10-9.html)。

## <a name="postgresql-version-96"></a>PostgreSQL 版本9。6
当前次要版本为9.6.14。 若要详细了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/9.6/static/release-9-6-14.html)。

## <a name="postgresql-version-95"></a>PostgreSQL 版本9。5
当前次要版本为9.5.18。 若要了解此次要版本中的改进和修复，请参阅 [PostgreSQL 文档](https://www.postgresql.org/docs/9.5/static/release-9-5-18.html)。

## <a name="managing-upgrades"></a>管理升级
Azure Database for PostgreSQL 自动管理次版本升级。 

不支持自动主要版本升级。 例如, 没有从 PostgreSQL 9.5 到 PostgreSQL 9.6 的自动升级。 若要升级到下一个主要版本, 请创建[数据库转储并还原](./howto-migrate-using-dump-and-restore.md)到使用新引擎版本创建的服务器。

### <a name="version-syntax"></a>版本语法
在 PostgreSQL 版本10之前, [PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)将_主要版本_升级视为第一个_或_第二个数字的增长。 例如, 9.5 到9.6 被视为_主要_版本升级。 从版本10开始, 只有第一个数字的更改被视为主要版本升级。 例如, 10.0 到10.1 是_次要_版本升级。 版本10到11是_主要_版本升级。

## <a name="next-steps"></a>后续步骤
有关支持的 PostgreSQL 扩展的信息, 请参阅[扩展文档](concepts-extensions.md)。
