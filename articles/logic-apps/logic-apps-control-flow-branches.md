---
title: 创建或联接并行分支 - Azure 逻辑应用 | Microsoft Docs
description: 如何创建或联接 Azure 逻辑应用中的工作流的并行分支
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 2e1c155a371fa96e4f772f632a9585948b012e54
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685018"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>创建或联接 Azure 逻辑应用中的工作流的并行分支

默认情况下，逻辑应用工作流中的操作按顺序运行。 若要同时执行独立操作，可以创建[并行分支](#parallel-branches)，然后在流中[联接这些分支](#join-branches)。 

> [!TIP] 
> 如果你有接收数组的触发器并且希望针对每个数组项运行工作流，则可以使用 [**SplitOn** 触发器属性](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)“分离”  该数组。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。 

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>添加并行分支

若要同时运行独立的步骤，可以在现有步骤旁边添加平行分支。 

![并行运行步骤](media/logic-apps-control-flow-branches/parallel.png)

逻辑应用等待所有分支都完成后才继续执行工作流。 仅当并行分支的 `runAfter` 属性值匹配已完成父步骤的状态时，才运行并行分支。 例如，`branchAction1` 和 `branchAction2` 都设置为仅在 `parentAction` 以 `Succeeded` 状态完成时才运行。

> [!NOTE]
> 在开始之前，逻辑应用必须已有可添加平行分支的步骤。

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的逻辑应用设计器中打开逻辑应用。

1. 将指针悬停在要添加平行分支的步骤上方的箭头上。 选择出现的加号 (+)，然后选择“添加并行分支”    。 

   ![添加并行分支](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. 在搜索框中，查找并选择所需的操作。

   ![查找并选择所需的操作](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   现在，所选操作将在并行分支中显示，例如：

   ![查找并选择所需的操作](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. 现在，在每个并行分支中添加所需的步骤。 若要将其他操作添加到分支，请将指针移动到要添加顺序操作的操作下。 选择出现的加号 (+)，然后选择“添加操作”。   

   ![将顺序操作添加到并行分支](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. 在搜索框中，查找并选择所需的操作。

   ![查找并选择顺序操作](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   现在，所选操作将在当前分支中显示，例如：

   ![查找并选择顺序操作](media/logic-apps-control-flow-branches/added-sequential-action.png)

若要将分支重新合并在一起，请[联接平行分支](#join-branches)。 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>并行分支定义 (JSON)

如果正在代码视图中操作，可以改为在逻辑应用的 JSON 定义中定义并行结构，例如：

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>联接并行分支

若要将并行分支合并在一起，只需在所有分支的底部添加一个步骤。 此步骤在所有平行分支完成运行后运行。

![联接并行分支](media/logic-apps-control-flow-branches/join.png)

1. 在 [Azure 门户](https://portal.azure.com)中的逻辑应用设计器中查找并打开逻辑应用。 

1. 在想要联接的平行分支下，选择 **新建步骤**。 

   ![添加要联接的步骤](media/logic-apps-control-flow-branches/add-join-step.png)

1. 在搜索框中，查找并选择操作，作为联接分支的步骤。

   ![查找并选择联接平行分支的操作](media/logic-apps-control-flow-branches/join-steps.png)

   现在已合并并行分支。

   ![已联接的分支](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>联接定义 (JSON)

如果正在代码视图中操作，可以改为在逻辑应用的 JSON 定义中定义联接结构，例如：

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能和建议或者为其投票，请访问 [Azure 逻辑应用用户反馈站点](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* [基于条件运行步骤（条件语句）](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [基于不同的值运行步骤（switch 语句）](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [运行并重复执行步骤（循环）](../logic-apps/logic-apps-control-flow-loops.md)
* [基于分组的操作状态运行步骤（作用域）](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
