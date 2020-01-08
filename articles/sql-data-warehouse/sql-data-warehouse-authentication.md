---
title: 向 Azure SQL 数据仓库进行身份验证 | Microsoft Docs
description: 了解如何使用 Azure Active Directory (AAD) 或 SQL Server 身份验证向 Azure SQL 数据仓库进行身份验证。
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a3bed9df5b62ce7f2f3df7046357dc4f2458575c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61475025"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>向 Azure SQL 数据仓库进行身份验证
了解如何使用 Azure Active Directory (AAD) 或 SQL Server 身份验证向 Azure SQL 数据仓库进行身份验证。

若要连接到 SQL 数据仓库，必须传入安全凭据进行身份验证。 建立连接时，特定的连接设置已配置为建立查询会话的一部分。  

若要深入了解安全性以及如何启用与数据仓库的连接，请参阅[保护 SQL 数据仓库中的数据库][Secure a database in SQL Data Warehouse]。

## <a name="sql-authentication"></a>SQL 身份验证
若要连接到 SQL 数据仓库，必须提供以下信息：

* 完全限定的服务器名称
* 指定 SQL 身份验证
* 用户名
* 密码
* 默认数据库（可选）

默认情况下，连接连接到 master  数据库而不是用户数据库。 若要连接到用户数据库，可以选择执行以下两项操作之一：

* 在 SSDT、SSMS 或应用程序连接字符串中将服务器注册到 SQL Server 对象资源管理器时指定默认数据库。 例如，包含 ODBC 连接的 InitialCatalog 参数。
* 在 SSDT 中创建会话之前先突出显示用户数据库。

> [!NOTE]
> 不支持使用 Transact-SQL 语句 **USE MyDatabase;** 更改连接的数据库。 有关使用 SSDT 连接到 SQL 数据仓库的指南，请参阅[使用 Visual Studio 进行查询][Query with Visual Studio]一文。
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) 身份验证
[Azure Active Directory][What is Azure Active Directory] 身份验证是使用 Azure Active Directory (Azure AD) 中的标识连接到 Microsoft Azure SQL 数据仓库的一种机制。 通过 Azure Active Directory 身份验证，可以在一个中心位置中集中管理数据库用户和其他 Microsoft 服务的标识。 集中 ID 管理提供单一位置用于管理 SQL 数据仓库用户，并简化权限管理。 

### <a name="benefits"></a>优点
Azure Active Directory 的优点包括：

* 提供一个 SQL Server 身份验证的替代方法。
* 帮助阻止用户标识在数据库服务器之间激增。
* 允许在单一位置中轮换密码
* 使用外部 (AAD) 组管理数据库权限。
* 通过启用集成的 Windows 身份验证和 Azure Active Directory 支持的其他形式的身份验证来消除存储密码。
* 使用包含的数据库用户在数据库级别对标识进行身份验证。
* 支持对连接到 SQL 数据仓库的应用程序进行基于令牌的身份验证。
* 通过 Active Directory 通用身份验证的各种工具包括支持多重身份验证[SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md)并[SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json)。

> [!NOTE]
> Azure Active Directory 仍然相对较新，具有某些限制。 若要确保 Azure Active Directory 适用于当前环境，请参阅 [Azure AD 功能和限制][Azure AD features and limitations]，尤其是那些需要额外考虑的内容。
> 
> 

### <a name="configuration-steps"></a>配置步骤
按照这些步骤配置 Azure Active Directory 身份验证。

1. 创建并填充 Azure Active Directory
2. 可选：关联或更改当前与 Azure 订阅关联的 Active Directory
3. 为 Azure SQL 数据仓库创建 Azure Active Directory 管理员。
4. 配置客户端计算机
5. 在映射到 Azure AD 标识的数据库中创建包含的数据库用户
6. 使用 Azure AD 标识连接到数据仓库

目前，Azure Active Directory 用户不会显示在 SSDT 对象资源管理器中。 解决方法是在 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx) 中查看这些用户。

### <a name="find-the-details"></a>查看详细信息
* 配置和使用 Azure Active Directory 身份验证的步骤与适用于 Azure SQL 数据库和 Azure SQL 数据仓库的步骤几乎完全相同。 请遵循主题 [使用 Azure Active Directory 身份验证连接到 SQL 数据库或 SQL 数据仓库](../sql-database/sql-database-aad-authentication.md)中的详细步骤。
* 创建自定义数据库角色，并向角色添加用户。 然后，向角色授予具体权限。 有关详细信息，请参阅[数据库引擎权限入门](https://msdn.microsoft.com/library/mt667986.aspx)。

## <a name="next-steps"></a>后续步骤
若要开始使用 Visual Studio 和其他应用程序查询数据仓库，请参阅[使用 Visual Studio 进行查询][Query with Visual Studio]。

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]:../active-directory/fundamentals/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
