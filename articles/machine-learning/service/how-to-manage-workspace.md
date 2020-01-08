---
title: 在门户中创建 Azure ML 工作区
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 门户中创建、查看和删除 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 511c737e160c0f0753e570314c9b29346972cb04
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269253"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>在 Azure 门户中创建和管理 Azure 机器学习工作区

在本文中，你将在[Azure 机器学习](overview-what-is-azure-ml.md)的 Azure 门户中创建、查看和删除[**Azure 机器学习工作区**](concept-workspace.md)。  门户是开始使用工作区的最简单方法，但随着需求的更改或自动化的要求增加，你还可以[使用通过使用](reference-azure-machine-learning-cli.md) [Python 代码](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或[通过 VS Code 扩展](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code)来创建和删除工作区。

## <a name="create-a-workspace"></a>创建工作区

必须有 Azure 订阅，才能创建工作区。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>下载配置文件

1. 如果要创建[笔记本 VM](tutorial-1st-experiment-sdk-setup.md#azure)，请跳过此步骤。

1. 如果计划使用引用此工作区的本地环境中的代码，请从工作区的“概述”部分中选择“下载 config.json”。  

   ![下载 config.json](./media/how-to-manage-workspace/configure.png)
   
   使用 Python 脚本或 Jupyter Notebook 将此文件放入到目录结构中。 它可以位于同一目录（名为 *.azureml* 的子目录）中，也可以位于父目录中。 创建笔记本 VM 时，会将此文件添加到 VM 上的正确目录。


## <a name="view"></a>查看工作区

1. 选择门户左上角的“所有服务”。

1. 在 "**所有服务**" 筛选器字段中，键入 "**机器学习服务**"。  

1. 选择**机器学习服务工作区**。

   ![搜索 Azure 机器学习工作区](media/how-to-manage-workspace/all-services.png)

1. 浏览筛选出的工作区列表。 筛选依据可包括订阅、资源组和位置。  

1. 选择要显示其属性的工作区。
   ![工作区属性](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>创建工作区

单击要删除的工作区顶部的“删除”按钮。

  ![“删除”按钮](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

按照全长教程，了解如何使用工作区生成、定型和部署具有 Azure 机器学习的模型。

> [!div class="nextstepaction"]
> [教程：训练模型](tutorial-train-models-with-aml.md)
