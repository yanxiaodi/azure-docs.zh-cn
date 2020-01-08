---
title: Azure SQL 数据库资源限制 - 托管实例 | Microsoft Docs
description: 本文概述托管实例的 Azure SQL 数据库资源限制。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 09/16/2019
ms.openlocfilehash: 5eaade975adac86b6842d1d8f9f9b8f522d15bca
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71816080"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Azure SQL 数据库托管实例资源限制概述

本文概述了 Azure SQL 数据库托管实例的技术特征和资源限制，并提供了有关如何请求提高这些限制的信息。

> [!NOTE]
> 有关支持的功能和 T-SQL 语句的差异，请参阅[功能差异](sql-database-features.md)和 [T-SQL 语句支持](sql-database-managed-instance-transact-sql-information.md)。 有关单一数据库和托管实例中服务层级之间的一般差异，请参阅[服务层级比较](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)。

## <a name="instance-level-resource-limits"></a>实例级别的资源限制

托管实例的某些特征和资源限制取决于底层基础结构和体系结构。 这些限制取决于硬件代系和服务层级。

### <a name="hardware-generation-characteristics"></a>硬件代系特性

Azure SQL 数据库托管实例可部署在两个硬件代次上：Gen4 和 Gen5。 硬件代次具有不同的特征，如下表所述：

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| 硬件 | Intel E5-2673 v3 (Haswell) 2.4-GHz 处理器、附加的 SSD vCore = 1 PP（物理核心） | Intel E5-2673 v4 (Broadwell) 2.3-GHz 处理器、快速 NVMe SSD、vCore=1 LP（超线程） |
| vCore 数目 | 8、16、24 个 vCore | 4、8、16、24、32、40、64、80 个 vCore |
| 最大内存（内存/核心比） | 每个 vCore 7 GB<br/>添加更多 vCore 以获得更多内存。 | 每个 vCore 5.1 GB<br/>添加更多 vCore 以获得更多内存。 |
| 最大内存中 OLTP 内存 | 实例限制：1-每 vCore 1.5 GB| 实例限制：每 vCore 0.8-1.65 GB |
| 最大实例预留存储 |  常规用途：8 TB<br/>业务关键：1TB | 常规用途：8 TB<br/> 业务关键型 1 TB、2 TB 或 4 TB，具体取决于核心数 |

> [!IMPORTANT]
> - Gen4 硬件正在逐步推出。建议在 Gen5 硬件上部署新的托管实例。
> - 目前，Gen4 硬件仅在以下区域提供：北欧、西欧、美国东部、美国中南部、美国中北部、美国西部2、美国中部、加拿大中部、印度南部和韩国中部。

#### <a name="in-memory-oltp-available-space"></a>内存中 OLTP 可用空间 

内存中 OLTP 空间量取决于 Vcore 和硬件生成的数量。 下表列出了可用于内存中 OLTP 对象的内存限制。

| 每个 vCore 的内存中 OLTP 空间    | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 | 3.14 GB | |   
| 8 | 6.28 GB | 8 GB |
| 16    | 15.77 GB | 20 GB |
| 24    | 25.25 GB | 36 GB |
| 32    | 37.94 GB | |
| 40    | 52.23 GB | |
| 64    | 99.9 GB   | |
| 80    | 131.68 GB| |

### <a name="service-tier-characteristics"></a>服务层特征

托管实例有两个服务层级：[常规用途](sql-database-service-tier-general-purpose.md)和[业务关键](sql-database-service-tier-business-critical.md)。 这些层级提供[不同的功能](sql-database-service-tiers-general-purpose-business-critical.md)，如下表中所述：

| **功能** | **常规用途** | **业务关键** |
| --- | --- | --- |
| vCore 数目\* | 第 4 代：8、16、24<br/>Gen5：4、8、16、24、32、40、64、80 | Gen4：8、16、24 <br/> Gen5：4、8、16、24、32、40、64、80 |
| 最大内存 | Gen4：56 GB - 168 GB (7GB/vCore)<br/>Gen5：20.4 GB-408 GB （5.1 GB/vCore）<br/>添加更多 vCore 以获得更多内存。 | Gen4：56 GB - 168 GB (7GB/vCore)<br/>Gen5：20.4 GB-408 GB （5.1 GB/vCore）<br/>添加更多 vCore 以获得更多内存。 |
| 最大实例存储大小（保留） | - 2 TB，适用于 4 个 vCore（仅限 Gen5）<br/>- 8 TB，适用于其他大小 | Gen4：1 TB <br/> Gen5： <br/>- 1 TB，适用于 4、8、16 个 vCore<br/>- 24 个 vCore 2 TB<br/>- 32、40、64、80 个 vCore 4 TB |
| 最大数据库大小 | 当前可用实例大小（最大为 2 TB-8 TB，具体取决于 Vcore 数）。 | 最高当前可用实例大小（最大 1 TB-4 TB，取决于 Vcore 数）。 |
| 最大 tempDB 大小 | 限制为 24 GB/vCore （96-1920 GB）和当前可用的实例存储大小。<br/>添加更多 vCore 以获得更多 TempDB 空间。 | 最多当前可用的实例存储大小。 TempDB 日志文件大小目前的限制为 24GB/vCore。 |
| 每个实例的数据库数目上限 | 100，除非已达到实例存储大小限制。 | 100，除非已达到实例存储大小限制。 |
| 每个实例的数据库文件数目上限 | 最大为280，除非已达到实例存储大小或[Azure 高级磁盘存储空间](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files)的限制。 | 每个数据库32767个文件，除非已达到实例存储大小限制。 |
| 数据文件最大大小 | 仅限当前可用的实例存储大小（最大 2 TB-8 TB）和[Azure 高级磁盘存储分配空间](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files)。 | 仅限当前可用的实例存储大小（最大为 1 TB-4 TB）。 |
| 最大日志文件大小 | 仅限 2 TB 和当前可用的实例存储大小。 | 仅限 2 TB 和当前可用的实例存储大小。 |
| 数据/日志 IOPS（近似） | 500 - 7,500（每个文件）<br/>\*[增大文件大小以获取更多 IOPS](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 5.5 K - 110 K (1375/vCore)<br/>添加更多 vCore 以获得更好的 IO 性能。 |
| 日志写入吞吐量限制（每个实例） | 3 MB/s（每个 vCore）<br/>最大 22 MB/秒 | 4 MB/秒（每个 vCore）<br/>最大 48 MB/s |
| 数据吞吐量（近似） | 100 - 250 MB/s（每个文件）<br/>\*[增大文件大小以获得更好的 IO 性能](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | 不受限制。 |
| 存储 IO 延迟（近似） | 5-10 毫秒 | 1-2 毫秒 |
| 内存中 OLTP | 不支持 | 可用 |
| 最大会话数 | 30000 | 30000 |
| [只读副本](sql-database-read-scale-out.md) | 0 | 1（包含在价格中） |

> [!NOTE]
> - **当前可用的实例存储大小**是保留实例大小与已用存储空间之间的差异。
> - 与最大存储大小限制进行比较的实例存储大小同时包括用户和系统数据库中的数据及日志文件大小。 可以使用 <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 系统视图来确定数据库使用的空间总量。 错误日志不会持久保存，不包括在大小中。 备份不包括在存储大小中。
> - 吞吐量和 IOPS 还取决于不受托管实例显式限制的页面大小。
> 可以使用自动故障转移组在不同的 Azure 区域中创建另一个只读副本。

> [!NOTE]
> 有关详细信息，请查看[本文中托管实例池中的资源限制](sql-database-instance-pools.md#instance-pools-resource-limitations)。

## <a name="supported-regions"></a>支持的区域

托管实例只能在[支持的区域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)中创建。 若要在当前不支持的区域中创建托管实例，可以[通过 Azure 门户发送支持请求](#obtaining-a-larger-quota-for-sql-managed-instance)。

## <a name="supported-subscription-types"></a>支持的订阅类型

目前，托管实例仅支持以下订阅类型中的部署：

- [企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [云服务提供商 (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 订阅者每月 Azure 信用额度的订阅](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>区域资源限制

支持的订阅类型针对每个区域可包含有限的资源数。 对于每个 Azure 区域，托管实例有两个默认限制（可以根据订阅类型的类型，通过在[Azure 门户中创建特殊支持请求](#obtaining-a-larger-quota-for-sql-managed-instance)来增加按需增加）：

- **子网限制**：在单一区域中部署托管实例的子网数上限。
- **vCore unit 限制**：可在单个区域中的所有实例之间部署的 vCore 单位的最大数目。 一个 GP vCore 使用一个 vCore 单元，一个 BC vCore 采用4个 vCore 单位。 实例的总数不受限于，只要它在 vCore 单元限制内。

> [!Note]
> 这些限制是默认设置，不是技术限制。 如果在当前区域中需要更多托管实例，可以[在 Azure 门户中创建特殊支持请求](#obtaining-a-larger-quota-for-sql-managed-instance)，以根据需要提高限制。 或者，可以在另一个 Azure 区域中创建新的托管实例，而不需要发送支持请求。

下表显示了支持的订阅类型的**默认区域限制**（可以使用下面所述的支持请求扩展默认限制）：

|订阅类型| 托管实例子网数目上限 | vCore 单元数目上限* |
| :---| :--- | :--- |
|即用即付|3|320|
|CSP |8（在某些区域中为 15 * *）|960（在某些区域中为 1440 * *）|
|即用即付开发/测试|3|320|
|Enterprise 开发/测试|3|320|
|EA|8（在某些区域中为 15 * *）|960（在某些区域中为 1440 * *）|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional 和 MSDN 平台|2|32|

\*在规划部署时，请考虑业务关键（BC）服务层需要四（4）倍于常规用途（GP）服务层的 vCore。 例如：1 GP vCore = 1 vCore unit 和 1 BC vCore = 4 vCore 单位。 若要简化对默认限制的消耗分析，请在部署托管实例的区域中的所有子网中汇总 vCore 单位，并将结果与订阅类型的实例单位限制进行比较。 **VCore 单位限制的最大数量**适用于区域中的每个订阅。 每个子网没有任何限制，只不过跨多个子网部署的所有 Vcore 的总和必须小于或等于**vCore 单元的最大数目**。

\*\*以下区域提供了更大的子网和 vCore 限制：澳大利亚东部、美国东部、美国东部2、北欧、美国中南部、东南亚、英国南部、西欧、美国西部2。

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>获取更大的 SQL 托管实例配额

如果在当前区域中需要更多托管实例，请使用 Azure 门户发送扩展配额的支持请求。
若要启动获取更大配额的过程，请执行以下操作：

1. 打开“帮助 + 支持”，单击“新建支持请求”。

   ![帮助和支持](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 在新支持请求的“基本信息”选项卡上：
   - 对于“问题类型”，选择“服务和订阅限制(配额)”。
   - 对于“订阅”，请选择自己的订阅。
   - 对于“配额类型”，选择“SQL 数据库托管实例”。
   - 对于“支持计划”，选择自己的支持计划。

     ![问题类型配额](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. 单击“下一步”。
4. 在新支持请求的“问题”选项卡上：
   - 对于“严重性”，选择问题的严重性级别。
   - 对于“详细信息”，提供有关问题的其他信息，包括错误消息。
   - 对于“文件上传”，附加包含详细信息的文件（最多 4 MB）。

     ![问题详细信息](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 有效的请求应包括：
     > - 需要提高订阅限制的区域。
     > - 每个服务层级在配额增加后在现有的子网中所需的 vCore 数目（如果需要扩展任何现有的子网）。
     > - 所需的新子网数目，以及每个服务层级在新子网内的 vCore 总数（如果需要在新子网中部署托管实例）。

5. 单击“下一步”。
6. 在新支持请求的“联系人信息”选项卡上，输入首选联系方式（电子邮件或电话）和联系人详细信息。
7. 单击“创建”。

## <a name="next-steps"></a>后续步骤

- 有关托管实例的详细信息，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
- 有关定价信息，请参阅 [SQL 数据库托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
- 若要了解如何创建第一个托管实例，请参阅[快速入门指南](sql-database-managed-instance-get-started.md)。
