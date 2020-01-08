---
title: Azure Database for PostgreSQL-单个服务器中的限制
description: 本文介绍用于 PostgreSQL 的单个服务器，例如连接数和存储引擎选项的 Azure 数据库中的限制。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: e4752112acf136d9ffb19a0b7383bc3aff5de5e0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448088"
---
# <a name="limits-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL-单个服务器中的限制
下列各部分介绍数据库服务中的容量和功能限制。 如果你想要了解有关资源 （计算、 内存、 存储） 层的信息，请参阅[定价层](concepts-pricing-tiers.md)一文。


## <a name="maximum-connections"></a>最大连接数
每个定价层的最大连接数和 vCore 数如下所示： 

|**定价层**| **vCore(s)**| 最大连接数  |
|---|---|---|
|基本| 第| 50 |
|基本| 2| 100 |
|常规用途| 2| 150|
|常规用途| 4| 250|
|常规用途| 8| 480|
|常规用途| 16| 950|
|常规用途| 32| 1500|
|常规用途| 64| 1900|
|内存优化| 2| 300|
|内存优化| 4| 500|
|内存优化| 8| 960|
|内存优化| 16| 1900|
|内存优化| 32| 1987|

当连接数超出限制时，可能会收到以下错误：
> 严重：很抱歉，客户端数过多

Azure 系统需要使用五个连接来监视 Azure Database for PostgreSQL 服务器。 

## <a name="functional-limitations"></a>功能限制
### <a name="scale-operations"></a>缩放操作
- 目前不支持向/从基本定价层动态缩放。
- 目前不支持减小服务器存储大小。

### <a name="server-version-upgrades"></a>服务器版本升级
- 目前不支持在主要数据库引擎版本之间进行自动迁移。 如果要升级到下一主要版本，请将其[转储和还原](./howto-migrate-using-dump-and-restore.md)到使用新引擎版本创建的服务器。

> 请注意，在 PostgreSQL 版本 10、 之前[PostgreSQL 版本控制策略](https://www.postgresql.org/support/versioning/)被视为_主版本_升级为增加的第一个_或_数目 （对于第二个示例中，被视为 9.5 到 9.6_主要_版本升级)。
> 从版本 10 中的第一个数字的唯一更改被视为主版本升级 (例如，是 10.0 到 10.1_次要_版本升级和 10 到 11_主要_版本升级)。

### <a name="vnet-service-endpoints"></a>VNet 服务终结点
- 只有常规用途和内存优化服务器才支持 VNet 服务终结点。

### <a name="restoring-a-server"></a>还原服务器
- 使用 PITR 功能时，将使用与新服务器所基于的服务器相同的定价层配置创建新服务器。
- 还原期间创建的新服务器没有原始服务器上存在的防火墙规则。 需要为此新服务器单独设置防火墙规则。
- 不支持还原已删除的服务器。

### <a name="utf-8-characters-on-windows"></a>Windows 上的 UTF-8 字符
- 在某些情况下，Windows 上的开源 PostgreSQL 不完全支持 UTF-8 字符，这会影响 Azure Database for PostgreSQL。 有关详细信息，请参阅 [postgresql-archive 中的 Bug #15476](https://www.postgresql-archive.org/BUG-15476-Problem-on-show-trgm-with-4-byte-UTF-8-characters-td6056677.html) 上的话题。

## <a name="next-steps"></a>后续步骤
- 了解[每个定价层中有哪些可用资源](concepts-pricing-tiers.md)
- 了解[支持的 PostgreSQL 数据库版本](concepts-supported-versions.md)
- 查看[如何使用 Azure 门户在 Azure Database for PostgreSQL 中备份和还原服务器](howto-restore-server-portal.md)
