---
title: 从 Excel 迁移分析
titleSuffix: Azure Machine Learning Studio
description: Excel 中和 Azure 机器学习工作室中的线性回归模型比较
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7db66f6f4efa5e48f2af9380115de8bcfb75cb86
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "67786671"
---
# <a name="migrate-analytics-from-excel-to-azure-machine-learning-studio"></a>将 Excel 中的分析信息迁移到 Azure 机器学习工作室

> *Kate Baroni* 和 *Ben Boatman* 是 Microsoft 数据科学卓越中心中的企业解决方案架构师。 在本文中，他们将会介绍使用 Azure 机器学习工作室将现有的回归分析套件迁移到基于云的解决方案的经验。

## <a name="goal"></a>目标

我们的项目开始时有两个目标： 

1. 使用预测分析来提高我们组织每月收入预测的准确性 
2. 使用 Azure 机器学习工作室确认、优化、加快我们的成果速度和规模。 

与许多企业一样，我们的组织每月也会经历收入预测过程。 我们的业务分析师小组的任务就是：使用 Azure 机器学习工作室来支持过程并改进预测的准确性。 小组花费了几个月的时间从多个源中收集数据，并通过标识与服务销售预测相关的重要属性的统计分析来运行数据属性。 后续步骤是要开始在 Excel 中为数据建立统计回归模型的原型。 在几个星期内，我们便有了 Excel 回归模型，其效果优于当前字段和财务预测过程。 这也成为预测结果的基线。 

然后，我们采取了下一步，将我们的预测分析转移到工作室，以了解工作室如何改进预测性能。

## <a name="achieving-predictive-performance-parity"></a>实现预测性能奇偶校验
我们最优先的任务是实现工作室和 Excel 回归模型之间的奇偶校验。 对我们想要在 Excel 和工作室之间实现预测性能奇偶校验的训练和测试数据，指定相同的数据，以及相同的拆分。 最初我们失败了。 Excel 模型的性能优于工作室模型。 失败的原因是由于对工作室中基本工具设置缺乏理解。 在与工作室产品团队同步讨论后，我们得以更好地理解了数据集所需的基本设置，并实现了两个模型之间的奇偶校验。 

### <a name="create-regression-model-in-excel"></a>在 Excel 中创建回归模型
我们的 Excel 回归使用 Excel 分析工具库中找到的标准线性回归模型。 

我们计算出*平均绝对百分比误差*，并将其用作模型的性能度量。 花费了 3 个月的时间来实现使用 Excel 的工作模型。 我们将许多学习经验带到工作室试验中，最终有益于了解相关要求。

### <a name="create-comparable-experiment-in-studio"></a>在工作室中创建可比较的试验
我们已遵循以下步骤在工作室中创建了试验： 

1. 将数据集以 csv 文件上传到工作室（非常小的文件）
2. 创建新实验, 并使用 "[选择数据集中的列][select-columns]" 模块来选择 Excel 中使用的相同数据功能 
3. 使用[拆分数据][split]模块 (使用*相对表达式*模式) 将数据划分为与在 Excel 中所执行的相同定型数据集 
4. 带有[线性回归][linear-regression]模块的 vspackage (仅限默认选项), 记录并将结果与我们的 Excel 回归模型进行比较

### <a name="review-initial-results"></a>查看初始结果
起初，Excel 模型的性能明显优于工作室模型： 

|  | Excel | 工作室 |
| --- |:---:|:---:|
| 性能 | | |
| <ul style="list-style-type: none;"><li>调整 R 平方</li></ul> |0.96 |不可用 |
| <ul style="list-style-type: none;"><li>决定 <br />系数</li></ul> |不可用 |0.78<br />（低准确性） |
| 平均绝对误差 |$9.5M |$ 19.4 M |
| 平均绝对误差 (%) |6.03% |12.2% |

当我们向机器学习团队的开发人员和数据科学家运行我们的过程和结果时，他们快速提供了一些有用的提示。 

* 当你在工作室中使用[线性回归][linear-regression]模块时, 将提供两种方法:
  * 在线梯度下降：可能更适合于大规模的问题
  * 普通最小二乘法：这是大多数人在听到线性回归时会想到的方法。 对于小型数据集，普通最小二乘法是较好的选择。
* 考虑调整 L2 正则化权重参数，以提高性能。 默认情况下，其设置为 0.001，而对于我们的小型数据集，将其设置为 0.005 以提高性能。 

### <a name="mystery-solved"></a>谜题解决了！
在我们应用建议时，我们在工作室中实现了与 Excel 的相同基线性能： 

|  | Excel | 工作室（初始） | 工作室（最小二乘法） |
| --- |:---:|:---:|:---:|
| 有标签的值 |实际值（数值） |相同 |相同 |
| 学习器 |Excel -> 数据分析 -> 回归 |线性回归。 |线性回归 |
| 学习器选项 |不可用 |默认值 |普通最小二乘法<br />L2 = 0.005 |
| 数据集 |26 行、3 个功能、1 个标签。 所有数字。 |相同 |相同 |
| 拆分：训练 |Excel 在前 18 行进行训练，在最后 8 行进行测试。 |相同 |相同 |
| 拆分：测试 |Excel 回归公式应用于最后 8 行 |相同 |相同 |
| **性能** | | | |
| 调整 R 平方 |0.96 |不可用 | |
| 决定系数 |不可用 |0.78 |0.952049 |
| 平均绝对误差 |$9.5M |$ 19.4 M |$9.5M |
| 平均绝对误差 (%) |<span style="background-color: 00FF00;"> 6.03%</span> |12.2% |<span style="background-color: 00FF00;"> 6.03%</span> |

此外，Excel 系数相较于 Azure 训练模型中的功能权重不相上下：

|  | Excel 系数 | Azure 功能权重 |
| --- |:---:|:---:|
| 截距/偏差 |19470209.88 |19328500 |
| 功能 A |0.832653063 |0.834156 |
| 功能 B |11071967.08 |11007300 |
| 功能 C |25383318.09 |25140800 |

## <a name="next-steps"></a>后续步骤
我们想要在 Excel 内使用机器学习 Web 服务。 我们的业务分析人员依靠 Excel，而我们需要一种方法来调用机器学习 Web 服务和 Excel 数据，并使其将预测的值返回到 Excel。 

我们还想使用工作室中可用的选项和算法来优化模型。

### <a name="integration-with-excel"></a>与 Excel 集成
我们的解决方案是：通过从训练模型中创建 Web 服务，使我们的机器学习回归模型实现可操作化。 在数分钟内，Web 服务即已创建，我们可以直接从 Excel 进行调用，以返回预测的收入值。 

*Web 服务仪表板*部分包括可下载的 Excel 工作簿。 该工作簿已使用 Web 服务 API 进行预格式化，并嵌入了架构信息。 单击“下载 Excel 工作簿”时，工作簿会打开，用户可以将其保存到本地计算机。 

![从 Web 服务面板下载 Excel 工作簿](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png)

在工作簿打开时，请将预定义的参数复制到蓝色的 Parameter 部分，如下所示。 一旦输入这些参数，Excel 即会调用机器学习 Web 服务，而且会在绿色预测值部分显示预测的评分标签。 该工作簿会根据在 Parameters 下输入的所有行项，继续根据训练模型创建预测。 有关如何使用此功能的详细信息，请参阅[从 Excel 使用 Azure 机器学习 Web 服务](consuming-from-excel.md)。 

![模板 Excel 工作簿连接到已部署的 web 服务](./media/linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png)

### <a name="optimization-and-further-experiments"></a>优化和进一步实验
至此，我们已经有了一个使用 Excel 模型的基线，接下来我们对机器学习线性回归模型进行了优化。 我们使用基于模块[筛选器的特征选择][filter-based-feature-selection]来改进我们选择的初始数据元素, 并帮助我们实现 4.6% 平均绝对错误的性能改进。 对于将来的项目, 我们将使用此功能, 此功能可以节省循环访问数据属性的时间, 从而找到要用于建模的正确功能集。 

接下来, 我们计划在实验中包含其他算法, 如[Bayesian][bayesian-linear-regression]或[提升决策树][boosted-decision-tree-regression], 以比较性能。 

如果要实验回归，能源效率回归示例数据集即是可用于尝试的好的数据集，其中包含很多数值属性。 数据集作为工作室中的示例数据集的一部分提供。 可以使用各种学习模块来预测加热负载或冷却负载。 下表是针对目标变量冷却负载预测的能源效率数据集的不同回归学习的性能比较： 

| 模型 | 平均绝对误差 | 均方根误差 | 相对绝对误差 | 相对平方误差 | 决定系数 |
| --- | --- | --- | --- | --- | --- |
| 提升决策树 |0.930113 |1.4239 |0.106647 |0.021662 |0.978338 |
| 线性回归（梯度下降） |2.035693 |2.98006 |0.233414 |0.094881 |0.905119 |
| 神经网络回归 |1.548195 |2.114617 |0.177517 |0.047774 |0.952226 |
| 线性回归（普通最小二乘法） |1.428273 |1.984461 |0.163767 |0.042074 |0.957926 |

## <a name="key-takeaways"></a>要点
通过从并行运行 Excel 回归和工作室试验，我们学到了很多。 在 Excel 中创建基线模型, 并将其与使用机器学习[线性回归][linear-regression]的模型进行比较, 帮助我们了解 Studio, 我们发现了改进数据选择和模型性能的机会。 

我们还发现, 建议使用[基于筛选器的功能选择][filter-based-feature-selection]来加速未来的预测项目。 通过对数据应用功能选择，可以在工作室中创建改进的模型，以获得更好的总体性能。 

将预测分析预测从工作室系统性地传输到 Excel 的能力，使得成功地为广泛业务用户受众提供结果的能力显著增加。 

## <a name="resources"></a>资源
下面是一些可帮助用户处理回归的资源： 

* Excel 中的回归。 如果从未尝试在 Excel 中使用回归，可参阅本教程轻松完成操作：[https://www.excel-easy.com/examples/regression.html](https://www.excel-easy.com/examples/regression.html)
* 回归与预测。 Tyler Chessman 撰写的博客文章，介绍了如何在 Excel 中进行时间序列预测，其中包括适合初学者的线性回归描述。 [https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts](https://www.itprotoday.com/sql-server/understanding-time-series-forecasting-concepts) 
* 普通最小二乘法（线性回归）：缺陷、问题和陷阱。 有关回归的简介和讨论：[https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](https://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

