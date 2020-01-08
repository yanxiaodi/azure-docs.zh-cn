---
title: 复制 Azure SQL 数据库 | Microsoft Docs
description: 在相同或不同的服务器上创建现有 Azure SQL 数据库的事务一致性副本。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 09/04/2019
ms.openlocfilehash: de56e66046bb61ac31c1842ae6ce7a9c6720760d
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934203"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>复制 Azure SQL 数据库的事务一致性副本

通过 Azure SQL 数据库，可以以多种方式在相同或不同的服务器上创建现有 Azure SQL 数据库（[单一数据库](sql-database-single-database.md)）的事务一致性副本。 可以使用 Azure 门户、PowerShell 或 T-SQL 复制 SQL 数据库。 

## <a name="overview"></a>概述

数据库副本是源数据库截至复制请求发出时的快照。 你可以选择同一服务器或不同的服务器。 另外，你还可以选择保留其服务层级和计算大小，或在同一服务层级中使用不同的计算大小（版本）。 在完成该复制后，副本将成为能够完全行使功能的独立数据库。 此时，可以升级或降级到任意版本。 登录名、用户和权限可单独进行管理。 使用异地复制技术创建副本，一旦完成种子设定，就会自动终止异地复制链接。 使用异地复制的所有要求都适用于数据库复制操作。 有关详细信息，请参阅[活动异地复制概述](sql-database-active-geo-replication.md)。

> [!NOTE]
> 在创建数据库副本时，将用到[自动数据库备份](sql-database-automated-backups.md)。

## <a name="logins-in-the-database-copy"></a>数据库副本中的登录名

将某个数据库复制到同一 SQL 数据库服务器时，可以在这两个数据库上使用相同的登录名。 用于复制该数据库的安全主体将成为新数据库上的数据库所有者。 所有数据库用户、其权限及安全标识符 (SID) 都复制到数据库副本中。  

将数据库复制到不同的 SQL 数据库服务器时，新服务器上的安全主体将成为新数据库上的数据库所有者。 如果使用[包含的数据库用户](sql-database-manage-logins.md)进行数据访问，请确保主数据库和辅助数据库始终具有相同的用户凭据，这样在复制完成后，便可以使用相同的凭据立即访问。 

如果使用 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)，则完全无需管理副本中的凭据。 但是，将数据库复制到新服务器时，基于登录名的访问可能不起作用，因为登录名在新服务器上不存在。 要了解如何在将数据库复制到其他 SQL 数据库服务器时管理登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。 

复制成功之后，重新映射其他用户之前，只有启动复制的登录名，即数据库所有者，才能登录到新数据库。 若要在复制操作完成后解析登录名，请参阅[解析登录名](#resolve-logins)。

## <a name="copy-a-database-by-using-the-azure-portal"></a>使用 Azure 门户复制数据库

要使用 Azure 门户复制数据库，请打开数据库页，并单击“复制”。 

   ![数据库复制](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>使用 PowerShell 复制数据库

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要使用 PowerShell 复制数据库，请使用 [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet。 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

如需完整的示例脚本，请参阅[将数据库复制到新的服务器](scripts/sql-database-copy-database-to-new-server-powershell.md)。

数据库复制是一个异步操作，但在接受请求后会立即创建目标数据库。 如果需要在仍在进行中时取消复制操作，请使用[AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet 删除目标数据库。  

## <a name="rbac-roles-to-manage-database-copy"></a>用于管理数据库副本的 RBAC 角色

若要创建数据库副本，需要具有以下角色

- “订阅所有者”或
- SQL Server 参与者角色或
- 具有以下权限的源数据库和目标数据库上的自定义角色：

   Microsoft.Sql/servers/databases/read   
   Microsoft.Sql/servers/databases/write   

若要取消数据库副本，需要具有以下角色

- “订阅所有者”或
- SQL Server 参与者角色或
- 具有以下权限的源数据库和目标数据库上的自定义角色：

   Microsoft.Sql/servers/databases/read   
   Microsoft.Sql/servers/databases/write   
   
若要使用 Azure 门户管理数据库复制，还需要以下权限：

&nbsp;&nbsp; Microsoft.资源/&nbsp;订阅/资源/读取   
&nbsp;&nbsp; Microsoft.资源/&nbsp;订阅/资源/写入   
&nbsp;&nbsp; Microsoft.&nbsp;资源/部署/读取   
&nbsp;&nbsp; Microsoft.&nbsp;资源/部署/写入   
&nbsp;&nbsp; Microsoft.resources/&nbsp;部署/operationstatuses/读取    

若要在门户上的资源组中查看部署下的操作，请在多个资源提供程序（包括 SQL 操作）之间进行操作，你将需要以下附加的 RBAC 角色： 

&nbsp;&nbsp; Resourcegroups/&nbsp;部署/操作/读取   
&nbsp;&nbsp; Resourcegroups/&nbsp;部署/operationstatuses/读取



## <a name="copy-a-database-by-using-transact-sql"></a>使用 Transact-SQL 复制数据库

使用服务器级别主体登录名或创建了要复制的数据库的登录名登录到 master 数据库。 若要成功复制数据库，非服务器级主体的登录名必须是 dbmanager 角色的成员。 有关登录名和链接到服务器的详细信息，请参阅[管理登录名](sql-database-manage-logins.md)。

使用 [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) 语句开始复制源数据库。 执行此语句将启动数据库复制过程。 因为复制数据库是一个异步过程，所以，CREATE DATABASE 语句会在数据库完成复制前返回。

### <a name="copy-a-sql-database-to-the-same-server"></a>将 SQL 数据库复制到同一台服务器

使用服务器级别主体登录名或创建了要复制的数据库的登录名登录到 master 数据库。 若要成功复制数据库，非服务器级主体的登录名必须是 dbmanager 角色的成员。

此命令将 Database1 复制到同一服务器上名为 Database2 的新数据库。 根据数据库的大小，复制操作可能需要一些时间才能完成。

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>将 SQL 数据库复制到不同的服务器

登录到目标服务器（即要创建新数据库的 SQL 数据库服务器）的 master 数据库。 所用登录名的名称和密码应该与源 SQL 数据库服务器上源数据库的数据库所有者的名称和密码相同。 目标服务器上的登录名也必须是 dbmanager 角色的成员或服务器级主体登录名。

此命令将 server1 上的 Database1 复制到 server2 上名为 Database2 的新数据库。 根据数据库的大小，复制操作可能需要一些时间才能完成。

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> 必须将两台服务器的防火墙都配置为允许来自发出 T-SQL COPY 命令的客户端 IP 的入站连接。

### <a name="copy-a-sql-database-to-a-different-subscription"></a>将 SQL 数据库复制到不同的订阅

可以使用上一部分中描述的步骤将数据库复制到不同订阅中的 SQL 数据库服务器。 请确保你使用的登录名具有与源数据库的数据库所有者相同的名称和密码，并且它是 dbmanager 角色的成员或者是服务器级主体登录名。 

> [!NOTE]
> [Azure 门户](https://portal.azure.com)不支持复制到其他订阅，因为门户调用 ARM API，并且它使用订阅证书来访问异地复制中涉及的两台服务器。  

### <a name="monitor-the-progress-of-the-copying-operation"></a>监视复制操作的进度

通过查询 sys.databases 和 sys.dm_database_copies 视图来监视复制过程。 在复制过程中，新数据库的 sys.databases 视图的 **state_desc** 列将设置为 **COPYING**。

* 如果复制失败，新数据库的 sys.databases 视图的 **state_desc** 列将设置为 **SUSPECT**。 对新数据库执行 DROP 语句并稍后重试。
* 如果复制成功，新数据库的 sys.databases 视图的 **state_desc** 列将设置为 **ONLINE**。 复制已完成并且新数据库是一个常规数据库，可独立于源数据库进行更改。

> [!NOTE]
> 如果决定在复制过程中取消复制，请对新数据库执行 [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) 语句。 此外，对源数据库执行 DROP DATABASE 语句也将取消复制过程。

> [!IMPORTANT]
> 如果需要使用比源大得多的 SLO 创建副本，则目标数据库可能没有足够的资源来完成种子设定过程，并且可能会导致复制 operaion 失败。 在此方案中，使用异地还原请求在不同的服务器和/或不同的区域中创建副本。 有关详细信息，请参阅[使用数据库备份恢复 AZURE SQL 数据库](sql-database-recovery-using-backups.md#geo-restore)。


## <a name="resolve-logins"></a>解析登录名

当新数据库在目标服务器上联机后，使用 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 语句将新数据库中的用户重新映射到目标服务器上的登录名。 若要解析孤立用户，请参阅[孤立用户疑难解答](https://msdn.microsoft.com/library/ms175475.aspx)。 另请参阅[灾难恢复后如何管理 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。

新数据库中的所有用户都保持他们在源数据库中已有的权限。 启动数据库复制过程的用户将成为新数据库的数据库所有者，并且会为该用户分配一个新的安全标识符 (SID)。 复制成功之后，重新映射其他用户之前，只有启动复制的登录名，即数据库所有者，才能登录到新数据库。

要了解如何在将数据库复制到其他 SQL 数据库服务器时管理用户和登录名，请参阅[灾难恢复后如何管理 Azure SQL 数据库的安全性](sql-database-geo-replication-security-config.md)。

## <a name="next-steps"></a>后续步骤

* 有关登录名的信息，请参阅[管理登录名](sql-database-manage-logins.md)和[灾难恢复后如何管理 Azure SQL 数据库安全性](sql-database-geo-replication-security-config.md)。
* 要导出数据库，请参阅[将数据库导出到 BACPAC](sql-database-export.md)。
