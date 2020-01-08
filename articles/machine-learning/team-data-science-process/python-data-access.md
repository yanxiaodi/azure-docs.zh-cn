---
title: 通过 Python 客户端库访问数据集 - Team Data Science Process
description: 安装并使用 Python 客户端库以从本地 Python 环境安全访问和管理 Azure 机器学习数据。
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bf0e679ab46752d71ba4f5ef2b014e0cb2b4c6ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60593997"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>使用 Azure 机器学习 Python 客户端库通过 Python 访问数据集
Microsoft Azure 机器学习 Python 客户端库的预览可允许从本地 Python 环境安全访问 Azure 机器学习数据集，以及允许在工作区创建并管理数据集。

本主题说明如何执行以下操作：

* 安装机器学习 Python 客户端库
* 访问和上传数据集，包括如何获取授权以从本地 Python 环境访问 Azure 机器学习数据集的说明
* 从实验访问中间数据集
* 使用 Python 客户端库枚举数据集、访问元数据、读取数据集内容、创建新的数据集和更新现有数据集

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="prerequisites"></a>先决条件
已在以下环境下测试 Python 客户端库：

* Windows、Mac 和 Linux
* Python 2.7、3.3 和 3.4

在以下包中具有依赖项：

* 请求
* python-dateutil
* pandas

建议使用 Python 分发，如 Python 随附的 [Anaconda](http://continuum.io/downloads#all) 或 [Canopy](https://store.enthought.com/downloads/)，IPython 和上面列出的三个包已安装。 虽然 IPython 不是绝对必需的，但对于交互式操作和可视化数据，它是非常理想的环境。

### <a name="installation"></a>如何安装 Azure 机器学习 Python 客户端库
此外必须安装 Azure 机器学习 Python 客户端库，以完成本主题中概述的任务。 可从 [Python Package Index](https://pypi.python.org/pypi/azureml)（Python 包索引）中获取。 若要在 Python 环境中进行安装，请从本地 Python 环境中运行以下命令：

    pip install azureml

或者，可以从 [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python) 上的源中下载和安装。

    python setup.py install

如果已在计算机上安装 git，则可以使用 pip 从 git 存储库中直接进行安装：

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>使用 Studio 代码片段以访问数据集
通过 Python 客户端库，能够以编程方式从已运行的实验中访问现有的数据集。

从 Studio Web 界面，可生成代码片段，其中包括下载和反序列化数据集作为本地计算机上 pandas DataFrame 对象的所有必要信息。

### <a name="security"></a>数据访问的安全
Studio 提供的用于 Python 客户端库的代码片段包括工作区 ID 和授权令牌。 这些将提供工作区的完全访问，必须受到保护，如密码。

出于安全原因，代码片段功能仅适用于其角色设置为工作区**所有者**的用户。 在“设置”  下的“用户”  页面上 Azure 机器学习工作室中会显示你的角色。

![安全][security]

如果角色未设置为**所有者**，可以请求重新邀请为所有者，或询问工作区所有者以提供代码片段。

若要获取授权令牌，可以执行以下任一操作：

* 向所有者请求令牌。 所有者可从 Studio 中其工作区“设置”页面中访问授权令牌。 从左窗格中选择“设置”  ，单击“授权令牌”  以查看主要和次要令牌。 尽管主要或次要授权都可在代码片段中使用，但是建议所有者只共享次要授权令牌。

![授权令牌](./media/python-data-access/ml-python-access-settings-tokens.png)

* 请求升级为所有者角色。 要执行此操作，当前工作区所有者需要先你将从工作区中删除，然后重新邀请你作为所有者加入。

开发人员获取工作区 ID 和授权令牌之后，能够使用代码片段访问工作区，无论其角色是什么。

在“设置”  下的“授权令牌”  上托管授权令牌。 可以撤销它们，但是此过程会撤销上一个令牌的访问权限。

### <a name="accessingDatasets"></a>从本地 Python 应用程序访问数据集
1. 在机器学习工作室中，单击左侧导航栏中的“数据集”  。
2. 选择想要访问的数据集。 可以从“我的数据集”  列表或“示例”  列表中选择任一数据集。
3. 从底部工具栏，单击“生成数据访问代码”  。 如果数据格式与 Python 客户端库不兼容，则禁用此按钮。
   
    ![数据集][datasets]
4. 从显示的窗口中选择代码片段，并将其复制到剪贴板。
   
    ![“生成数据访问代码”按钮][dataset-access-code]
5. 将代码粘贴到本地 Python 应用程序的 Notebook 中。
   
    ![将代码粘贴到笔记本中][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>从机器学习试验访问中间数据集
在机器学习工作室中运行实验之后，就可以从模块的输出节点中访问中间数据集。 中间数据集是指模型工具运行后，已创建并用于中间步骤的数据。

只要数据格式符合 Python 客户端库，就可以访问中间数据集。

支持以下格式（这些的常量是 `azureml.DataTypeIds` 类）：

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

将鼠标悬停在模块输出节点上，可确定格式。 工具提示中同时会显示节点名。

一些模块（如[拆分][split]模块）会输出名为 `Dataset` 的格式，而 Python 客户端库不支持此格式。

![数据集格式][dataset-format]

需要使用转换模块，如[转换为 CSV][convert-to-csv]，以将输出转换为受支持的格式。

![GenericCSV 格式][csv-format]

以下步骤介绍创建实验、运行实验并访问中间数据集的示例。

1. 创建新实验。
2. 插入“成年人口收入二元分类数据集”  模块。
3. 插入[拆分][split]模块，并将其输入连接到数据集模块输出。
4. 插入[转换为 CSV][convert-to-csv] 模块，并将其输入连接到其中一个[拆分][split]模块输出。
5. 保存此实验并运行，并等待运行完成。
6. 单击[转换为 CSV][convert-to-csv] 模块上的输出节点。
7. 上下文菜单出现时，选择“生成数据访问代码”  。
   
    ![上下文菜单][experiment]
8. 从显示的窗口中选择代码片段，并将其复制到剪贴板。
   
    ![从上下文菜单生成访问代码][intermediate-dataset-access-code]
9. 将代码粘贴到 Notebook 中。
   
    ![将代码粘贴到笔记本中][ipython-intermediate-dataset]
10. 可使用 matplotlib 将数据可视化。 这会在直方图的年龄列中显示：
    
    ![直方图][ipython-histogram]

## <a name="clientApis"></a>使用机器学习 Python 客户端库来访问、读取、创建和管理数据集
### <a name="workspace"></a>工作区
工作区是 Python 客户端库的入口点。 提供具有工作区 ID 和授权令牌的 `Workspace` 类以创建实例：

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>枚举数据集
枚举给定工作区中所有数据集：

    for ds in ws.datasets:
        print(ds.name)

只枚举用户创建的数据集：

    for ds in ws.user_datasets:
        print(ds.name)

只枚举示例数据集：

    for ds in ws.example_datasets:
        print(ds.name)

可以按照名称（区分大小写）访问数据集：

    ds = ws.datasets['my dataset name']

或者按照索引访问数据集：

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadata
除了内容，数据集还具有元数据。 （中间数据集是例外，它不具有任何元数据。）

某些元数据值在创建时由用户分配：

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

其他值由 Azure ML 分配：

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

请参阅 `SourceDataset` 类以获取关于可用元数据的详细信息。

### <a name="read-contents"></a>读取内容
机器学习工作室提供的代码片段会自动将数据集下载并反序列化到 pandas DataFrame 对象。 通过 `to_dataframe` 方法完成此操作：

    frame = ds.to_dataframe()

如果想要下载原始数据，并自己执行反序列化，这是一个选项。 目前，对于 Python 客户端库无法反序列化的格式（如“ARFF”），这是唯一的选项。

将内容读取为文本：

    text_data = ds.read_as_text()

将内容读取为二进制：

    binary_data = ds.read_as_binary()

还可以只将流打开到内容：

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>创建新的数据集
Python 客户端库允许从 Python 程序上传数据集。 然后这些数据集便可在工作区中使用。

如果具有 pandas DataFrame 中的数据，请使用以下代码：

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

如果数据已序列化，则可以使用：

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Python 客户端库可以将 pandas DataFrame 序列化为以下格式（这些的常量是 `azureml.DataTypeIds` 类）：

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>更新现有数据集
如果尝试上传具有与现有数据集匹配的名称的新数据集，则会收到冲突错误。

若要更新现有数据集，首先需要获取对现有数据集的引用：

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

然后使用 `update_from_dataframe` 序列化并替换 Azure 上数据集的内容：

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

如果想要将数据序列化为其他格式，请为可选的 `data_type_id` 参数指定值。

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

（可选）可通过为 `description` 参数指定值来设置新的说明。

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

（可选）可通过为 `name` 参数指定值来设置新的名称。 从现在起，将只使用新名称来检索数据集。 以下代码将更新数据、名称和说明。

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

`data_type_id` 、`name` 和 `description` 参数是可选项，并默认为其以前的值。 `dataframe` 参数始终是必需的。

如果数据已序列化，则使用 `update_from_raw_data` 而不是 `update_from_dataframe`。 如果只需传递到 `raw_data` 而不是 `dataframe` 中，则使用相同方式操作。

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

