---
title: 示例-加拿大联邦 PBMM 蓝图-部署步骤
description: 部署 theCanada 联邦 PBMM 蓝图示例的步骤。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/05/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b5cf0cf5dc8a0964d981c5537b6fa41f1c6c2058
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968492"
---
# <a name="deploy-the-canada-federal-pbmm-blueprint-samples"></a>部署加拿大联邦 PBMM 蓝图示例

若要部署加拿大联邦 PBMM 蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 基于示例创建新的蓝图
> - 将示例副本标记为“已发布”
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>基于示例创建蓝图

首先，通过使用示例作为起点在环境中创建新的蓝图，来实现蓝图示例。

1. 选择“所有服务”，然后在左窗格中搜索并选择“策略”。 在“策略”页上选择“蓝图”。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。

1. 在_其他示例_下找到**加拿大联邦 PBMM**蓝图示例，然后选择 "**使用此示例**"。

1. 输入该蓝图示例的“基本信息”：

   - **蓝图名称**：提供蓝图示例副本的名称。
   - **定义位置**：使用省略号并选择要将示例副本保存到的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:项目”。

1. 查看构成蓝图示例的项目列表。 许多项目包含稍后我们将要定义的参数。 查看完蓝图示例后，选择“保存草稿”。

## <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其**发布**，然后才能分配和部署它。 可以根据您的环境和需要自定义蓝图示例的副本, 但这种修改可能会远离标准版本。

1. 选择“所有服务”，然后在左窗格中搜索并选择“策略”。 在“策略”页上选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。 在右侧的新窗格中，提供蓝图示例副本的**版本**。 以后做出修改时，此属性非常有用。 提供一些**更改注释**，如 "从加拿大联邦 PBMM 蓝图示例发布的第一个版本"。 然后选择页面底部的“发布”。

## <a name="assign-the-sample-copy"></a>分配示例副本

成功**发布**蓝图示例的副本后，可将它分配到它所在的管理组中的某个订阅。 在此步骤中，需提供参数来使蓝图示例副本的每个部署保持唯一。

1. 选择“所有服务”，然后在左窗格中搜索并选择“策略”。 在“策略”页上选择“蓝图”。

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

项目名称|项目类型|参数名称|描述|
|-|-|-|-|
|\[预览\]：为 Linux VM 部署 Log Analytics 代理 |策略分配 |Linux VM 的 Log Analytics 工作区 |有关详细信息, 请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../../../azure-monitor/learn/quick-create-workspace.md)。 |
|\[预览\]：为 Linux VM 部署 Log Analytics 代理 |策略分配 |可选：支持将 Linux OS 添加到范围的 VM 映像列表 |空数组可用于指示没有可选参数：`[]` |
|\[预览\]：为 Windows VM 部署 Log Analytics 代理 |策略分配 |可选：支持将 Windows OS 添加到范围的 VM 映像列表 |空数组可用于指示没有可选参数：`[]` |
|\[预览\]：为 Windows VM 部署 Log Analytics 代理 |策略分配 |Windows VM 的 Log Analytics 工作区 |有关详细信息, 请参阅[在 Azure 门户中创建 Log Analytics 工作区](../../../../azure-monitor/learn/quick-create-workspace.md)。 |
|\[预览\]：审核加拿大联邦 PBMM 控件并部署特定的 VM 扩展以支持审核要求 |策略分配 |应为 VM 配置的 Log Analytics 工作区 ID |这是应为 VM 配置的 Log Analytics 工作区的 ID (GUID)。 |
|\[预览\]：审核加拿大联邦 PBMM 控件并部署特定的 VM 扩展以支持审核要求 |策略分配 |应启用诊断日志的资源类型列表 |如果未启用诊断日志设置，则为要审核的资源类型的列表。 [Azure Monitor 诊断日志架构](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)中提供了可接受的值。 |
|\[预览\]：审核加拿大联邦 PBMM 控件并部署特定的 VM 扩展以支持审核要求 |策略分配 |Administrators 组 |组. 示例： `Administrator; myUser1; myUser2` |
|\[预览\]：审核加拿大联邦 PBMM 控件并部署特定的 VM 扩展以支持审核要求 |策略分配 |应该包括在 Windows VM 管理员组中的用户的列表 |以分号分隔的应包括在管理员本地组中的成员列表。 示例： `Administrator; myUser1; myUser2` |
|在存储帐户上部署高级威胁防护 |策略分配 |效果 |有关策略效果的信息，请参阅[了解 Azure 策略影响](../../../policy/concepts/effects.md)。 |
|在 SQL Server 上部署审核 |策略分配 |保持期的值（天数，0 表示保持期无限制） |保留天数（可选，如果未指定，则为_180_天） |
|在 SQL Server 上部署审核 |策略分配 |要进行 SQL Server 审核的存储帐户的资源组名称 |审核将数据库事件写入 Azure 存储帐户中的审核日志（在创建 SQL Server 的每个区域中创建存储帐户，该区域由该区域中的所有服务器共享）。 重要提示：对于正确的审核操作，请不要删除或重命名资源组或存储帐户。 |
|为网络安全组部署诊断设置 |策略分配 |适用于网络安全组诊断的存储帐户前缀 |此前缀与网络安全组位置组合在一起以形成创建的存储帐户名称。 |
|为网络安全组部署诊断设置 |策略分配 |适用于网络安全组诊断的存储帐户的资源组名称（必须存在） |在其中创建存储帐户的资源组。 此资源组必须已存在。 |

## <a name="next-steps"></a>后续步骤

现在，你已查看部署加拿大联邦 PBMM 示例的步骤，请访问以下文章了解概述和控件映射：

> [!div class="nextstepaction"]
> [加拿大联邦 PBMM 蓝图-概述](./index.md)
> [加拿大联邦 PBMM 蓝图-控件映射](./control-mapping.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。