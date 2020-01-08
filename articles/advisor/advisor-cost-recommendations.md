---
title: 使用 Azure 顾问降低服务成本 | Microsoft Docs
description: 使用 Azure 顾问优化 Azure 部署的成本。
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 78429001b855e3347e72fbb0f0d4d3171731a8e2
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703038"
---
# <a name="reduce-service-costs-using-azure-advisor"></a>使用 Azure 顾问降低服务成本

通过识别闲置和未充分利用的资源，顾问有助于优化和降低 Azure 总支出。 可在顾问仪表板的“成本”选项卡获取成本建议。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>通过调整或关闭未充分利用的实例来优化虚拟机花费 

虽然某些应用程序方案有意使虚拟机利用率较低，但通过管理虚拟机大小和数量通常可降低成本。 当最大 CPU 使用率的最大值为 3% 且网络使用率低于7天的 2% 时，顾问高级评估模型会将虚拟机 P95th。 如果可以在较小的 SKU （位于同一 SKU 系列中）或更小的实例数中容纳当前负载，使虚拟机可用于适当大小，以便在非用户工作负荷下当前负载不超过 80% 的利用率面向用户的工作负荷时，超过 40%。 此处的工作负荷类型通过分析工作负荷的 CPU 利用率特征来确定。

建议的操作为 "关闭" 或 "大小调整"，特定于建议使用的资源。 顾问会针对建议的操作（重设大小或关机）显示估计的成本节约。 而且，若要调整大小建议的操作，顾问提供当前和目标 SKU 信息。 

如果你想要更积极地识别使用不足的虚拟机, 你可以基于每个订阅调整 CPU 使用率规则。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>通过消除未设置的 ExpressRoute 线路来降低成本

顾问将识别提供程序状态为“未设置”长达一个月以上的 ExpressRoute 线路将被顾问标识，如果没有使用连接性提供程序配置该线路的计划，顾问将建议删除它。

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>通过删除或重新配置空闲虚拟网络网关来降低成本

顾问标识在超过90天内空闲的虚拟网络网关。 由于这些网关按小时计费，因此如果不打算再使用它们，则应考虑重新配置或删除它们。 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>购买虚拟机预留实例可节省即用即付成本

顾问将查看过去 30 天的虚拟机使用情况，并确定是否可以通过购买 Azure 预留来节省资金。 顾问将展示可能在其中最大程度节省资金的区域和大小，并展示通过购买预留节约下来的估算费用。 通过 Azure 预留，可以预先购买虚拟机的基本成本。 折扣将自动应用于新的或现有的 VM，这些 VM 具有与预留相同的大小和区域。 [深入了解 Azure 虚拟机预留实例。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

顾问还会向你通知在接下来的 30 天内将过期的预留实例。 它将建议你购买新的预留实例来避免即用即付定价。

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>删除未关联的公共 IP 地址可节省资金

顾问可以标识目前未关联到 Azure 资源（例如负载均衡器或 VM）的公共 IP 地址。 这些公共 IP 地址会产生少许费用。 如果不打算使用它们，删除它们可以节省成本。

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>删除失败的 Azure 数据工厂管道

Azure 顾问将检测重复失败的 Azure 数据工厂管道, 并建议解决这些问题, 或在不再需要时删除失败的管道。 即使这些管道发生故障, 也会对这些管道进行计费。 

## <a name="use-standard-snapshots-for-managed-disks"></a>使用托管磁盘的标准快照
为了节省 60% 的成本，我们建议将快照存储在标准存储中，而不考虑父磁盘的存储类型。 这是托管磁盘快照的默认选项。 Azure 顾问会识别存储高级存储的快照, 并建议将快照从高级存储迁移到标准存储。 [了解有关托管磁盘定价的详细信息](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>如何访问 Azure 顾问中的成本建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

2.  在顾问仪表板中，单击“成本”选项卡。

## <a name="next-steps"></a>后续步骤

若要了解有关顾问建议的详细信息，请参阅以下资源：
* [顾问简介](advisor-overview.md)
* [入门](advisor-get-started.md)
* [顾问性能建议](advisor-cost-recommendations.md)
* [顾问高可用性建议](advisor-cost-recommendations.md)
* [顾问安全性建议](advisor-cost-recommendations.md)
