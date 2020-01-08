---
title: Azure Active Directory 身份验证 - Azure SQL | Microsoft 文档
description: 了解如何将 Azure Active Directory 身份验证与 SQL 数据库、托管实例和 SQL 数据仓库结合使用
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 02/20/2019
ms.openlocfilehash: 848cfc96a7da4e69ff77d16a42226a983153ac63
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897005"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql"></a>使用 Azure Active Directory 身份验证进行 SQL 身份验证

Azure Active Directory 身份验证是使用 Azure Active Directory (Azure AD) 中的标识连接到 Azure [SQL 数据库](sql-database-technical-overview.md)、[托管实例](sql-database-managed-instance.md)和 [SQL 数据仓库](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)的一种机制。 

> [!NOTE]
> 本主题适用于 Azure SQL 服务器，同时也适用于在 Azure SQL 服务器中创建的 SQL 数据库和 SQL 数据仓库数据库。 为简单起见，在提到 SQL 数据库和 SQL 数据仓库时，本文统称 SQL 数据库。

通过 Azure AD 身份验证，可以在一个中心位置中集中管理数据库用户和其他 Microsoft 服务的标识。 集中 ID 管理提供一个单一位置来管理数据库用户，并简化权限管理。 包括如下优点：

- 提供一个 SQL Server 身份验证的替代方法。
- 帮助阻止用户标识在数据库服务器之间激增。
- 允许在单一位置中轮换密码。
- 客户可以使用外部 (Azure AD) 组管理数据库权限。
- 它可以通过启用集成的 Windows 身份验证和 Azure Active Directory 支持的其他形式的身份验证来消除存储密码。
- Azure AD 身份验证使用包含的数据库用户以数据库级别对标识进行身份验证。
- Azure AD 支持对连接到 SQL 数据库的应用程序进行基于令牌的身份验证。
- Azure AD 身份验证支持对本地 Azure Active Directory 进行 ADFS（域联合）或本机用户/密码身份验证，无需进行域同步。
- Azure AD 支持从 SQL Server Management Studio 进行连接，后者使用 Active Directory 通用身份验证，其中包括多重身份验证 (MFA)。  MFA 包括利用一系列简单的验证选项进行的强身份验证，这些选项包括电话、短信、含有 PIN 码的智能卡或移动应用通知。 有关详细信息，请参阅 [SQL 数据库和 SQL 数据仓库针对 Azure AD MFA 的 SSMS 支持](sql-database-ssms-mfa-authentication.md)。
- Azure AD 支持来自 SQL Server Data Tools (SSDT) 的使用 Active Directory 交互式身份验证的类似连接。 有关详细信息，请参阅 [SQL Server Data Tools (SSDT) 中的 Azure Active Directory 支持](/sql/ssdt/azure-active-directory)。

> [!NOTE]  
> 不支持使用 Azure Active Directory 帐户连接到 Azure VM 上运行的 SQL Server。 请改用域 Active Directory 帐户。  

配置步骤包括配置和使用 Azure Active Directory 身份验证的以下过程。

1. 创建并填充 Azure AD。
2. 可选：关联或更改当前与 Azure 订阅关联的 Active Directory。
3. 为 Azure SQL 数据库服务器、托管实例或 [Azure SQL 数据仓库](https://azure.microsoft.com/services/sql-data-warehouse/)创建 Azure Active Directory 管理员。
4. 配置客户端计算机。
5. 在映射到 Azure AD 标识的数据库中创建包含的数据库用户。
6. 通过使用 Azure AD 标识连接到数据库。

> [!NOTE]
> 要了解如何创建和填充 Azure AD，并使用 Azure SQL 数据库、托管实例和 SQL 数据仓库配置 Azure AD，请参阅[使用 Azure SQL 数据库配置 Azure AD](sql-database-aad-authentication-configure.md)。

## <a name="trust-architecture"></a>信任体系结构

以下高级别关系图概述了将 Azure AD 身份验证用于 Azure SQL 数据库的解决方案体系结构。 相同的概念适用于 SQL 数据仓库。 若要支持 Azure AD 本机用户密码，只需考虑云部分和 Azure AD/Azure SQL 数据库。 若要支持联合身份验证（或 Windows 凭据的用户/密码），需要与 ADFS 块进行通信。 箭头表示通信路径。

![AAD 身份验证关系图][1]

下图表明允许客户端通过提交令牌连接到数据库的联合、信任和托管关系。 该令牌已由 Azure AD 进行身份验证且受数据库信任。 客户 1 可以代表具有本机用户的 Azure Active Directory 或具有联合用户的 Azure AD。 客户 2 代表包含已导入用户的可行解决方案；在本例中，来自联合 Azure Active Directory 且 ADFS 正与 Azure Active Directory 进行同步。 请务必了解，使用 Azure AD 身份验证访问数据库需要托管订阅与 Azure AD 相关联。 必须使用相同的订阅创建托管 Azure SQL 数据库或 SQL 数据仓库的 SQL Server。

![订阅关系][2]

## <a name="administrator-structure"></a>管理员结构

使用 Azure AD 身份验证时，SQL 数据库服务器和托管实例会有两个管理员帐户：原始的 SQL Server 管理员和 Azure AD 管理员。 相同的概念适用于 SQL 数据仓库。 只有基于 Azure AD 帐户的管理员可以在用户数据库中创建第一个 Azure AD 包含的数据库用户。 Azure AD 管理员登录名可以是 Azure AD 用户，也可以是 Azure AD 组。 当管理员为组帐户时，可以由任何组成员使用，为 SQL Server 实例启用多个 Azure AD 管理员。 以管理员身份使用组帐户时，允许无需更改 SQL 数据库中的用户或权限，便可集中添加和删除 Azure AD 中的组成员，从而提高可管理性。 无论何时都仅可配置一个 Azure AD 管理员（一个用户或组）。

![管理结构][3]

## <a name="permissions"></a>权限

若要新建用户，必须具有数据库中的 `ALTER ANY USER` 权限。 `ALTER ANY USER` 权限可以授予任何数据库用户。 `ALTER ANY USER` 权限还由服务器管理员帐户、具有该数据库的 `CONTROL ON DATABASE` 或 `ALTER ON DATABASE` 权限的数据库用户以及 `db_owner` 数据库角色的成员拥有。

若要在 Azure SQL 数据库、托管实例或 SQL 数据仓库中创建一个包含的数据库用户，必须使用 Azure AD 标识连接到数据库或实例。 若要创建第一个包含数据库用户，必须通过使用 Azure AD 管理员（其是数据库的所有者）连接到数据库。 有关详细信息，请参阅[使用 SQL 数据库或 SQL 数据仓库配置和管理 Azure Active Directory 身份验证](sql-database-aad-authentication-configure.md)。 只有为 Azure SQL 数据库或 SQL 数据仓库服务器创建 Azure AD 管理员之后，才有可能进行任何 Azure AD 身份验证。 如果已从服务器删除 Azure Active Directory 管理员，先前在 SQL Server 内创建的现有 Azure Active Directory 用户便无法再使用其 Azure Active Directory 凭据连接到数据库。

## <a name="azure-ad-features-and-limitations"></a>Azure AD 功能和限制

- 可以在 Azure SQL Server 或 SQL 数据仓库中预配以下 Azure AD 成员：

  - 本机成员：在托管域或客户域中的 Azure AD 中创建的成员。 有关详细信息，请参阅[将自己的域名添加到 Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)。
  - 联合域成员：在联合域的 Azure AD 中创建的成员。 有关详细信息，请参阅 [Microsoft Azure 现在支持与 Windows Server Active Directory 联合](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)。
  - 作为本机或联合域成员从其他 Azure AD 导入的成员。
  - 以安全组形式创建的 Active Directory 组。

- 属于具有 `db_owner` 服务器角色的组的 Azure AD 用户无法对 Azure SQL 数据库和 Azure SQL 数据仓库使用 **[CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** 语法。 将出现以下错误：

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    请直接将 `db_owner` 角色授予个体 Azure AD 用户以缓解 **CREATE DATABASE SCOPED CREDENTIAL** 问题。

- 这些系统函数在 Azure AD 主体下执行时，返回 NULL 值：

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="managed-instances"></a>托管实例

- Azure AD 服务器主体（登录名）和用户作为[托管实例](sql-database-managed-instance.md)的预览功能受支持。
- [托管实例](sql-database-managed-instance.md)中不支持将映射到 Azure AD 组的 Azure AD 服务器主体（登录名）设置为数据库所有者。
    - 此情况的扩展是当将组添加为 `dbcreator` 服务器角色的一部分时，此组中的用户可以连接到托管实例并创建新数据库，但将不能访问数据库。 这是因为新的数据库所有者是 SA，而不是 Azure AD 用户。 如果将个体用户添加到 `dbcreator` 服务器角色，则不会出现此问题。
- Azure AD 服务器主体（登录名）支持 SQL 代理管理和作业执行。
- Azure AD 服务器主体（登录名）可以执行数据库备份和还原操作。
- 支持审核与 Azure AD 服务器主体（登录名）和身份验证事件相关的所有语句。
- 支持为属于 sysadmin 服务器角色成员的 Azure AD 服务器主体（登录名）建立专用管理员连接。
    - 可以通过 SQLCMD 实用工具和 SQL Server Management Studio 提供此支持。
- 来自 Azure AD 服务器主体（登录名）的登录事件支持登录触发器。
- 可以使用 Azure AD 服务器主体（登录名）设置 Service Broker 和数据库邮件。


## <a name="connecting-using-azure-ad-identities"></a>使用 Azure AD 标识进行连接

Azure Active Directory 身份验证支持使用 Azure AD 标识连接到数据库的以下方法：

- Azure Active Directory 密码
- 集成式 Azure Active Directory
- 采用了 MFA 的通用 Azure Active Directory
- 使用应用程序令牌身份验证

Azure AD 服务器主体（登录名）（**公共预览版**）支持以下身份验证方法：

- Azure Active Directory 密码
- 集成式 Azure Active Directory
- 采用了 MFA 的通用 Azure Active Directory


### <a name="additional-considerations"></a>其他注意事项

- 为了增强可管理性，建议将一个专用 Azure AD 组预配为管理员。   
- 无论何时都仅可为 Azure SQL 数据库服务器或 Azure SQL 数据仓库配置一个 Azure AD 管理员（一个用户或组）。
  - 为托管实例添加 Azure AD 服务器主体（登录名）（**公共预览版**）允许创建可以添加到 `sysadmin` 角色的多个 Azure AD 服务器主体（登录名）。
- 只有 SQL Server 的 Azure AD 管理员最初可以使用 Azure Active Directory 帐户连接到 Azure SQL 数据库服务器、托管实例或 Azure SQL 数据仓库。 Active Directory 管理员可以配置后续的 Azure AD 数据库用户。   
- 我们建议将连接超时值设置为 30 秒。   
- SQL Server 2016 Management Studio 和 SQL Server Data Tools for Visual Studio 2015（版本 14.0.60311.1（2016 年 4 月）或更高版本）支持 Azure Active Directory 身份验证。 （**用于 SqlServer 的 .NET Framework 数据提供程序**（.NET Framework 4.6 或更高版本）支持 Azure AD 身份验证）。 因此，这些工具和数据层应用程序（DAC 和 .BACPAC）的最新版本可以使用 Azure AD 身份验证。   
- 从版本 15.0.1 开始，[sqlcmd 实用工具](/sql/tools/sqlcmd-utility)和 [bcp 实用工具](/sql/tools/bcp-utility)支持采用了 MFA 的 Active Directory 交互式身份验证。
- SQL Server Data Tools for Visual Studio 2015 至少需要 2016 年 4 月版的 Data Tools（版本 14.0.60311.1）。 目前，Azure AD 用户不会显示在 SSDT 对象资源管理器中。 解决方法是在 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx) 中查看这些用户。   
- [Microsoft JDBC Driver 6.0 for SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) 支持 Azure AD 身份验证。 另外，请参阅[设置连接属性](https://msdn.microsoft.com/library/ms378988.aspx)。   
- PolyBase 无法使用 Azure AD 身份验证进行身份验证。   
- Azure 门户的“导入数据库”和“导出数据库”边栏选项卡支持 SQL 数据库的 Azure AD 身份验证。 PowerShell 命令也支持使用 Azure AD 身份验证的导入和导出。   
- SQL 数据库、托管实例和 SQL 数据仓库通过 CLI 支持 Azure AD 身份验证。 有关详细信息，请参阅[使用 SQL 数据库或 SQL 数据仓库配置和管理 Azure Active Directory 身份验证](sql-database-aad-authentication-configure.md)和 [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server)。

## <a name="next-steps"></a>后续步骤

- 若要了解如何创建和填充 Azure AD，以及如何使用 Azure SQL 数据库或 Azure SQL 数据仓库配置 Azure AD，请参阅[使用 SQL 数据库、托管实例或 SQL 数据仓库配置和管理 Azure Active Directory 身份验证](sql-database-aad-authentication-configure.md)。
- 有关将 Azure AD 服务器主体（登录名）与托管实例配合使用的教程，请参阅 [Azure AD 服务器主体（登录名）与托管实例](sql-database-managed-instance-aad-security-tutorial.md)
- 有关 SQL 数据库中的访问和控制的概述，请参阅 [SQL 数据库访问和控制](sql-database-control-access.md)。
- 有关 SQL 数据库中的登录名、用户和数据库角色的概述，请参阅[登录名、用户和数据库角色](sql-database-manage-logins.md)。
- 有关数据库主体的详细信息，请参阅[主体](https://msdn.microsoft.com/library/ms181127.aspx)。
- 有关数据库角色的详细信息，请参阅[数据库角色](https://msdn.microsoft.com/library/ms189121.aspx)。
- 有关为托管实例创建 Azure AD 服务器主体（登录名）的语法，请参阅 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)。
- 有关 SQL 数据库中的防火墙规则的详细信息，请参阅 [SQL 数据库防火墙规则](sql-database-firewall-configure.md)。

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png
