---
title: Azure 顾问入门 | Microsoft Docs
description: Azure 顾问入门。
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: f91e48a532a278c95d50775e135ac6379e8d8070
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332052"
---
# <a name="get-started-with-azure-advisor"></a>Azure 顾问入门

了解如何通过 Azure 门户访问顾问，以及如何获取和实现建议。

> [!NOTE]
> Azure 顾问自动在后台运行，以查找新创建的资源。 最多可能需要 24 小时才能提供有关这些资源的建议。

## <a name="get-recommendations"></a>获取建议

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 在左窗格中，单击“顾问”  。  如果左窗格中未显示“顾问”，请单击“所有服务”  。  在服务菜单窗格中，在“监视和管理”下，单击“顾问”   。 将显示顾问仪表板。

   ![使用 Azure 门户访问 Azure 顾问](./media/advisor-get-started/advisor-portal-menu.png) 

1. 顾问仪表板将显示针对所有所选订阅的建议的摘要。  可通过订阅筛选器下拉列表，选择要显示建议的订阅。

1. 若要获取特定类别的建议，请单击以下选项卡之一：“高可用性”、“安全”、“性能”或“成本”     。 

   ![Azure 顾问仪表板](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>获取建议详细信息并实现解决方案

可以在顾问中选择建议以查看详细信息 - 例如建议操作和受影响的资源 - 并实现针对建议的解决方案。  

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

1. 选择一个建议类别并显示该类别下的建议列表，或选择“全部”选项卡来查看所有建议  。

1. 单击要详细查看的建议。

1. 查看建议的相关信息，以及建议适用的资源。

1. 单击“推荐操作”以实现该建议  。

## <a name="filter-recommendations"></a>筛选器建议

可以通过筛选建议，深度获取最重要的信息。  可按订阅、资源类型或建议状态进行筛选。  

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

1. 使用顾问仪表板上的下拉列表，按订阅、资源类型或建议状态进行筛选。

    ![顾问搜索筛选条件](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>推迟或消除建议

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

1. 导航至要推迟或消除的建议。

1. 单击建议。

1. 单击“推迟”  。 

1. 指定推迟时间段，或选择“从不”  以消除该建议。

## <a name="exclude-subscriptions-or-resource-groups"></a>排除订阅或资源组

对于某些资源组或订阅，你可能不希望接收与其相关的顾问建议 - 例如“测试”资源。  可以将顾问配置为仅为特定订阅或资源组生成建议。

> [!NOTE]
> 只有订阅所有者可以向顾问添加或从中排除订阅或资源组。  如果不具有所需的订阅或资源组权限，则用户界面中的添加或排除选项为禁用状态。

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

1. 单击操作栏中的“配置”  。

1. 如果不想接收有关某个订阅或资源组的顾问建议，可取消选中它。

    ![顾问配置资源示例](./media/advisor-get-started/advisor-configure-resources.png)

1. 单击“应用”按钮。 

## <a name="configure-low-usage-vm-recommendation"></a>配置低使用率 VM 建议

此过程针对低使用率虚拟机建议，配置平均 CPU 使用率规则。

顾问可监视虚拟机 7 天的使用情况，并识别出利用率较低的虚拟机。 以下情况可以将虚拟机视为使用率低：其 CPU 使用率为 5% 或更低且其网络使用率低于 2%，或者更小的虚拟机大小可以容纳当前工作负荷。

若要加强对低使用率虚拟机的标识，可在每个订阅的基础上调整平均 CPU 使用率。  CPU 使用率规则可以设置为 5%、 10%、 15%或 20%。

> [!NOTE]
> 要调整用于标识低使用率虚拟机的平均 CPU 使用率规则，操作者必须是订阅所有者  。  如果缺少订阅或资源组所需的相关权限，则用户界面中的添加或排除选项是禁用的。 

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

1. 单击操作栏中的“配置”  。

1. 单击“规则”选项卡  。

1. 选择要为其调整平均 CPU 使用率规则的订阅，然后单击“编辑”  。

1. 选择所需的平均 CPU 使用率值，然后单击“应用”  。

1. 单击“刷新建议”来更新现有建议，以便使用新平均 CPU 使用率规则  。 

   ![顾问配置建议规则示例](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>下载建议

可使用顾问下载建议摘要。  可下载 PDF 文件或 CSV 文件格式的建议。  通过下载建议，可轻松与同事共享建议数据，或根据建议数据执行自己的分析。

1. 登录 [Azure 门户](https://portal.azure.com)，并打开[顾问](https://aka.ms/azureadvisordashboard)。

1. 在操作栏中，单击“以 CSV 格式下载”或“以 PDF 格式下载”   。

下载选项与在顾问仪表板中应用的筛选器密切相关。  如果在选择下载选项的同时，正在查看某个特定的建议类别或建议，则所下载的摘要将仅包括该类别或建议的相关信息。 

## <a name="next-steps"></a>后续步骤

若要详细了解顾问，请参阅以下资源：

- [Azure 顾问简介](advisor-overview.md)
- [顾问高可用性建议](advisor-high-availability-recommendations.md)
- [顾问安全性建议](advisor-security-recommendations.md)
- [顾问性能建议](advisor-performance-recommendations.md)
- [顾问成本建议](advisor-performance-recommendations.md)
