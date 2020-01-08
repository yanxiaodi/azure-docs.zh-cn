---
title: 示例-DSS-DSS 3.2.1 蓝图-部署步骤
description: 支付卡行业数据安全标准版3.2.1 蓝图示例的部署步骤。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 430cf7cde22cc8de337d33e1f083121503d084f5
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802344"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>部署 PCI-X 3.2.1 蓝图蓝图示例

若要部署 Azure 蓝图 pci-e pci-e 蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 基于示例创建新的蓝图
> - 将示例副本标记为“已发布”
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>基于示例创建蓝图

首先，通过使用示例作为起点在环境中创建新的蓝图，来实现蓝图示例。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。

1. 在_其他示例_下，找到**PCI-DSS v 3.2.1**蓝图示例，然后选择 "**使用此示例**"。

1. 输入该蓝图示例的“基本信息”：

   - **蓝图名称**：提供 PCI-DSS v2.0 蓝图蓝图示例的副本名称。
   - **定义位置**：使用省略号并选择要将示例副本保存到的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:项目”。

1. 查看构成蓝图示例的项目列表。 许多项目包含稍后我们将要定义的参数。 查看完蓝图示例后，选择“保存草稿”。

## <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其**发布**，然后才能分配和部署它。 可以根据您的环境和需要自定义蓝图示例的副本，但修改后可以将其移出 PCI-DSS v 3.2.1 标准版。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。 在右侧的新窗格中，提供蓝图示例副本的**版本**。 以后做出修改时，此属性非常有用。 提供如 "从 PCI-DSS v2.0 蓝图蓝图发布的第一个版本" 之类的**更改说明**。 然后选择页面底部的“发布”。

## <a name="assign-the-sample-copy"></a>分配示例副本

成功**发布**蓝图示例的副本后，可将它分配到它所在的管理组中的某个订阅。 在此步骤中，需提供参数来使蓝图示例副本的每个部署保持唯一。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择蓝图定义页面顶部的“分配蓝图”。

1. 提供蓝图分配的参数值：

   - 基本

     - **订阅**：在蓝图示例副本所保存到的管理组中选择一个或多个订阅。 如果选择多个订阅，将使用输入的参数为每个订阅创建一个分配。
     - **分配名称**：系统会根据蓝图的名称预先填充该名称。
       请根据需要更改该名称，或保留原样。
     - **位置**：选择要在其中创建托管标识的区域。 Azure 蓝图使用此托管标识在分配的蓝图中部署所有项目。 若要了解详细信息，请参阅 [Azure 资源的托管标识](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **蓝图定义版本**：选择蓝图示例副本的**已发布**版本。

   - 锁定分配

     选择环境的蓝图锁定设置。 有关更多信息，请参阅[蓝图资源锁定](../../concepts/resource-locking.md)。

   - 托管标识

     保留默认的系统分配的托管标识选项。

   - 项目参数

     在本部分定义的参数将应用到定义了这些参数的项目。 这些参数属于[动态参数](../../concepts/parameters.md#dynamic-parameters) ，因为它们是在分配蓝图期间定义的。 有关完整列表或项目参数及其说明，请参阅[项目参数表](#artifact-parameters-table) 。

1. 输入所有参数后，选择页面底部的“分配”。 随后将创建蓝图分配，并开始部署项目。 部署过程大约需要一小时。 若要检查部署状态，请打开蓝图分配。

> [!WARNING]
> Azure 蓝图服务和内置蓝图示例是**免费的**。 Azure 资源[按产品定价](https://azure.microsoft.com/pricing/) 。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/) 可以估算运行此蓝图示例部署的资源所需的成本。

## <a name="artifact-parameters-table"></a>项目参数表

下表提供了蓝图项目参数的列表：

|项目名称|项目类型|参数名称|描述|
|-|-|-|-|
|\[预览版\] audit pci-x 3.2.1：2018控制和部署特定 VM 扩展以支持审核要求|策略分配|资源类型列表 | 所选资源类型的审核诊断设置。 默认值为 "所有资源"| 
|允许的位置|策略分配|允许的位置列表|允许部署到其中的任何资源的数据中心位置的列表。 此列表可在全球范围内自定义到所需的 Azure 位置。 选择要允许的位置。| 
|允许的资源组位置|策略分配 |允许的位置 |此策略使你能够限制你的组织可在中创建资源组的位置。 用于强制执行异地符合性要求。| 
|在 SQL Server 上部署审核|策略分配|保留天数|数据保留，单位为天。 默认值为180，但 PCI 需要365。| 
|在 SQL Server 上部署审核|策略分配|存储帐户的资源组名称|审核针对 Azure 存储帐户（将在 SQL Server 所在的每个区域中创建的存储帐户，由该区域中的所有服务器共享）中审核日志的写入数据库事件。| 

## <a name="next-steps"></a>后续步骤

现在，你已经查看了用于部署 PCI-X 3.2.1 蓝图蓝图示例的步骤，接下来请访问以下文章来了解概述和控件映射：

> [!div class="nextstepaction"]
> [Pci-dss v2.0 3.2.1 蓝图-概述](./index.md)
> [PCI-dss v2.0 蓝图-控件映射](./control-mapping.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
