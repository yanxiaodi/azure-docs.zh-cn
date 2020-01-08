---
title: 管理弹性数据库客户端库中的凭据 | Microsoft 文档
description: 如何为弹性数据库应用设置正确的凭据级别（从管理员到只读权限）
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: d89e83092775828016c2c47a96164319f5474c1e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568424"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>用于访问弹性数据库客户端库的凭据

[弹性数据库客户端库](sql-database-elastic-database-client-library.md)使用三种不同的凭据来访问[分片映射管理器](sql-database-elastic-scale-shard-map-management.md)。 使用凭据时，应根据需要尽可能采用最低访问级别。

* **管理凭据**：用于创建或操作分片映射管理器。 （请参阅[词汇表](sql-database-elastic-scale-glossary.md)。）
* **访问凭据**：用于访问现有分片映射管理器以获取有关分片的信息。
* **连接凭据**：用于连接到分片。

另请参阅[管理 Azure SQL 数据库的数据库和登录名](sql-database-manage-logins.md)。

## <a name="about-management-credentials"></a>关于管理凭据

使用管理凭据可以针对操作分片映射的应用程序创建 **ShardMapManager**（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)）对象。 （有关示例，请参阅[使用弹性数据库工具添加分片](sql-database-elastic-scale-add-a-shard.md)和[数据相关路由](sql-database-elastic-scale-data-dependent-routing.md)）。 弹性缩放客户端库的用户创建 SQL 用户和 SQL 登录名，并确保授予每个 SQL 用户/登录名对全局分片映射数据库以及所有分片数据库的读/写权限。 对分片映射执行更改时，这些凭据用于维护全局分片映射和局部分片映射。 例如，使用管理凭据创建分片映射管理器对象（使用 **GetSqlShardMapManager**（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)））：

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

变量 **smmAdminConnectionString** 是包含管理凭据的连接字符串。 用户 ID 和密码提供对分片映射数据库和单个分片的读/写访问权限。 管理连接字符串还包括服务器名称和数据库名称，以标识全局分片映射数据库。 下面是用于实现此目的的典型连接字符串：

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

请勿使用 username@server 格式的值 - 只需使用“username”格式的值。  这是因为凭据必须同时适用于分片映射管理器数据库和各个分片，而它们可能位于不同的服务器上。

## <a name="access-credentials"></a>访问凭据

在不用于管理分片映射的应用程序中创建分片映射管理器时，使用在全局分片映射上具有只读权限的凭据。 在这些凭据下从全局分片映射检索到的信息可用于[数据相关路由](sql-database-elastic-scale-data-dependent-routing.md)，以及用于填充客户端上的分片映射缓存。 通过与 **GetSqlShardMapManager** 相同的调用模式提供凭据：

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

记下 **smmReadOnlyConnectionString** 的使用，以代表**非管理员**用户反映用于此访问的其他凭据的使用：这些凭据不应在全局分片映射上提供写入权限。

## <a name="connection-credentials"></a>连接凭据

使用 **OpenConnectionForKey**（[Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey)、[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)）方法访问与某个分片键相关联的分片时，还需使用其他凭据。 这些凭据需要提供对驻留在该分片上的局部分片映射表的只读访问权限。 若要对分片上的数据依赖路由执行连接验证，需要执行此操作。 此代码片段允许在使用数据相关路由的情况下进行数据访问：

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

在本示例中，**smmUserConnectionString** 包含用户凭据的连接字符串。 对于 Azure SQL DB，下面是用户凭据的典型连接字符串：

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

与管理员凭据一样，请不要使用“username@server”格式的值， 而应使用“用户名”格式的值。  另请注意，连接字符串不包含服务器名称和数据库名称。 这是因为，**OpenConnectionForKey** 调用会自动根据键将连接定向到正确的分片。 因此，不需提供数据库名称和服务器名称。

## <a name="see-also"></a>请参阅

[在 Azure SQL 数据库中管理数据库和登录名](sql-database-manage-logins.md)

[保护 SQL 数据库](sql-database-security-overview.md)

[弹性数据库作业](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
