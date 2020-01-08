---
title: Azure SAP HANA（大型实例）操作系统升级 | Microsoft Docs
description: 执行 Azure SAP HANA（大型实例）操作系统升级
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6341f58791c2fad71a65650e32cff02fb52d78c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098682"
---
# <a name="operating-system-upgrade"></a>操作系统升级
本文档介绍 HANA 大型实例操作系统升级的详细信息。

>[!NOTE]
>操作系统升级是客户的责任，Microsoft 运营支持团队可指导你发现升级过程中需要留意的重点领域。 在计划升级前，你还应咨询操作系统供应商。

在现在的速度设置中, Microsoft 运营团队安装了操作系统。 随着时间推移，你需要维护 HLI 单元的操作系统（例如：修补、优化、升级等）。

在对操作系统进行重大更改 (例如, 将 SP1 升级到 SP2) 之前, 需要通过打开支持票证来与 Microsoft 运营团队联系以进行查阅。

在票证中包括:

* 你的 HLI 订阅 ID。
* 你的服务器名称。
* 你打算应用的修补程序级别。
* 你计划此更改的日期。 

建议你至少比理想的升级日期提前一周来提交此票证，因为需要让操作团队检查你的服务器刀片是否需要进行固件升级。


有关不同 Linux 版本的不同 SAP HANA 版本的支持矩阵，请参阅 [SAP 说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。


## <a name="known-issues"></a>已知问题

以下是升级过程中几个常见的已知问题：
- 在 II 类 SKU 上，Software Foundation Software (SFS) 会在操作系统升级后移除。 在 OS 升级后, 需要重新安装兼容的 SFS。
- 以太网卡驱动程序（ENIC 和 FNIC）会回滚到旧版本。 升级后需要重新安装兼容的驱动程序版本。

## <a name="next-steps"></a>后续步骤
- 有关 I 类 SKU 操作系统备份的信息，请参阅[备份和恢复](hana-overview-high-availability-disaster-recovery.md)。
- 请参阅第 ii 类 SKU 类的[修订版3橡皮类型的操作系统备份](os-backup-type-ii-skus.md)。
