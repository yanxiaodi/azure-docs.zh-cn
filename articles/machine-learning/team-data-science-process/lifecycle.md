---
title: Team Data Science Process 生命周期
description: 执行数据科学项目所需的步骤
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 40d1af6e6258b5026853532f7963a76d4fc389db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837874"
---
# <a name="the-team-data-science-process-lifecycle"></a>Team Data Science Process 生命周期

Team Data Science Process (TDSP) 提供可用于构建数据科学项目的建议生命周期。 生命周期概述了执行项目时，其从开始到结束所遵循的步骤。 如果使用另一数据科学生命周期，例如数据挖掘的跨行业标准过程 [(CRISP-DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)、数据库中的知识发现 [(KDD)](https://wikipedia.org/wiki/Data_mining#Process) 或组织自己的定制过程，仍可使用基于任务的 TDSP。 

此生命周期为数据科学项目而设计，这些项目旨在作为智能应用程序的一部分提供。 这些应用程序部署机器学习或人工智能模型以进行预测分析。 探索数据科学项目和即席分析项目也可从使用此过程获益。 但对于这些项目，可能并不需要这里描述的一些步骤。 

## <a name="five-lifecycle-stages"></a>五个生命周期阶段

TDSP 生命周期由 5 个以迭代方式执行的主要阶段组成。 这些阶段包括：

   1. [了解业务](lifecycle-business-understanding.md)
   2. [数据采集和理解](lifecycle-data.md)
   3. [建模](lifecycle-modeling.md)
   4. [部署](lifecycle-deployment.md)
   5. [客户验收](lifecycle-acceptance.md)

此处直观地展示了 TDSP 生命周期： 

![TDSP 生命周期](./media/lifecycle/tdsp-lifecycle2.png) 


将 TDSP 生命周期建模为一系列迭代步骤，这些步骤为使用预测模型所需的任务提供指导。 在计划使用的生产环境中部署预测模型以构建智能应用程序。 此过程生命周期的目标是将数据科学项目继续推动到明确的参与终点。 数据科学是研究和发现中的一项运用。 通过使用标准模板的定义完善的一系列项目，可以将任务传达给团队和客户，这有助于避免产生误解。 使用这些模板还可增加成功完成复杂数据科学项目的几率。

我们为每个阶段提供以下信息：

   * **目标**：具体目标。
   * **如何执行**：具体任务和有关如何完成这些任务的指导的概述。
   * **项目**：可交付结果和用于生成结果的支持。

## <a name="next-steps"></a>后续步骤

我们还提供了完整的演练，演示特定方案过程中的所有步骤。 [示例演练](walkthroughs.md)一文提供了包含链接和缩略图描述的方案列表。 该演练演示如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。 

有关如何在使用 Azure 机器学习工作室的 TDSP 中执行步骤的示例，请参阅[通过 Azure 机器学习使用 TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)。
