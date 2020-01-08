---
title: Azure SAP HANA（大型实例）的存储体系结构 | Microsoft Docs
description: 有关如何部署 Azure SAP HANA（大型实例）的存储体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 256aaf94175394fd737e53c6281f2d8b45e8af41
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099646"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA（大型实例）存储体系结构

SAP HANA 根据 SAP 建议的指导原则在经典部署模型中配置 Azure 上的 SAP HANA（大型实例）的存储布局。 [SAP HANA 存储要求](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)白皮书中阐述了指导原则。

I 类 HANA 大型实例附带有四倍内存卷作为存储卷。 对于 II 类 HANA 大型实例单位，存储不会是四倍大小。 这些单位附带的卷用于存储 HANA 事务日志备份。 有关详细信息，请参阅[安装和配置 Azure 上的 SAP HANA（大型实例）](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

请参阅下表，了解存储分配情况。 此表大致列出了不同卷的容量，这些卷是随不同的 HANA 大型实例单位一起提供的。

| HANA 大型实例 SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S96 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4,608 GB | 1,024 GB | 1,536 GB | 1,024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xm | 16,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xxm |  20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576m | 20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4,096 GB | 2,048 GB | 4,096 GB |
| S768m | 28,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768xm | 40,960 GB | 6,144 GB | 4,096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


实际部署的卷可能会稍有不同，具体取决于部署以及用来显示卷大小的工具。

如果对 HANA 大型实例 SKU 进行细分，则请参阅以下示例，了解可能的分区块：

| 内存分区 (GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1,024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


这些大小为大致的卷数字，可能会因部署和用来查看卷的工具而稍有不同。 此外还可以考虑其他分区大小，例如 2.5 TB。 这些存储大小在计算时使用的公式类似于那些用于上述分区的公式。 “分区”一词并不表示操作系统、内存或 CPU 资源也可以分区。 它只是表示适用于不同 HANA 实例的存储分区，这些实例可能需要部署到单个 HANA 大型实例单位。 

可能需要更多的存储。 可以通过购买额外存储（以 1 TB 为单位）的方式来添加存储。 该额外存储可以作为额外卷来添加， 也可以用来扩展一个或多个现有卷。 卷的大小（大部分如上表所述）一经部署就不能缩减。 也不能更改卷名或装入名。 如上所述的存储卷是作为 NFS4 卷附加到 HANA 大型实例单位的。

可以使用存储快照来实现备份、还原和灾难恢复目的。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

请参阅[支持 HLI 的方案](hana-supported-scenario.md)，了解方案的存储布局详细信息。

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>在一个 HANA 大型实例单位上运行多个 SAP HANA 实例

可以在 HANA 大型实例单位上托管多个活动的 SAP HANA 实例。 此类配置需要按实例进行卷设置，这样就仍然能够提供存储快照和灾难恢复的功能。 目前，HANA 大型实例单位可以细分如下：

- **S72、S72m、S96、S144、S192**:增量为 256 GB, 最小起始单位为 256 GB。 可以组合使用不同的增量（例如 256 GB、512 GB 等），但不得超出该单位的最大内存。
- **S144m 和 S192m**:增量为 256 GB, 512 GB 是最小单位。 可以组合使用不同的增量（例如 512 GB、768 GB 等），但不得超出该单位的最大内存。
- **类型 II 类**:增量为 512 GB, 最小起始单位为 2 TB。 可以组合使用不同的增量（例如 512 GB、1 TB 和 1.5 TB 等），但不得超出该单位的最大内存。

运行多个 SAP HANA 实例的部分示例如下。

| SKU | 内存大小 | 存储大小 | 使用多个数据库时的大小 |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768 GB HANA 实例<br /> 或 1 x 512 GB 实例 + 1 x 256 GB 实例<br /> 或 3 x 256 GB 实例 | 
| S72m | 1.5 TB | 6 TB | 3x512GB HANA 实例<br />或 1 x 512 GB 实例 + 1 x 1 TB 实例<br />或 6 x 256 GB 实例<br />或 1 x 1.5 TB 实例 | 
| S192m | 4 TB | 16 TB | 8 x 512 GB 实例<br />或 4 x 1 TB 实例<br />或 4 x 512 GB 实例 + 2 x 1 TB 实例<br />或 4 x 768 GB 实例 + 2 x 512 GB 实例<br />或 1 x 4 TB 实例 |
| S384xm | 8 TB | 22 TB | 4 x 2 TB 实例<br />或 2 x 4 TB 实例<br />或 2 x 3 TB 实例 + 1 x 2 TB 实例<br />或 2 x 2.5 TB 实例 + 1 x 3 TB 实例<br />或 1 x 8 TB 实例 |


还有其他组合变化。 

## <a name="encryption-of-data-at-rest"></a>静态数据加密
用于 HANA 大型实例的存储对数据使用透明加密, 因为它是在2018年结束后存储在磁盘上。 在以前的部署中, 你可以选择将卷加密。 如果你决定采用该选项, 则可以请求获取联机加密的卷。 将从非加密移动到加密卷是透明的并且不需要停机时间。 

使用 I 类 SKU 时，会加密存储启动 LUN 的卷。 在修订版 3 HANA 大型实例标记中, 使用 HANA 大型实例 Sku 的类型 II 类, 需要使用 OS 方法加密启动 LUN。 在版本4中, HANA 大型实例戳, 使用类型 II 单位存储启动 LUN 的卷, 默认情况下加密为静态。 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>HANA 大型实例上较大 HANA 实例的必需设置
HANA 大型实例中使用的存储具有文件大小限制。 [大小限制为](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)每个文件 16 TB。 不同于 EXT3 文件系统中的文件大小限制, HANA 不会隐式识别 HANA 大型实例存储所强制执行的存储限制。 因此, 当达到16TB 的文件大小限制时, HANA 不会自动创建新的数据文件。 由于 HANA 尝试将文件增长到超过 16 TB, 因此, HANA 将报告错误, 并且索引服务器将在结尾崩溃。

> [!IMPORTANT]
> 为了防止 HANA 尝试将数据文件增长到 HANA 大型实例存储的 16 TB 文件大小限制之外, 你需要在 HANA 的全局 .ini 配置文件中设置以下参数
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - 另请参阅 SAP 说明[#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - 请注意 SAP 说明[#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**后续步骤**
- 请参阅 [HANA 大型实例支持的方案](hana-supported-scenario.md)