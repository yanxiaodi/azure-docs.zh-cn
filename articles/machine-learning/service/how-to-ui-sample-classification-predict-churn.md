---
title: '视觉对象接口示例 #5：用于预测流失的分类 + 亲和力 + 向上销售'
titleSuffix: Azure Machine Learning
description: 此视觉对象接口示例试验显示了对变动的二进制分类器预测，这是客户关系管理（CRM）的常见任务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 260d94ddf2572979e819ee89dfcbd315ef3c4769
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131930"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>示例 5-分类：预测变动率、亲和力和向上销售 

了解如何在不编写代码的情况下使用可视界面构建复杂的机器学习试验。

此试验训练三**个双类提升决策树**分类器，用于预测客户关系管理（CRM）系统的常见任务：改动、亲和力和向上销售。 数据值和标签跨多个数据源进行拆分，并打乱编码为匿名的客户信息，但是，我们仍然可以使用可视界面合并数据集，并使用打乱的值来训练模型。

因为你要尝试回答问题 "哪一个？" 这称为分类问题，但你可以在此项目中应用相同的逻辑来解决任何类型的机器学习问题，无论是回归、分类、群集等。

下面是此试验的完成关系图：

![试验图](./media/how-to-ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. 对于示例5试验，请选择 "**打开**" 按钮。

    ![打开试验](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

此实验的数据来自 KDD 杯2009。 它包含50000行和230功能列。 该任务旨在预测使用这些功能的客户的变动量、亲和力和向上销售情况。 有关数据和任务的详细信息，请参阅[KDD 网站](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)。

## <a name="experiment-summary"></a>试验摘要

此可视化界面示例试验显示了对变动、亲和力和向上销售的二元分类器预测，这是客户关系管理（CRM）的常见任务。

首先，执行一些简单的数据处理。

- 原始数据集包含大量缺少值。 使用 "**清理缺失数据**" 模块将缺失值替换为0。

    ![清理数据集](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- 功能和相应的改动、亲和力以及销售标签位于不同的数据集中。 使用 "**添加列**" 模块将标签列追加到特征列。 第一列**Col1**是标签列。 其余列、 **Var1**、 **Var2**等是功能列。

    ![添加列数据集](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- 使用 "**拆分数据**" 模块将数据集拆分为定型集和测试集。

    然后，将提升决策树二进制分类器与默认参数一起使用，以生成预测模型。 为每个任务生成一个模型，即每个模型预测向上销售、亲和力和变动。

## <a name="results"></a>结果

可视化 "**评估模型**" 模块的输出，以查看该模型在测试集上的性能。 对于向上销售的任务，ROC 曲线显示模型确实比随机模型更好。 曲线（AUC）下的区域为0.857。 阈值为0.5，精度为0.7，召回为0.463，F1 分数为0.545。

![评估结果](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 可以移动 "**阈值**" 滑块，并查看二元分类任务的指标变化。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

浏览可用于可视界面的其他示例：

- [示例 1-回归：预测汽车的价格](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [示例 2-回归：比较汽车价格预测的算法](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [示例 3-分类：预测信用风险](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [示例 4-分类：预测信用风险（区分成本）](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [示例 6-分类：预测航班延迟](how-to-ui-sample-classification-predict-flight-delay.md)
