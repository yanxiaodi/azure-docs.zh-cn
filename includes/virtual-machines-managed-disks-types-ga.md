---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2bc5602011ed64b11b1b8c96b7e69a8d5ee9bf32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133353"
---
## <a name="premium-ssd"></a>高级·SSD

Azure 高级 SSD 为运行输入/输出 (IO) 密集型工作负荷的虚拟机 (VM) 提供高性能、低延迟的磁盘支持。 若要利用高级存储磁盘的速度和性能优势，可将现有的 VM 磁盘迁移到高级 SSD。 高级 SSD 适用于任务关键型生产应用程序。 高级 Ssd 仅可用于将高级存储兼容的 VM 系列。

若要详细了解各种 VM 类型和大小以 Azure 为 Windows，包括的大小高级存储兼容，请参阅[Windows VM 大小](../articles/virtual-machines/windows/sizes.md)。 若要了解有关各个 VM 类型和在 Azure 中的大小的详细信息对于 Linux，包括的大小高级存储兼容，请参阅[Linux VM 大小](../articles/virtual-machines/linux/sizes.md)。

### <a name="disk-size"></a>磁盘大小
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

预配高级存储磁盘时，可以获得该磁盘的容量、IOPS 和吞吐量保证，这与标准存储不同。 例如，如果创建 P50 磁盘，Azure 将为此磁盘预配 4,095-GB 存储容量、7,500 IOPS 和 250-MB/秒的吞吐量。 应用程序可以使用全部或部分容量与性能。 高级 SSD 磁盘旨在提供低位数的毫秒级延迟和面向 IOPS 和吞吐量上述表 99.9%的时间中所述。

### <a name="transactions"></a>事务

对于高级 Ssd，每个 I/O 操作小于或等于为 256 KiB 吞吐量被视为单个 I/O 操作。 I/O 的吞吐量大于 256 KiB 操作被视为多个 I/o 大小为 256 KiB。

## <a name="standard-ssd"></a>标准 SSD

Azure 标准 SSD 是经济高效的存储选项，已针对需要一致性能和较低 IOPS 级别的工作负荷进行优化。 对于想要迁移到云的用户而言，标准 SSD 提供良好的入门级体验，尤其是在本地 HDD 解决方案中运行各种工作负荷遇到问题时。 标准 Ssd 与标准 Hdd 相比，提供更好的可用性、 一致性、 可靠性和延迟。 标准 SSD 适用于 Web 服务器、低 IOPS 应用程序服务器、较少使用的企业应用程序和开发/测试工作负荷。 与标准 Hdd，标准 SSDs 是可在所有 Azure Vm 上。

### <a name="disk-size"></a>磁盘大小
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

标准 Ssd 旨在提供一位数的毫秒级延迟的 IOPS 和吞吐量达到上述表 99%的时间中所述的限制。 实际 IOPS 和吞吐量有时根据流量模式而异。 相比 HDD 磁盘，标准 SSD 提供更加一致的性能，并且延迟更低。

### <a name="transactions"></a>事务

对于标准的 Ssd，每个 I/O 操作小于或等于为 256 KiB 吞吐量被视为单个 I/O 操作。 I/O 的吞吐量大于 256 KiB 操作被视为多个 I/o 大小为 256 KiB。 这些事务具有计费影响。

## <a name="standard-hdd"></a>标准 HDD

Azure 标准 HDD 为运行不区分延迟的工作负荷提供可靠、低成本的磁盘支持。 使用标准存储，将数据存储在硬盘驱动器 (HDD)。 与基于 SSD 的磁盘相比，延迟、 IOPS 和吞吐量的标准 HDD 磁盘可能会更广泛地有所不同。 标准 HDD 磁盘旨在提供写下 10 毫秒的延迟和读取延迟在 20 毫秒，对于大多数 IO 操作，但是实际性能可能会有所不同，具体取决于 IO 大小和工作负荷模式。 时使用的 Vm，可以使用标准 HDD 磁盘，用于开发/测试方案和更低的关键工作负荷。 标准 Hdd 可在所有 Azure 区域以及可用于所有 Azure Vm。

### <a name="disk-size"></a>磁盘大小
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>事务

对于标准 HDD，每个 IO 操作会被视为单个事务，无论 I/O 大小如何。 这些事务具有计费影响。

## <a name="billing"></a>计费

使用托管磁盘时，将考虑以下计费事项：

- 磁盘类型
- 托管磁盘大小
- 快照
- 出站数据传输
- 事务数

**托管磁盘大小**：托管磁盘按预配大小计费。 Azure 将预配大小映射（向上舍入）到所提供的最接近的磁盘大小。 有关所提供的磁盘大小的详细信息，请参阅前面的表。 每个磁盘将映射到一种受支持的预配磁盘大小套餐并相应地计费。 例如，如果预配了 200 GiB 的标准 SSD，它会映射到 E15 的磁盘大小 (256 GiB)。 任何预配的磁盘根据每月的高级存储优惠价格按小时计费。 例如，如果在预配 E10 磁盘的 20 小时后删除它，则会以 20 小时计算 E10 产品/服务的费用。 这与写入磁盘的实际数据量无关。

**快照**：基于已使用大小对快照计费。 例如，如果创建预配容量为 64 GiB 且实际使用数据大小为 10 GiB 的托管磁盘的快照，则仅针对已用数据大小 10 GiB 对该快照计费。
