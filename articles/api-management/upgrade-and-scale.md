---
title: 升级和缩放 Azure API 管理实例 | Microsoft Docs
description: 本主题介绍如何升级和缩放 Azure API 管理实例。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018237"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>升级和缩放 Azure API 管理实例  

客户可通过添加和删除单位来缩放 Azure API 管理 (APIM) 实例。 “单位”由专用的 Azure 资源构成，具有以每月 API 调用次数表示的承载容量。 此数字不表示调用限制，而是粗略容量规划允许的最大吞吐量值。 实际吞吐量和延迟因多种因素而异，例如并发连接的数量和速率、配置策略的类型和数量、请求和响应大小以及后端延迟。

每个单位的容量和价格取决于该单位所在的“层”。 可以四个层之间进行选择：开发人员、基本、标准和高级。 如果需要提高层中服务的容量，应添加单位。 如果当前在 APIM 实例中选择的层不允许添加更多单位，则需要升级到更高级别的层。

每个单位的价格和可用的功能（例如多区域部署）取决于为 APIM 实例选择的层。 [定价详细信息](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)一文介绍了每个单位的价格，以及每个层提供的功能。 

>[!NOTE]
>[定价详细信息](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)一文说明了每个层中单位容量的大致数字。 若要获取更准确的数字，需要分析所用 API 的实际方案。 请参阅 [Azure API 管理实例的容量](api-management-capacity.md)一文。

## <a name="prerequisites"></a>先决条件

若要执行本文中的步骤，必须：

+ 拥有一个有效的 Azure 订阅。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ 有一个 APIM 实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。

+ 了解 [Azure API 管理实例容量](api-management-capacity.md)的概念。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>升级和缩放  

可以四个层之间进行选择：开发人员、基本、标准和高级。 应将“开发人员”层用于评估服务；不应将其用于生产。 “开发人员”层不附带 SLA，无法缩放此层（添加/删除单位）。 

“基本”、“标准”和“高级”是附带 SLA 的生产层，可以缩放。 “基本”层是附带 SLA 的最便宜层，最多可以扩展 2 个单位，“标准”层最多可以扩展到四个单位。 可将任意数目的单位添加到“高级”层。

使用“高级”层可在任意数目的所需 Azure 区域中分配单个 Azure API 管理实例。 最初创建 Azure API 管理服务时，实例仅包含一个单位，并驻留在单个 Azure 区域中。 初始区域被指定为“主要”区域。 可以轻松添加更多区域。 添加区域时，需指定想要分配的单位数。 例如，可在“主要”区域中包含一个单位，在另外某个区域中包含五个单位。 可以定制用于处理每个区域中的流量的单位数。 有关详细信息，请参阅[如何将 Azure API 管理服务实例部署到多个 Azure 区域](api-management-howto-deploy-multi-region.md)。

可在任意两个层之间来回升级和降级。 请注意，升级或降级可能会删除某些功能，例如，从“高级”层降级到“标准”或“基本”层时，会删除 VNET 或多区域部署。

> [!NOTE]
> 升级或缩放过程可能需要 15 到 45 分钟才能完成。 完成时会收到通知。

> [!NOTE]
> **消耗**层中的 API 管理服务根据流量自动缩放。

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>使用 Azure 门户进行升级和缩放

![在 Azure 门户中缩放 APIM](./media/upgrade-and-scale/portal-scale.png)

1. 在 [Azure 门户](https://portal.azure.com/)中导航到自己的 APIM 实例。
2. 从菜单中选择“规模和定价”。
3. 选择所需的层。
4. 指定想要添加的“单位”数。 可以使用滑块，或键入单位数。  
    如果选择“高级”层，则首先需要选择一个区域。
5. 按“保存”。

## <a name="downtime-during-scaling-up-and-down"></a>增加和减少过程中的停机时间
如果要从或向开发人员层进行缩放, 将会出现停机时间。 否则, 不会造成停机。 


## <a name="next-steps"></a>后续步骤

- [如何将 Azure API 管理服务实例部署到多个 Azure 区域](api-management-howto-deploy-multi-region.md)
- [如何自动缩放 Azure API 管理服务实例](api-management-howto-autoscale.md)
