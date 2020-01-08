---
title: 创建自动化机器学习试验
titleSuffix: Azure Machine Learning
description: 自动化机器学习将为你选择一种算法，并生成随时可用于部署的模型。 了解可用于配置自动化机器学习试验的选项。
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: seodec18
ms.openlocfilehash: 4d4a3eae9ea3931ceb720785bbf458f54689be6e
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213519"
---
# <a name="configure-automated-ml-experiments-in-python"></a>在 Python 中配置自动 ML 试验

本指南介绍如何通过[AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)定义自动机器学习试验的各种配置设置。 自动化机器学习将自动选择算法和超参数，并生成随时可用于部署的模型。 可以使用多个选项来配置自动化机器学习试验。

若要查看自动化机器学习试验的示例，请参阅[教程：使用自动化机器学习训练分类模型](tutorial-auto-train-models.md)或[使用云中的自动化机器学习训练模型](how-to-auto-train-remote.md)。

自动化机器学习提供的配置选项：

* 选择试验类型：分类、回归或时序预测
* 数据源、格式和提取数据
* 选择计算目标：本地或远程
* 自动化机器学习试验设置
* 运行自动化机器学习试验
* 探索模型指标
* 注册和部署模型

如果你不愿意，还可以[在 Azure 门户中创建自动化机器学习试验](how-to-create-portal-experiments.md)。

## <a name="select-your-experiment-type"></a>选择试验类型

在开始试验之前，应确定要解决的机器学习问题类型。 自动化机器学习支持分类、回归和预测任务类型。

在自动化和优化过程中，自动化机器学习支持以下算法。 用户不需要指定算法。

分类 | 回归 | 时序预测
|-- |-- |--
[逻辑回归](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [弹性网络](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[渐进提升](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[决策树](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)|[决策树](https://scikit-learn.org/stable/modules/tree.html#regression)
[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K 近邻](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[线性 SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C 支持向量分类 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[随机林](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[极端随机树](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 分类器](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 线性分类器](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[线性回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[线性回归量](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[朴素贝叶斯](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[随机梯度下降 (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|

使用`AutoMLConfig`构造`task`函数中的参数指定实验类型。

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

## <a name="data-source-and-format"></a>数据源和格式
自动化机器学习支持驻留在本地桌面上或云中（例如 Azure Blob 存储）的数据。 可将数据读取成 scikit-learn 支持的数据格式。 可将数据读取成：
* Numpy 数组 X（特征）和 y（目标变量，也称为标签）
* Pandas 数据帧

>[!Important]
> 定型数据的要求：
>* 数据必须为表格格式。
>* 要预测的值（目标列）必须在数据中存在。

例如：

*   Numpy 数组

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   Pandas 数据帧

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    y = df["Label"]
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>在远程计算中提取用于运行试验的数据

对于远程执行，需要使数据可从远程计算访问。 可以通过将数据上传到数据存储来完成此操作。

下面是使用`datastore`的示例：

```python
    import pandas as pd
    from sklearn import datasets

    data_train = datasets.load_digits()

    pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
    pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)
```

### <a name="define-dprep-references"></a>定义 iris.dprep 引用

将 X 和 y 定义为 iris.dprep 引用，该引用将传递给自动机器`AutoMLConfig`学习对象，如下所示：

```python

    X = dprep.auto_read_file(path=ds.path('digitsdata/X_train.csv'))
    y = dprep.auto_read_file(path=ds.path('digitsdata/y_train.csv'))


    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 path = project_folder,
                                 run_configuration=conda_run_config,
                                 X = X,
                                 y = y,
                                 **automl_settings
                                )
```

## <a name="train-and-validation-data"></a>训练和验证数据

您可以直接在`AutoMLConfig`方法中指定单独的定型和验证集。

### <a name="k-folds-cross-validation"></a>K 折交叉验证

使用 `n_cross_validations` 设置指定交叉验证的数目。 训练数据集将随机拆分为大小相等的 `n_cross_validations` 折。 在每个交叉验证轮次，某个折将用于验证剩余折上训练的模型。 重复此过程 `n_cross_validations` 次，直到每个折作为验证集使用了一次。 将报告在所有 `n_cross_validations` 轮次中获得的平均评分，并基于整个训练数据集重新训练相应的模型。

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte Carlo 交叉验证（重复随机子采样）

使用 `validation_size` 指定应该用于验证的训练数据集百分比，并使用 `n_cross_validations` 指定交叉验证的数目。 在每个交叉验证轮次，将随机选择 `validation_size` 大小的子集来验证基于剩余数据训练的模型。 最后，将报告在所有 `n_cross_validations` 轮次中获得的平均评分，并基于整个训练数据集重新训练相应的模型。 Monte Carlo 不支持时序预测。

### <a name="custom-validation-dataset"></a>自定义验证数据集

如果不接受随机拆分，则使用自定义验证数据集，通常为时序数据或不均衡数据。 可以指定自己的验证数据集。 将会根据指定的验证数据集而不是随机数据集来评估模型。

## <a name="compute-to-run-experiment"></a>用于运行试验的计算环境

接下来，确定要在何处训练模型。 自动化机器学习训练试验可在以下计算选项中运行：
*   本地台式机或便携式计算机等本地计算机  – 如果数据集较小，并且仍处于探索阶段，则通常使用此选项。
*   云中的远程计算机 – [Azure 机器学习托管计算](concept-compute-target.md#amlcompute)是一个托管服务，可用于在 Azure 虚拟机群集上训练机器学习模型。

有关包含本地和远程计算目标的示例 Notebook，请参阅 [GitHub 站点](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)。

*   Azure 订阅中的 Azure Databricks 群集。 可在此处找到更多详细信息-[设置 Azure Databricks 群集以实现自动 ML](how-to-configure-environment.md#azure-databricks)

请参阅[GitHub 站点](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl)以获取 Azure Databricks 的笔记本示例。

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>配置试验设置

可以使用多个选项来配置自动化机器学习试验。 通过实例化 `AutoMLConfig` 对象来设置这些参数。 有关参数的完整列表，请参阅 [AutoMLConfig 类](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)。

示例包括：

1.  分类试验使用加权 AUC 作为主要指标，每次迭代的最大时间为 12,000 秒，试验在完成 50 次迭代和 2 个交叉验证折后结束。

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  下面是设置为在 100 次迭代后结束的回归试验示例，每次迭代最长持续 600 秒，并完成 5 个交叉验证折。

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

这三个`task`不同的参数值决定了要应用的模型的列表。  `whitelist`使用或`blacklist`参数进一步修改包含或排除的可用模型的迭代。 支持的模型的列表可以在[SupportedModels 类](https://docs.microsoft.com/en-us/python/api/azureml-train-automl/azureml.train.automl.constants.supportedmodels?view=azure-ml-py)中找到。

### <a name="primary-metric"></a>主要指标
主要指标;如以上示例中所示，确定要在模型定型期间用于优化的指标。 你可以选择的主要指标取决于你选择的任务类型。 下面是可用指标的列表。

在[了解自动化机器学习结果](how-to-understand-automated-ml.md)中了解这些信息的具体定义。

|分类 | 回归 | 时序预测
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

### <a name="data-preprocessing--featurization"></a>数据预处理 & 特征化

在每个自动机器学习试验中，你的数据将[自动缩放并规范化](concept-automated-ml.md#preprocess)，以帮助算法正常执行。  但是，还可以启用其他预处理/特征化，例如缺失值插补法、编码和转换。 [详细了解所包含的特征化](how-to-create-portal-experiments.md#preprocess)。

若要启用此特征化， `"preprocess": True`请[ `AutoMLConfig`为类](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)指定。

> [!NOTE]
> 自动机器学习预处理步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为基础模型的一部分。 使用模型进行预测时，训练期间应用的相同预处理步骤将自动应用于输入数据。

### <a name="time-series-forecasting"></a>时序预测
对于时序预测任务类型，你有其他要定义的参数。
1. time_column_name-这是一个必需参数，它定义包含日期/时间序列的定型数据中的列的名称。
1. max_horizon-定义要根据定型数据的周期进行预测的时间长度。 例如，如果您有使用每日时间粒度的定型数据，则可以定义要在多长时间内为模型定型。
1. grain_column_names-定义在定型数据中包含单个时序数据的列的名称。 例如，如果要按商店预测特定品牌的销售额，则可以将商店和品牌列定义为粒度列。

请参阅下面使用的这些设置的示例，[此处](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)提供了笔记本示例。

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task='forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>系综配置

默认情况下启用系综模型，并在自动机器学习运行中显示为最终的运行迭代。 当前支持的系综方法是投票和堆栈。 投票是使用加权平均值作为软投票实现的，堆栈实现使用2层实现，其中第一层具有与投票系综相同的模型，第二层模型用于查找第一层中的模型。 如果使用的是 ONNX 模型，**或**启用了模型 explainability，则将禁用堆栈，并且仅使用投票。

`kwargs`在对象中，可以提供多个默认参数以更改默认stack系综行为。`AutoMLConfig`

* `stack_meta_learner_type`：元学习器是针对单个不同模型的输出训练的模型。 默认`LogisticRegression`的元学习器适用于分类任务（ `LogisticRegressionCV`或启用了交叉验证）和`ElasticNet`回归/预测任务（ `ElasticNetCV`如果启用了交叉验证）。 此参数可以是下列`LogisticRegression`字符串之一：、 `LogisticRegressionCV`、 `LightGBMClassifier`、 `ElasticNet`、 `ElasticNetCV`、 `LightGBMRegressor`或`LinearRegression`。
* `stack_meta_learner_train_percentage`：指定为定型学习器而保留的定型集的比例（选择定型定型类型时）。 默认值为 `0.2`。
* `stack_meta_learner_kwargs`：要传递给学习器的初始值设定项的可选参数。 这些参数和参数类型从相应的模型构造函数中镜像这些参数和参数类型，并将其转发到模型构造函数。

下面的代码演示了一个在`AutoMLConfig`对象中指定自定义系综行为的示例。

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        **ensemble_settings
        )
```

默认情况下，系综训练是启用的，但它可以通过使用`enable_voting_ensemble`和`enable_stack_ensemble`布尔参数来禁用。

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        iterations=20,
        X=X_train,
        y=y_train,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>运行试验

对于自动 ML，你将`Experiment`创建一个对象，该对象是`Workspace`中用于运行试验的的命名对象。

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

提交试验以运行和生成模型。 将 `AutoMLConfig` 传递给 `submit` 方法以生成模型。

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>首先在新的计算机上安装依赖项。  最长可能需要在 10 分钟后才会显示输出。
>将 `show_output` 设置为 `True` 可在控制台上显示输出。

### <a name="exit-criteria"></a>退出条件
可以定义几个选项来完成试验。
1. 无标准-如果不定义任何退出参数，则试验将继续，直到你的主要指标没有进一步的进度。
1. 迭代数-定义要运行的实验的迭代次数。 您可以选择添加 iteration_timeout_minutes 以定义每个迭代的时间限制（以分钟为单位）。
1. 在你的设置中使用 experiment_timeout_minutes 后退出，你可以定义一个试验在多长时间内会继续运行。
1. 达到分数后退出-使用 experiment_exit_score，可以选择在达到主要指标的分数后完成试验。

### <a name="explore-model-metrics"></a>探索模型指标

如果在笔记本中，则可以在小组件或内嵌项中查看训练结果。 有关更多详细信息，请参阅[跟踪和评估模型](how-to-track-experiments.md#view-run-details)。

## <a name="understand-automated-ml-models"></a>了解自动 ML 模型

使用自动 ML 生成的任何模型都包括以下步骤：
+ 自动功能设计（如果预处理 = True）
+ 缩放/规范化和具有 hypermeter 值的算法

我们使它从自动 ML 的 fitted_model 输出中获取此信息是透明的。

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>自动功能设计

查看预处理 = True 时发生的预处理和[自动功能工程](concept-automated-ml.md#preprocess)的列表。

请看以下示例：
+ 有4种输入功能：A （数值），B （数值），C （数值），D （DateTime）
+ 数值特征 C 被丢弃，因为它是具有所有唯一值的 ID 列
+ 数值特征 A 和 B 的值缺失，因此数据估算的是平均值
+ DateTime 功能 D 特征化为11个不同的工程功能

在拟合模型的第一个步骤中使用这两个 Api 来了解更多信息。  请参阅[此示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

+ API 1： `get_engineered_feature_names()`返回工程功能名称的列表。

  用法：
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  此列表包括所有工程的功能名称。

  >[!Note]
  >请将 "timeseriestransformer" 用于任务 = "预测"，否则请将 "datatransformer" 用于 "回归" 或 "分类" 任务。

+ API 2： `get_featurization_summary()`返回所有输入功能的特征化汇总。

  用法：
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >请将 "timeseriestransformer" 用于任务 = "预测"，否则请将 "datatransformer" 用于 "回归" 或 "分类" 任务。

  输出：
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   其中：

   |Output|定义|
   |----|--------|
   |RawFeatureName|提供的数据集中的输入功能/列名称。|
   |TypeDetected|检测到输入功能的数据类型。|
   |删除|指示输入功能是否已删除或已被使用。|
   |EngineeringFeatureCount|通过自动功能工程转换生成的功能的数量。|
   |转换|应用于输入功能以生成工程功能的转换的列表。|

### <a name="scalingnormalization-and-algorithm-with-hypermeter-values"></a>缩放/规范化和具有 hypermeter 值的算法：

若要了解管道的缩放/规范化和算法/超参数值，请使用 fitted_model。 [详细了解缩放/规范化](concept-automated-ml.md#preprocess)。 下面是示例输出：

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

若要获取更多详细信息，请使用[此示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)中所示的帮助器函数。

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

下面是使用特定算法（在本例中为 LogisticRegression）的管道的示例输出。

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

<a name="explain"></a>

## <a name="explain-the-model-interpretability"></a>说明模型（interpretability）

使用自动化机器学习可以了解特征重要性。  在训练过程中，可以获取模型的全局特征重要性。  对于分类方案，还可以获取类级特征重要性。  必须提供验证数据集 (X_valid) 才能获取特征重要性。

可通过两种方式生成特征重要性。

*   完成试验后，可以针对任何迭代使用 `explain_model` 方法。

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   若要查看所有迭代的特征重要性，请在 AutoMLConfig 中将 `model_explainability` 标志设置为 `True`。

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    完成后，可以使用 retrieve_model_explanation 方法检索特定迭代的特征重要性。

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

使用 run 对象显示 URL 以查看功能重要性：

```
automl_run.get_portal_url()
```

您可以在工作区的 Azure 门户中或从[工作区登陆页面（预览版）](https://ml.azure.com)中可视化功能重要性图表。 使用笔记本中的`RunDetails` [Jupyter 小组件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)时，也会显示该图表。 若要了解有关图表的详细信息，请参阅[了解自动化机器学习结果](how-to-understand-automated-ml.md)。

```Python
from azureml.widgets import RunDetails
RunDetails(automl_run).show()
```

![特征重要性图形](./media/how-to-configure-auto-train/feature-importance.png)

若要详细了解如何在 SDK 的其他区域中启用模型解释和功能重要性，请参阅 interpretability 上的[概念](machine-learning-interpretability-explainability.md)文章。

## <a name="next-steps"></a>后续步骤

详细了解[如何以及在何处部署模型](how-to-deploy-and-where.md)。

详细了解[如何使用自动机器学习对回归模型定型](tutorial-auto-train-models.md)，或者[如何在远程资源上使用自动机器学习进行训练](how-to-auto-train-remote.md)。
