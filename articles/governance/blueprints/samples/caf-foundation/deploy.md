---
title: 示例 - CAF 基础蓝图 - 部署步骤
description: CAF 基础蓝图示例的部署步骤。
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: 98ade4e5c8a14c2d4f5c2a22c2c973fac844861f
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802553"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>部署适用于 Azure 的 Microsoft 云采用框架基础蓝图示例

若要部署适用于 Azure 的 Microsoft 云采用框架 (CAF) 基础蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 基于示例创建新的蓝图
> - 将示例副本标记为“已发布” 
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>基于示例创建蓝图

首先，通过使用示例作为起点在环境中创建新的蓝图，来实现蓝图示例。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。   

1. 在“其他示例”下找到“CAF 基础”蓝图示例，然后选择“使用此示例”。   

1. 输入该蓝图示例的“基本信息”： 

   - **蓝图名称**：提供“CAF 基础”蓝图示例副本的名称。
   - **定义位置**：使用省略号并选择要将示例副本保存到的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:   项目”。

1. 查看构成蓝图示例的项目列表。 许多项目包含稍后我们将要定义的参数。 查看完蓝图示例后，选择“保存草稿”。 

## <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其**发布**，然后才能分配和部署它。  可根据环境和需求自定义蓝图示例的副本，但这种修改可能会使该副本偏离 CAF 基础蓝图。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧选择“蓝图定义”页。  使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。  在右侧的新窗格中，提供蓝图示例副本的**版本**。 以后做出修改时，此属性非常有用。 提供**更改注释**，例如，“基于 CAF 基础蓝图示例发布的第一个版本”。 然后选择页面底部的“发布”。 

## <a name="assign-the-sample-copy"></a>分配示例副本

成功**发布**蓝图示例的副本后，可将它分配到它所在的管理组中的某个订阅。 在此步骤中，需提供参数来使蓝图示例副本的每个部署保持唯一。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧选择“蓝图定义”页。  使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择蓝图定义页面顶部的“分配蓝图”。 

1. 提供蓝图分配的参数值：

   - 基础
       - **订阅**：在蓝图示例副本所保存到的管理组中选择一个或多个订阅。 如果选择多个订阅，将使用输入的参数为每个订阅创建一个分配。
     - **分配名称**：系统会根据蓝图的名称预先填充该名称。
       请根据需要更改该名称，或保留原样。
     - **位置**：选择要在其中创建托管标识的区域。
     - Azure 蓝图使用此托管标识在分配的蓝图中部署所有项目。
       若要了解详细信息，请参阅 [Azure 资源的托管标识](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **蓝图定义版本**：选择蓝图示例副本的**已发布**版本。

   - 锁分配

     选择环境的蓝图锁定设置。 有关更多信息，请参阅[蓝图资源锁定](../../concepts/resource-locking.md)。

   - 托管标识

     选择默认的_系统分配_的托管标识选项或_用户分配_的标识选项。

   - 蓝图参数

     蓝图定义中的许多项目使用本部分定义的参数来提供一致性。

     - **组织**：输入组织名称（例如 Contoso），必须唯一。
     - **Azure 区域**：选择要部署的 Azure 区域。
     - **允许的位置**：你将允许哪些 Azure 区域内置资源？
     
   - 项目参数

     在本部分定义的参数将应用到定义了这些参数的项目。 这些参数属于[动态参数](../../concepts/parameters.md#dynamic-parameters) ，因为它们是在分配蓝图期间定义的。 有关完整列表或项目参数及其说明，请参阅[项目参数表](#artifact-parameters-table) 。

1. 输入所有参数后，选择页面底部的“分配”。  随后将创建蓝图分配，并开始部署项目。 部署过程大约需要一小时。 若要检查部署状态，请打开蓝图分配。

> [!WARNING]
> Azure 蓝图服务和内置蓝图示例是**免费的**。 Azure 资源[按产品定价](https://azure.microsoft.com/pricing/) 。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/) 可以估算运行此蓝图示例部署的资源所需的成本。

## <a name="artifact-parameters-table"></a>项目参数表

下表提供了蓝图项目参数的列表：

|项目名称|项目类型|参数名称|说明|
|-|-|-|-|
|允许的存储帐户 SKU|策略分配|Policy_Allowed-StorageAccount-SKUs|诊断日志存储帐户中使用的 SKU|
|允许的虚拟机 SKU|策略分配|Policy_Allowed-VM-SKUs|允许的虚拟机 SKU|
|将 CostCenter 标记追加到资源组|策略分配|Policy_CostCenter_Tag|从资源组追加 CostCenter 标记及其值|
|你不想在环境中允许的资源类型|策略分配|Policy _Allowed-Resource-Types|你希望在环境中允许哪些 Azure 资源|
|部署 Key Vault|资源管理器模板|KV-AccessPolicy|**已锁定** - 在 Key Vault 中向其授予权限的 Azure AD 组或用户 <Object ID>|
|部署 Log Analytics|资源管理器模板|LogAnalytics_DataRetention|**已锁定** - 数据将在 Log Analytics 中保留的天数|
|部署 Log Analytics|资源管理器模板|LogAnalytics_Location|**已锁定** - 建立工作区时使用的区域|

## <a name="next-steps"></a>后续步骤

查看了部署 CAF 基础蓝图示例的步骤后，请访问以下文章来了解体系结构：

> [!div class="nextstepaction"]
> [CAF 基础蓝图 - 概述](./index.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。