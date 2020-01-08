---
title: Azure SQL 数据库和数据仓库的透明数据加密 | Microsoft Docs
description: SQL 数据库和数据仓库的透明数据加密概述。 本文档介绍服务托管的透明数据加密和“创建自己的密钥”的优势与配置选项。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 9261bae0d2bee990a5048cb87a863d96e1854d00
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061939"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>SQL 数据库和数据仓库的透明数据加密

透明数据加密 (TDE) 通过加密静态数据, 帮助保护 Azure SQL 数据库、Azure SQL 托管实例和 Azure 数据仓库免受恶意脱机活动的威胁。 它可执行静态数据库、关联备份和事务日志文件的实时加密和解密，无需更改应用程序。 默认情况下，为所有新部署的 Azure SQL 数据库启用了 TDE。 不能使用 TDE 来加密 SQL 数据库中的逻辑 **master** 数据库。  **master** 数据库包含对用户数据库执行 TDE 操作时所需的对象。

需要为早期版本的 Azure SQL 数据库、Azure SQL 托管实例或 Azure SQL 数据仓库手动启用 TDE。
通过还原创建托管实例数据库将从源数据库继承加密状态。

透明数据加密使用称为数据库加密密钥的对称密钥来加密整个数据库的存储。 此数据库加密密钥受透明数据加密保护器的保护。 保护器是服务托管的证书（服务托管的透明数据加密）或存储在 Azure Key Vault 中的非对称密钥（“创建自己的密钥”）。 在 Azure SQL 数据库和数据仓库的服务器级别以及 Azure SQL 托管实例的实例级别设置透明数据加密保护程序。 除非另有说明，否则术语“服务器”指的是整个文档中的服务器和实例。

数据库启动时，加密的数据库加密密钥将会解密，然后在 SQL Server 数据库引擎进程中用于解密和重新加密数据库文件。 透明数据加密在页面级别对数据执行实时 I/O 加密和解密。 将每个页面读入内存时会将其解密，在写入磁盘之前会将其加密。 有关透明数据加密的一般说明，请参阅[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)。

在 Azure 虚拟机上运行的 SQL Server 也可以使用 Key Vault 中的非对称密钥。 配置步骤与在 SQL 数据库和 SQL 托管实例中使用非对称密钥不同。 有关详细信息，请参阅[使用 Azure Key Vault 进行可扩展的密钥管理 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)。

## <a name="service-managed-transparent-data-encryption"></a>服务托管的透明数据加密

在 Azure 中，透明数据加密的默认设置是通过内置的服务器证书保护数据库加密密钥。 内置服务器证书对每个服务器都是唯一的, 使用的加密算法是 AES 256。 如果某个数据库存在异地复制关系，则主数据库和异地辅助数据库将受主数据库的父服务器密钥的保护。 如果两个数据库连接到同一个服务器，则它们也共享相同的内置证书。  Microsoft 根据内部安全策略自动轮换这些证书，根密钥由 Microsoft 内部密码存储保护。  客户可在[Microsoft 信任中心](https://servicetrust.microsoft.com/)上的独立第三方审核报告中验证 SQL 数据库与内部安全策略的符合性。

Microsoft 还可按需无缝移动和管理密钥，以实现异地复制和还原。

> [!IMPORTANT]
> 默认情况下, 使用服务托管的透明数据加密对所有新创建的 SQL 数据库和托管实例的数据库进行加密。 默认情况下, 不会加密在2017年5月之前创建的现有 SQL 数据库和通过还原、异地复制和数据库副本创建的 SQL 数据库。 默认情况下, 不会加密在2019年2月之前创建的现有托管实例数据库。 通过还原创建托管实例数据库将从源继承加密状态。

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>客户管理的透明数据加密 - 创建自己的密钥

[使用 Azure Key Vault 中由客户管理的密钥进行 TDE](transparent-data-encryption-byok-azure-sql.md) 允许使用由客户管理的非对称密钥（称为 TDE 保护器）对数据库加密密钥 (DEK) 进行加密。  这通常也称为透明数据加密的创建自己的密匙 (BYOK) 支持。 在 BYOK 方案中，TDE 保护器存储在由客户拥有和管理的 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)（Azure 的基于云的外部密钥管理系统）中。 TDE 保护器可[由 Key Vault 生成，也可以从本地 HSM 设备转移到 Key Vault](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)。 TDE DEK 存储在数据库的启动页上，由 TDE 保护器进行加密和解密，该保护器存储在 Azure Key Vault 中并且从不会离开密钥保管库。  需要向 SQL 数据库授予对客户管理的密钥保管库的权限才能对 DEK 进行解密和加密。 如果撤销了逻辑 SQL Server 对密钥保管库的权限，则数据库将无法访问，并且所有数据都是加密的。 对于 Azure SQL 数据库，TDE 保护器是在逻辑 SQL Server 级别设置的，并由该服务器关联的所有数据库继承。 对于[AZURE SQL 托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)(预览版中的 BYOK 功能), TDE 保护程序在实例级别设置, 并由该实例上的所有*加密*数据库继承。 除非另有说明，否则术语“服务器”在整个文档中指的是服务器和实例。

使用集成了 Azure Key Vault 的 TDE，用户可以控制密钥管理任务，包括密钥轮换、密钥保管库权限、密钥备份，以及使用 Azure Key Vault 功能对所有 TDE 保护器启用审核/报告。 Key Vault 提供了集中化密钥管理功能，利用严格监控的硬件安全模块 (HSM)，并可在密钥与数据管理之间实现职责分离，以帮助满足安全策略的要求。
若要详细了解 Azure SQL 数据库的透明数据创建自己的密钥 Azure Key Vault 加密、SQL 托管实例 (预览版中的 BYOK 功能) 和数据仓库, 请参阅[透明数据加密和 Azure Key Vault集成](transparent-data-encryption-byok-azure-sql.md)。

若要开始使用集成了 Azure Key Vault 的透明数据加密（支持“创建自己的密钥”），请参阅操作指南[通过 PowerShell 使用 Key Vault 中的自有密钥启用透明数据加密](transparent-data-encryption-byok-azure-sql-configure.md)。

## <a name="move-a-transparent-data-encryption-protected-database"></a>移动受透明数据加密保护的数据库

对于 Azure 中的操作，不需要解密数据库。 源数据库或主数据库上的透明数据加密设置将以透明方式继承到目标。 包括的操作涉及到：

- 异地还原
- 自助时间点还原
- 还原已删除的数据库
- 活动异地复制
- 创建数据库副本
- 将备份文件还原到 Azure SQL 托管实例

> [!IMPORTANT]
> 由于无法访问用于加密的证书，因此不允许在 Azure SQL 托管实例中对由服务托管的 TDE 加密的数据库手动执行仅复制备份。 使用时间点还原功能将此类型的数据库移到另一个托管实例。

导出受透明数据加密保护的数据库时，不会加密数据库的导出内容。 此导出内容存储在未加密的 BACPAC 文件中。 请务必适当保护 BACPAC 文件，并在完成新数据库导入后启用透明数据加密。

例如，如果从本地 SQL Server 实例导出 BACPAC 文件，则新数据库的导入内容不会自动加密。 同样，如果将 BACPAC 文件导出到本地 SQL Server 实例，则新数据库也不会自动加密。

向/从 SQL 数据库导出时例外。 新数据库中已启用透明数据加密，但 BACPAC 文件本身不会加密。

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>在 Azure 门户中管理透明数据加密

若要通过 Azure 门户配置透明数据加密，必须以 Azure 所有者、参与者或 SQL 安全管理员的身份进行连接。

在数据库级别启用和禁用透明数据加密。 若要在数据库中启用透明数据加密，请转到 [Azure 门户](https://portal.azure.com)，并使用 Azure 管理员或参与者帐户登录。 在用户数据库下找到透明数据加密设置。 默认会使用服务托管的透明数据加密。 将为包含数据库的服务器自动生成透明数据加密证书。 对于 Azure SQL 托管实例，使用 T-SQL 在数据库上启用和禁用透明数据加密。

![服务托管的透明数据加密](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

在服务器级别设置透明数据加密主密钥（也称为透明数据加密保护器）。 要使用带“创建自己的密钥”支持的透明数据加密，以及使用 Key Vault 中的密钥来保护数据库，请打开服务器下的透明数据加密设置。

![支持“创建自己的密钥”的透明数据加密](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>使用 PowerShell 管理透明数据加密

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

若要通过 PowerShell 配置透明数据加密，必须以 Azure 所有者、参与者或 SQL 安全管理员的身份进行连接。

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>适用于 Azure SQL 数据库和数据仓库的 Cmdlet

对 Azure SQL 数据库和数据仓库使用以下 cmdlet：

| Cmdlet | 描述 |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |为数据库启用或禁用透明数据加密|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |获取数据库的透明数据加密状态 |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |检查数据库的加密进度 |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |将 Key Vault 密钥添加到 SQL Server 实例 |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |获取 Azure SQL 数据库服务器的 Key Vault 密钥  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |设置 SQL Server 实例的透明数据加密保护器 |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |获取透明数据加密保护器 |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |从 SQL Server 实例中删除 Key Vault 密钥 |
|  | |

> [!IMPORTANT]
> 对于 Azure SQL 托管实例，请使用 T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) 命令在数据库级别上启用和禁用透明数据加密，并检查[示例 PowerShell 脚本](transparent-data-encryption-byok-azure-sql-configure.md)以管理实例级别的透明数据加密。

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>使用 Transact-SQL 管理透明数据加密

使用 master 数据库中充当管理员或 **dbmanager** 角色成员的登录名连接到数据库。

| Command | 描述 |
| --- | --- |
| [ALTER DATABASE（Azure SQL 数据库）](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF 会加密或解密数据库 |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |返回有关数据库的加密状态及其关联的数据库加密密钥的信息 |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |返回有关每个数据仓库节点的加密状态及其关联的数据库加密密钥的信息 |
|  | |

无法使用 Transact-SQL 将透明数据加密保护器切换为 Key Vault 中的密钥。 请使用 PowerShell 或 Azure 门户。

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>使用 REST API 管理透明数据加密

若要通过 REST API 配置透明数据加密，必须以 Azure 所有者、参与者或 SQL 安全管理员的身份进行连接。
对 Azure SQL 数据库和数据仓库使用以下命令集：

| Command | 描述 |
| --- | --- |
|[创建或更新服务器](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|将 Azure Active Directory 标识添加到 SQL Server 实例（用于授予 Key Vault 的访问权限）|
|[创建或更新服务器密钥](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|将 Key Vault 密钥添加到 SQL Server 实例|
|[删除服务器密钥](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|从 SQL Server 实例中删除 Key Vault 密钥|
|[获取服务器密钥](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|从 SQL Server 实例中获取特定的 Key Vault 密钥|
|[按服务器列出服务器密钥](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|获取 SQL Server 实例的 Key Vault 密钥 |
|[创建或更新加密保护器](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|设置 SQL Server 实例的透明数据加密保护器|
|[获取加密保护器](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|获取 SQL Server 实例的透明数据加密保护器|
|[按服务器列出加密保护器](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|获取 SQL Server 实例的透明数据加密保护器 |
|[创建或更新透明数据加密配置](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|为数据库启用或禁用透明数据加密|
|[获取透明数据加密配置](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|获取数据库的透明数据加密配置|
|[列出透明数据加密配置结果](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|获取数据库的加密结果|

## <a name="next-steps"></a>后续步骤

- 有关透明数据加密的一般说明，请参阅[透明数据加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)。
- 要详细了解 Azure SQL 数据库、Azure SQL 托管实例和数据仓库的带有“创建自己的密钥”支持的透明数据加密，请参阅[带有“创建自己的密钥”支持的透明数据加密](transparent-data-encryption-byok-azure-sql.md)。
- 若要开始使用支持“创建自己的密钥”的透明数据加密，请参阅操作方法指南[通过 PowerShell 使用 Key Vault 中的自有密钥启用透明数据加密](transparent-data-encryption-byok-azure-sql-configure.md)。
- 有关 Key Vault 的详细信息，请参阅 [Key Vault 文档页](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)。
