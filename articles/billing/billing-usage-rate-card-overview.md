---
title: 使用 Azure 计费 API 获取 Azure 使用情况 | Microsoft Docs
description: 了解 Azure 帐单用量和 RateCard API，洞察 Azure 资源消耗量和趋势。
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: f8a70c5cf233212412261eb48c85ae3a1dd6c94d
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718562"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>使用 Azure 计费 API 以编程方式洞察 Azure 用量
使用 Azure 计费 API 将用量和资源数据提取到偏好的数据分析工具。 Azure 资源用量和 RateCard API 可以帮助你准确预测及管理成本。 这些 API 作为资源提供程序实现，属于 Azure 资源管理器公开的 API 系列。  

## <a name="azure-invoice-download-api-preview"></a>Azure 发票下载 API（预览版）
[完成选择加入](billing-manage-access.md#opt-in)后，请使用[发票 API](/rest/api/billing) 预览版下载发票。 具体功能包括：

* **Azure 基于角色的访问控制** - 在 [Azure 门户](https://portal.azure.com)上或通过 [Azure PowerShell cmdlet](/powershell/azure/overview) 配置访问策略，指定哪些用户或应用程序有权访问订阅的用量数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。
* 日期筛选  - 使用 `$filter` 参数，在发票周期结束日期前按倒序顺序获取所有发票。

> [!NOTE]
> 此功能在初始预览版中推出，可能需进行无法向后兼容的更改。 目前，它不适用于某些订阅套餐（不支持 EA、CSP、AIO）和 Azure Germany。

## <a name="azure-resource-usage-api-preview"></a>Azure 资源使用状况 API（预览）
使用 Azure [资源用量 API](/previous-versions/azure/reference/mt219003(v=azure.100)) 获取预估的 Azure 耗用数据量。 该 API 包括：

* **Azure 基于角色的访问控制** - 在 [Azure 门户](https://portal.azure.com)上或通过 [Azure PowerShell cmdlet](/powershell/azure/overview) 配置访问策略，指定哪些用户或应用程序有权访问订阅的用量数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。
* **每小时或每日聚合** - 调用方可以指定 Azure 使用状况数据是以每小时存储桶为单位，还是以每日存储桶为单位。 默认值为每日聚合。
* **实例元数据（包括资源标记）** - 获取实例级详细信息，例如完全限定的资源 URI (/subscriptions/{subscription-id}/..)，以及资源组信息和资源标记。 对于交叉费用等用例，此元数据可帮助你明确以编程方式按标记分配使用状况。
* **资源元数据** - 资源详细信息（如测定仪名称、测定仪类别、测定仪子类别、计价单位和区域）让调用方更好地了解消耗量。 此外，我们还将致力于跨 Azure 门户、Azure 使用状况 CSV、EA 计费 CSV 和其他面向公众体验的情况下保持资源元数据术语的一致性，从而让你能够在不同体验间关联数据。
* **不同产品类型的用量** - 提供产品类型的用量数据，例如即用即付、MSDN、货币承诺、货币信用额和 EA（[CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription) 除外）。

## <a name="azure-resource-ratecard-api-preview"></a>Azure 资源价目表 API（预览）
使用 [Azure 资源 RateCard API](/previous-versions/azure/reference/mt219005(v=azure.100)) 获取可用 Azure 资源的列表，以及每个资源的估计定价信息。 该 API 包括：

* **Azure 基于角色的访问控制** - 在 [Azure 门户](https://portal.azure.com)上或通过 [Azure PowerShell cmdlet](/powershell/azure/overview) 配置访问策略，指定哪些用户或应用程序有权访问 RateCard 数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。
* **支持即用即付、MSDN、货币承诺和货币信用额产品（不支持 EA 和 [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card)）** - 此 API 提供 Azure 产品级费率信息。  此 API 的调用方必须传入产品/服务信息，才能获取资源详细信息和费率。 由于 EA 产品按注册自定义费率，因此我们暂时无法提供 EA 费率。

## <a name="scenarios"></a>方案
使用状况和价目表 API 组合可以实现下面一些方案：

* **Azure 月支出** - 将用量与 RateCard API 结合使用可以深入了解云的月支出。 可以分析用量和估计费用的每小时和每日存储桶。
* **设置警报** - 可将用量和 RateCard API 结合使用，估计云消耗量和费用，从而设置基于资源或资金的警报。
* **预测帐单** - 可以估计消耗量和云支出，应用机器学习算法来预测计费周期结束时的帐单。
* **消耗费用分析** - 如果要将工作负荷转移到 Azure，可以使用 RateCard API 来预测帐单（通过提供的所需用量数据）。 如果在其他云或私有云中拥有现有的工作负荷，则还可以将用量与 Azure 费率进行映射，以便更好地估计 Azure 支出。 通过这种估计可以根据套餐生成透视图，在不同的套餐类型（不仅仅局限于即用即付，还包括货币承诺和货币信用额）之间的对比和对照。 此外，API 还能够按区域查看费用差异，使你能够做假设性成本分析，帮助做出部署决策。
* **假设分析** -

  * 可以确定在其他区域或 Azure 资源的其他配置上运行工作负荷是否会更具成本效益。 Azure 资源费用根据所用的 Azure 区域而异。
  * 还可以确定其他 Azure 套餐类型是否提供更优惠的 Azure 资源费率。


## <a name="next-steps"></a>后续步骤
* 在 GitHub 上查看代码示例：
  * [发票 API 代码示例](https://go.microsoft.com/fwlink/?linkid=845124)

  * [使用情况 API 代码示例](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [RateCard API 代码示例](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* 若要了解有关 Azure 资源管理器的详细信息，请参阅 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。
