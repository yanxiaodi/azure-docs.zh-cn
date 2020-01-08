---
title: Azure Data Lake Storage Gen1 跨区域迁移 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen1 的跨区域迁移。
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: 0bf0843314f38c0de28820c82e95b7921297bf40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60518473"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>跨区域迁移 Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 在新区域中推出后，用户可以选择执行一次性的迁移，并便可利用新区域。 了解规划和完成迁移时要注意的事项。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**。 有关详细信息，请参阅[立即创建免费 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)。
* **在两个不同的区域中提供 Data Lake Storage Gen1 帐户**。 有关详细信息，请参阅 [Azure Data Lake Storage Gen1 入门](data-lake-store-get-started-portal.md)。
* **Azure 数据工厂**。 有关详细信息，请参阅 [Azure 数据工厂简介](../data-factory/introduction.md)。


## <a name="migration-considerations"></a>迁移注意事项

首先，确定最适合在 Data Lake Storage Gen1 中写入、读取或处理数据的应用程序的迁移策略。 选择策略时，应考虑应用程序的可用性要求，以及迁移期间的停机时间。 例如，最简单的方法可能是使用“直接迁移”云迁移模型。 使用此方法时，可以暂停现有区域中的应用程序，同时将所有数据复制到新区域。 复制过程完成后，可以恢复新区域中的应用程序，并删除旧的 Data Lake Storage Gen1 帐户。 迁移过程中需要停机。

为了减少停机时间，可以立即开始在新区域中引入新数据。 获得所需的少量数据后，在新区域中运行应用程序。 在后台可以继续将现有 Data Lake Storage Gen1 帐户中的旧数据复制到新区域中的新 Data Lake Storage Gen1 帐户。 使用这种方法可以在造成极短停机时间的情况下切换到新区域。 复制所有旧数据后，可以删除旧的 Data Lake Storage Gen1 帐户。

规划迁移时要考虑的其他重要详细信息包括：

* **数据量**。 数据量（GB 大小、文件和文件夹数目等等）会影响迁移所需的时间和资源。

* **Data Lake Storage Gen1 帐户名**。 新区域中的新帐户名必须全局唯一。 例如，如果美国东部 2 区中旧 Data Lake Storage Gen1 帐户的名称为 contosoeastus2.azuredatalakestore.net， 可将欧盟北部中的新 Data Lake Storage Gen1 帐户命名为 contosonortheu.azuredatalakestore.net。

* **工具**。 建议使用 [Azure 数据工厂复制活动](../data-factory/connector-azure-data-lake-store.md)来复制 Data Lake Storage Gen1 文件。 数据工厂支持高性能、高可靠性的数据移动。 请记住，数据工厂只会复制文件夹层次结构和文件内容。 需要手动将旧帐户中使用的任何访问控制列表 (ACL) 应用到新帐户。 有关详细信息，包括最佳方案的性能目标，请参阅[复制活动性能和优化指南](../data-factory/copy-activity-performance.md)。 如果想要更快地复制数据，可能需要使用其他云数据移动单元。 其他某些工具（例如 AdlCopy）不支持在区域之间复制数据。  

* **带宽费用**。 由于要将数据传出 Azure 区域，因此会产生[带宽费用](https://azure.microsoft.com/pricing/details/bandwidth/)。

* **数据 ACL**。 可以通过向文件和文件夹应用 ACL 来保护新区域中的数据。 有关详细信息，请参阅[保护 Azure Data Lake Storage Gen1 中存储的数据](data-lake-store-secure-data.md)。 我们建议通过迁移来更新和调整 ACL。 可以使用类似于当前设置的设置。 可以使用 Azure 门户、[PowerShell cmdlet](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission) 或 SDK 查看已应用到任何文件的 ACL。  

* **分析服务的位置**。 为了获得最佳性能，应该将 Azure Data Lake Analytics 或 Azure HDInsight 等分析服务放置在数据所在的同一区域。  

## <a name="next-steps"></a>后续步骤
* [Azure Data Lake Storage Gen1 概述](data-lake-store-overview.md)
