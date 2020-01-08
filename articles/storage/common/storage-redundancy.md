---
title: Azure 存储中的数据冗余 | Microsoft Docs
description: 复制 Microsoft Azure 存储帐户中的数据以实现持久性和高可用性。 冗余选项包括本地冗余存储（LRS）、区域冗余存储（ZRS）、异地冗余存储（GRS）、读取访问异地冗余存储（RA-GRS）、区域冗余存储（GZRS）（预览版）和读取访问异地区域冗余存储（GZRS）（预览）。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 8025228275afeb3f23268db759eb7659b9887132
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670785"
---
# <a name="azure-storage-redundancy"></a>Azure 存储冗余

始终复制 Microsoft Azure 存储帐户中的数据以确保持久性和高可用性。 Azure 存储功能会复制数据，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 可以选择在同一数据中心中、跨同一区域中的局域数据中心或跨地理上隔离的区域复制数据。

即使面临故障时，复制也可确保存储帐户满足[存储的服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/)的要求。 请参阅 SLA，了解有关 Azure 存储确保持续性和可用性的信息。

Azure 存储使用循环冗余检验 (CRC) 定期验证存储的数据的完整性。 如果检测到数据损坏，则使用冗余数据进行修复。 Azure 存储还计算所有网络流量的校验和，以检测存储或检索数据时数据包的损坏。

## <a name="choosing-a-redundancy-option"></a>选择冗余选项

创建存储帐户时，可以选择以下冗余选项之一：

[!INCLUDE [azure-storage-redundancy](../../../includes/azure-storage-redundancy.md)]

下表简要概述了每种复制策略为给定类型的事件（或具有类似影响性的事件）提供的持久性和可用性范围。

| 应用场景                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS （预览版）                              |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| 数据中心内的节点不可用                                                                 | 是                             | 是                              | 是                                  | 是                                  |
| 整个数据中心（区域性或非区域性）不可用                                           | 否                              | 是                              | 是                                  | 是                                  |
| 区域范围的服务中断                                                                                     | 否                              | 否                               | 是                                  | 是                                  |
| 整个区域不可用时对数据进行读取访问（远程异地复制区域中） | 否                              | 否                               | 是（通过 GRS）                                   | 是（通过 GZRS）                                 |
| 旨在给定年份为对象提供 \_\_ 的持续性                                          | 至少为 99.999999999% (11 9's) | 至少为 99.9999999999% (12 9's) | 至少为 99.99999999999999% (16 9's) | 至少为 99.99999999999999% (16 9's) |
| 支持的存储帐户类型                                                                   | GPv2、GPv1、Blob                | GPv2                             | GPv2、GPv1、Blob                     | GPv2                     |
| 读取请求的可用性 SLA | 至少为 99.9%（冷访问层为 99%） | 至少为 99.9%（冷访问层为 99%） | 对于 GRS，至少 99.9% （对于冷访问层为 99%）<br /><br />至少 99.99% （对于 "冷" 访问层为 99.9%） GRS | 对于 GZRS，至少 99.9% （对于冷访问层为 99%）<br /><br />至少 99.99% （对于 "冷" 访问层为 99.9%） GZRS |
| 写入请求的可用性 SLA | 至少为 99.9%（冷访问层为 99%） | 至少为 99.9%（冷访问层为 99%） | 至少为 99.9%（冷访问层为 99%） | 至少为 99.9%（冷访问层为 99%） |

将复制存储帐户中的所有数据，包括块 blob 和追加 blob、页 blob、队列、表和文件。 尽管 ZRS 需要常规用途 v2 存储帐户，但会复制所有类型的存储帐户。

有关每个冗余选项的定价信息，请参阅 [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)。 

有关 Azure 存储确保持续性和可用性的信息，请参阅 [Azure 存储 SLA](https://azure.microsoft.com/support/legal/sla/storage/)。

> [!NOTE]
> Azure 高级存储目前仅支持本地冗余存储（LRS）。

## <a name="changing-replication-strategy"></a>更改复制策略

可以使用[Azure 门户](https://portal.azure.com/)、 [azure Powershell](storage-powershell-guide-full.md)、 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)或[azure 存储客户端库](https://docs.microsoft.com/azure/index#pivot=sdkstools)之一来更改存储帐户的复制策略。 更改存储帐户的复制类型不会导致停机。

> [!NOTE]
> 目前，不能使用 Azure 门户或 Azure 存储客户端库将帐户转换为 ZRS、GZRS 或 GZRS。 若要将帐户迁移到 ZRS，请参阅用于[构建高度可用的 Azure 存储应用程序的区域冗余存储（ZRS）](storage-redundancy-zrs.md) ，了解详细信息。 若要迁移 GZRS 或 GZRS，请参阅[异地冗余存储以获得高可用性和最大持续性（预览版）](storage-redundancy-zrs.md) ，了解详细信息。

### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>更改帐户的复制策略是否产生任何费用？

这取决于转换路径。 从至少到最昂贵的 Azure 存储冗余产品/服务、LRS、ZRS、GRS、RA-GRS、GZRS 和 RA-GZRS 进行排序。 例如，从 LRS 转移到任何其他类型的复制会产生额外的费用，因为这是转移到更高级的冗余级别。 迁移到 GRS 或 RA-GRS 会产生出口带宽费用，因为（主要区域中的）数据将复制到远程次要区域。 此费用是在初始设置期间收取的一次性费用。 复制数据后，无需进一步支付迁移费用。 只有在复制任何新数据，或者复制现有数据的更新时才要付费。 有关带宽费用的详细信息，请参阅 [Azure 存储定价页面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

如果将存储帐户从 GRS 迁移到 LRS，则不会产生额外的费用，但从次要位置复制的数据将被删除。

如果将存储帐户从 RA-GRS 迁移到 GRS 或 LRS，则会在完成转换 30 天后以 RA-GRS 方式对该帐户计费。

## <a name="see-also"></a>请参阅

- [本地冗余存储 (LRS)：Azure 存储的低成本数据冗余](storage-redundancy-lrs.md)
- [区域冗余存储 (ZRS)：高度可用的 Azure 存储应用程序](storage-redundancy-zrs.md)
- [异地冗余存储 (GRS)：Azure 存储的跨区域复制](storage-redundancy-grs.md)
- [区域冗余存储（GZRS），用于实现高可用性和最大持续性（预览版）](storage-redundancy-gzrs.md)
- [Azure 存储可伸缩性和性能目标](storage-scalability-targets.md)
- [使用 RA-GRS 存储设计高度可用的应用程序](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure 存储冗余选项和读取访问异地冗余存储](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP 论文 - Azure 存储：具有高度一致性的高可用云存储服务](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
