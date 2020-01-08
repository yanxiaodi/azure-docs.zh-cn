---
title: 业务连续性和灾难恢复 (BCDR)：Azure 配对区域 | Microsoft Docs
description: 了解 Azure 区域对，以确保应用程序在数据中心发生故障期间可保持复原能力。
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 81ba993e6cbe55b45d34325545754bec561ce479
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514458"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>业务连续性和灾难恢复 (BCDR)：Azure 配对区域

## <a name="what-are-paired-regions"></a>什么是配对区域？

Azure 在世界各地的多个地理位置运营。 Azure 地理位置是至少包含一个 Azure 区域的规定世界区域。 Azure 区域是包含一个或多个数据中心的地理位置内的某个区域。

每个 Azure 区域与同一地理位置中另一个区域配对。 但巴西南部除外，它与自身地理位置外部的某个区域配对。 Azure 将跨区域对序列化平台更新（计划内维护），以便一次只更新一个配对区域。 如果发生影响多个区域的中断，则每对中的至少一个区域将优先进行恢复。

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

图 1 – Azure 区域对

| 地理位置 | 配对区域 |  |
|:--- |:--- |:--- |
| 亚洲 |东亚 |东南亚 |
| 澳大利亚 |澳大利亚东部 |澳大利亚东南部 |
| 澳大利亚 |澳大利亚中部 |澳大利亚中部 2 |
| 巴西 |巴西南部 |美国中南部 |
| 加拿大 |加拿大中部 |加拿大东部 |
| 中国 |中国北部 |中国东部|
| 中国 |中国北部 2 |中国东部 2|
| 欧洲 |欧洲北部（爱尔兰） |欧洲西部（荷兰） |
| 法国 |法国中部|法国南部|
| 德国 |德国中部 |德国东北部 |
| 印度 |印度中部 |印度南部 |
| 印度 |印度西部 |印度南部 |
| 日本 |日本东部 |日本西部 |
| 韩国 |韩国中部 |韩国南部 |
| 北美 |East US |美国西部 |
| 北美 |美国东部 2 |美国中部 |
| 北美 |美国中北部 |美国中南部 |
| 北美 |美国西部 2 |美国中西部 
| 南非 | 南非北部 | 南非西部
| 英国 |英国西部 |英国南部 |
| 阿拉伯联合酋长国 | 阿拉伯联合酋长国北部 | 阿拉伯联合酋长国中部
| 美国国防部 |美国 DoD 东部 |美国 DoD 中部 |
| 美国政府 |美国亚利桑那州政府 |美国德克萨斯州政府 |
| 美国政府 |US Gov 爱荷华州 |美国政府弗吉尼亚州 |
| 美国政府 |美国政府弗吉尼亚州 |美国德克萨斯州政府 |

表 1 - Azure 区域对映射

- 印度西部配对只在一个方向。 印度西部的次要区域是印度南部，而印度南部的次要区域是印度中部。
- 巴西南部与其他区域的不同之处在于，它与自身地理位置外部的区域配对。 巴西南部的次要区域是美国中南部。 美国中南部的次要区域不是巴西南部。
- 美国爱荷华州政府的次要区域是美国弗吉尼亚州政府。
- 美国弗吉尼亚州政府的次要区域是美国德克萨斯州政府。
- 美国德克萨斯州政府的次要区域是美国弗吉尼亚亚利桑那州。


我们建议你跨区域对配置业务连续性和灾难恢复 (BCDR)，以便从 Azure 的隔离和可用性策略中受益。 对于支持多个活动区域的应用程序，我们建议尽可能使用区域对中的这两个区域。 这将确保应用程序的最佳可用性，并在发生灾难时最大限度地缩短恢复时间。 

## <a name="an-example-of-paired-regions"></a>配对区域的示例
以下图 2 显示了使用区域对进行灾难恢复的虚构应用程序。 绿色数字突出显示了三个 Azure 服务（Azure 计算、存储和数据库）的跨区域活动，以及这些服务如何配置为跨区域复制。 橙色数字突出显示了跨配对区域部署的独特优势。

![配对区域的优势概览](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

图 2 – 虚构的 Azure 区域对

## <a name="cross-region-activities"></a>跨区域活动
如图 2 所示。

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure 计算 (IaaS)** - 必须提前预配附加的计算资源，以确保在发生灾难期间另一个区域可以提供资源。 有关详细信息，请参阅 [Azure resiliency technical guidance](resiliency/resiliency-technical-guidance.md)（Azure 复原技术指南）。

![存储](./media/best-practices-availability-paired-regions/2Green.png) **Azure 存储**-如果你使用的托管的磁盘，了解有关[跨区域备份](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines)使用 Azure 备份并[复制 Vm](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication)从一个区域到另一个使用 Azure Site Recovery。 如果使用的存储帐户，然后异地冗余存储 (GRS) 是默认配置时创建一个 Azure 存储帐户。 使用 GRS 时，数据会在主要区域自动复制三次，并在配对区域复制三次。 有关详细信息，请参阅 [Azure 存储冗余选项](storage/common/storage-redundancy.md)。

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL 数据库** – 使用 Azure SQL 数据库异地复制，可以将事务的异步复制配置到全球任何区域；但是，我们建议在配对区域中为大多数灾难恢复方案部署这些资源。 有关详细信息，请参阅 [Azure SQL 数据库中的异地复制](sql-database/sql-database-geo-replication-overview.md)。

![资源管理器](./media/best-practices-availability-paired-regions/4Green.png)**Azure 资源管理器** - 资源管理器原本就能跨区域提供组件的逻辑隔离。 这意味着某个区域发生逻辑故障不太可能会影响另一个区域。

## <a name="benefits-of-paired-regions"></a>配对区域的优势
如图 2 所示。  

![隔离](./media/best-practices-availability-paired-regions/5Orange.png)
**物理隔离** - Azure 希望区域对中数据中心之间的距离尽量至少保持 300 英里，不过，要在所有地理位置满足这个要求并不实际且不可能。 物理数据中心隔离能够降低自然灾害、社会动乱、电力中断或物理网络中断同时影响两个区域的可能性。 隔离受限于地理位置的约束（地理位置大小、电源/网络基础结构可用性、法规，等等）。  

![复制](./media/best-practices-availability-paired-regions/6Orange.png)
**平台提供的复制** - 某些服务（例如异地冗余存储）提供自动复制到配对区域的功能。

![恢复](./media/best-practices-availability-paired-regions/7Orange.png)
**区域恢复顺序** - 如果发生广泛中断，会优先恢复每个对中的某一个区域。 可以保证跨配对区域部署的应用程序在其中一个区域中优先恢复。 如果应用程序部署在未配对的区域中，则可能会发生恢复延迟；最坏的情况是，选中的两个区域可能最后才会得到恢复。

![更新](./media/best-practices-availability-paired-regions/8Orange.png)
**依序更新** - 计划的 Azure 系统更新将按顺序发布到配对的区域（不是同时发布），以便在出现罕见的更新失败时，将停机时间、Bug 影响和逻辑故障的可能性降到最低。

![数据](./media/best-practices-availability-paired-regions/9Orange.png)
**数据驻留** - 一个区域驻留在与其配对区域相同的地理位置（巴西南部除外），以符合税务和执法管辖范围方面的数据驻留要求。
