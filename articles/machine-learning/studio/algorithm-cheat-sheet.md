---
title: 机器学习算法备忘单
titleSuffix: Azure Machine Learning Studio
description: 可打印的机器学习算法备忘单可帮助在 Azure 机器学习工作室中为预测模型选择正确的算法。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: 51a743e7578ea5bbc2acb9094bbf704a09f3cd6a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60752075"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-studio"></a>Azure 机器学习工作室机器学习算法备忘单

**Azure 机器学习工作室算法备忘单**可帮助为预测分析模型选择合适的算法。

[Azure 机器学习工作室](https://studio.azureml.net/)拥有一个大型算法库，包括回归、分类、聚类分析和异常检测系列。 每一类算法都可用于解决一种类型的机器学习问题。

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>下载：机器学习算法备忘单

**在此下载备忘单：[机器学习算法备忘单(11 x 17 英寸)](https://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v7.pdf)**

![机器学习算法备忘单：了解如何选择机器学习算法。](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png)

下载该机器学习工作室算法备忘单，并将其打印为 Tabloid 大小，既方便携带又可帮助你选择算法。

> [!NOTE]
> 有关如何使用此备忘单选择合适算法的帮助，以及不同类型的机器学习算法及其使用方式的深入探讨，请参阅[如何选择 Microsoft Azure 机器学习工作室算法](algorithm-choice.md)。

## <a name="notes-and-terminology-definitions-for-the-machine-learning-studio-algorithm-cheat-sheet"></a>机器学习工作室算法备忘单的说明和术语定义

* 此算法备忘单中提供的建议近似于经验法则。 一些可以不完全照做，一些可以大胆地违反。 它旨在建议一个起点。 不要担心几种算法之间对数据运行正面竞争。 没有只是没有什么可以替代理解每种算法并生成你的数据的系统的原则。

* 每种机器学习算法都有自己的样式或*归纳偏差*。 对于特定问题，可能有几种算法合适，但会有一种算法可能比其他算法更合适。 但并非总是可以预先知道哪种是最合适的。 在这些情况下，会在备忘单中列出几种算法。 适当的策略是尝试一种算法，如果结果尚不令人满意，则尝试其他算法。 下面是 [Azure AI 库](https://gallery.azure.ai/)中的一个试验示例。该试验对相同数据尝试多种算法并对结果进行比较：[比较多类分类器：字母识别](https://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92)。

* 有三种主要类别的机器学习：**监督式学习**、**非监督式学习**和**强化学习**。

  * 在**监督式学习**中，将标记每个数据点或将其与某个类别或相关值相关联。  分类标签的示例是将图像分配为“猫”或“狗”。  值标签的示例是与二手车关联的销售价格。 监督式学习的目的是研究大量类似这样的标记示例，并能够对未来的数据点进行预测。 例如，识别包含正确动物的新照片或为其他二手车指定准确的销售价格。 这是一种常用且有用的机器学习类型。 除 [K-Means 群集][k-means-clustering]外，Azure 机器学习工作室中的所有其他模块都采用监督式学习算法。

  * 在**非监督式学习**中，数据点没有与其关联的标签。 相反，非监督式学习算法的目的是以某种方式组织数据或者说明其结构。 这意味着将其分组到群集（如 K-means 所实现）或查找不同的方法来查看复杂数据以使其显示更简单。

  * 在**强化学习**中，算法需选择响应每个数据点的操作。 它是机器人学中的常见方法，在此技术中一个时间点的传感器读数集是数据点，并且算法必须选择机器人的下一个动作。 它也是物联网应用程序的理想选择。 学习算法还会在短时间后收到奖励信号，指示决策的优秀程度。 在此基础上，算法会修改其战略议获得最高奖励。 目前，Azure 机器学习工作室中暂无强化学习算法模块。

* **贝叶斯方法**对统计上独立的数据点做出假设。 这意味着一个数据点中的未建模变化与其他数据点不相关，也就是说，无法预测。 例如，如果所记录的数据是下一班地铁到达之前的分钟数，将一天分开的两个度量值在统计上是不相关的。 但是，将一分钟分开的两个度量值在统计上不是不相关的 - 一个的值能够高度预测另一个的值。

* **提升决策树回归**利用了功能重叠或功能间的交互。 这意味着，在任何给定的数据点，在某种程度上能够根据一个功能的值预测另一个功能的值。 例如，在每日高/低温度数据中，知道一天的低温度让可以合理地猜测该天的高温度。 这两个功能中包含的信息在某种程度上是冗余的。

* 对数据分类到两个以上的类别可以使用的本质上是多类分类器，或通过将一组双类分类器组合完成**系综**。 在集合方法中，每个类有一个单独的双类分类器 - 每个分类器将数据分成两个类别：“此类”和“非此类”。 然后，这些分类器为数据点是否正确分配投票。 这是 [One-vs-All Multiclass][one-vs-all-multiclass] 背后的操作原理。

* 包括逻辑回归和贝叶斯点机在内的多种方法都假定**线性类边界**。 也就是说，它们假设类之间的边界近似于直线（或在更普遍的情况下是超平面）。 通常这是在尝试分隔它之后才知道的数据特征，但这通常可以通过事先进行可视化来了解。 如果类边界看起来非常不规则，可继续使用决策树、决策森林、支持向量机或神经网络。

* 可以将神经网络与类别变量配合使用，方法是为每个类别创建一个**虚拟变量**，并在类别适用时将该变量设置为 1，不适用时将该变量设置为 0。

## <a name="next-steps"></a>后续步骤

* 有关说明算法并提供示例的可下载信息图，请参阅[可下载的信息图：包含算法示例的机器学习基础知识](basics-infographic-with-algorithm-examples.md)。

* 有关机器学习工作室中所有可用的机器学习算法的种类列表，请参阅“机器学习工作室算法和模块帮助”中的[初始化模型][initialize-model]。

* 有关机器学习工作室中算法和模块的完整列表（按字母顺序排列），请参阅“机器学习工作室算法和模块帮助”中的[机器学习工作室模块 A-Z 列表][a-z-list]。



<!-- Module References -->
[a-z-list]: /azure/machine-learning/studio-module-reference/a-z-module-list
[initialize-model]: /azure/machine-learning/studio-module-reference/machine-learning-initialize-model
[k-means-clustering]: /azure/machine-learning/studio-module-reference/k-means-clustering
[one-vs-all-multiclass]: /azure/machine-learning/studio-module-reference/one-vs-all-multiclass
