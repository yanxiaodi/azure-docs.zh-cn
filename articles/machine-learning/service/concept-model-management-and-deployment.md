---
title: MLOps:管理、部署、& 监视 ML 模型
titleSuffix: Azure Machine Learning
description: 了解如何使用用于 MLOps 的 Azure 机器学习：部署、管理和监视模型，以便不断地改善它们。 可以在本地计算机上部署通过 Azure 机器学习训练的模型，也可从其他源进行部署。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 98a3102d47504b40a6b62eb329b508468947ca79
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035469"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning"></a>MLOps:通过 Azure 机器学习管理、部署和监视模型

本文介绍如何使用 Azure 机器学习来管理模型的生命周期。 Azure 机器学习使用机器学习操作（MLOps）方法，该方法可提高机器学习解决方案的质量和一致性。 

Azure 机器学习提供以下 MLOps 功能：

- **从任何位置部署 ML 项目**
- **监视 ml 应用程序的操作和 ml 相关问题**-比较定型与推理之间的模型输入，探索特定于模型的指标，并提供有关 ML 基础结构的监视和警报。
- **捕获建立 ML 生命周期的端到端审核跟踪所需的数据**，包括发布模型的人员、进行更改的原因以及在生产环境中部署或使用模型的时间。
- **通过 Azure 机器学习和 Azure DevOps 自动执行端到端 ML 生命周期**，以频繁更新模型、测试新模型，并随其他应用程序和服务一起不断推出新的 ML 模型。

若要了解有关 MLOps 背后的概念以及如何将它们应用于 Azure 机器学习的详细信息，请观看以下视频。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>从任何位置部署 ML 项目

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>将定型过程转换为可重复的管道
使用 Azure 机器学习的 ML 管道将模型定型过程中涉及的所有步骤汇聚到一起，从数据准备到功能提取到超参数优化到模型评估。

有关详细信息，请参阅[ML 管道](concept-ml-pipelines.md)。

### <a name="register-and-track-ml-models"></a>注册和跟踪 ML 模型

通过模型注册，可在 Azure 云的工作区中存储模型并确定模型版本。 使用模型注册表，可轻松组织和跟踪定型的模型。

> [!TIP]
> 已注册的模型是组成模型的一个或多个文件的逻辑容器。 例如, 如果您有一个存储在多个文件中的模型, 则可以将它们作为一个模型注册到 Azure 机器学习工作区中。 注册后, 可以下载或部署已注册的模型, 并接收已注册的所有文件。
 
按名称和版本标识已注册的模型。 每次使用与现有名称相同的名称来注册模型时，注册表都会将版本递增。 也可在注册过程中提供其他的元数据标记，这些标记可以在搜索模型时使用。 Azure 机器学习支持可使用 Python 3.5.2 或更高版本加载的任何模型。

> [!TIP]
> 你还可以注册在 Azure 机器学习之外训练的模型。

不能删除在活动部署中使用的已注册模型。
有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#registermodel)的注册模型部分。

### <a name="package-and-debug-models"></a>包和调试模型

在将模型部署到生产环境之前，会将其打包到 Docker 映像中。 在大多数情况下，映像创建会在部署期间自动执行。 对于高级方案，可以手动指定映像。

如果遇到部署问题，可以在本地开发环境中进行部署，以便进行故障排除和调试。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md#registermodel)和[排查部署问题](how-to-troubleshoot-deployment.md)。

### <a name="validate-and-profile-models"></a>验证和分析模型

Azure 机器学习可以使用分析来确定部署模型时要使用的理想 CPU 和内存设置。 在此过程中，将使用为分析过程提供的数据来进行模型验证。

### <a name="convert-and-optimize-models"></a>转换和优化模型

将模型转换为[开放式神经网络交换](https://onnx.ai)（ONNX）可以提高性能。 平均情况下，转换为 ONNX 可能会提高性能的2倍。

有关 ONNX 与 Azure 机器学习的详细信息，请参阅[创建和加速 ML 模型](concept-onnx.md)一文。

### <a name="use-models"></a>使用模型

训练的机器学习模型可以作为 web 服务部署在云中或本地部署到开发环境中。 你还可以将模型部署到 Azure IoT Edge 设备。 部署可以对推断使用 CPU、GPU 或现场可编程入口阵列（FPGA）。 你还可以使用 Power BI 中的模型。

使用模型作为 web 服务或 IoT Edge 设备时，需要提供以下项：

* 用于对提交到服务/设备的数据进行评分的模型。
* 一个项脚本。 此脚本接受请求，使用模型对数据进行评分并返回响应。
* Conda 环境文件，用于描述模型和条目脚本所需的依赖项。
* 模型和输入脚本所需的任何其他资产，如文本、数据等。

这些资产打包到 Docker 映像，并部署为 web 服务或 IoT Edge 模块。

或者，你可以使用以下参数来进一步优化部署：

* 启用 GPU：用于在 Docker 映像中启用 GPU 支持。 必须在 Microsoft Azure 服务（如 Azure 容器实例、Azure Kubernetes 服务、Azure 机器学习计算或 Azure 虚拟机）上使用映像。
* 额外的 docker 文件步骤：一个文件，其中包含创建 Docker 映像时要运行的其他 Docker 步骤。
* 基本映像：用作基础映像的自定义映像。 如果不使用自定义映像，则 Azure 机器学习提供基本映像。

还提供目标部署平台的配置。 例如，在部署到 Azure Kubernetes 服务时，VM 家族类型、可用内存和核心数。

创建映像时，还会添加 Azure 机器学习所需的组件。 例如，运行 web 服务并与 IoT Edge 进行交互所需的资产。

> [!NOTE]
> 不能修改或更改 Docker 映像中使用的 web 服务器或 IoT Edge 组件。 Azure 机器学习使用 web 服务器配置，并 IoT Edge Microsoft 所测试和支持的组件。

#### <a name="web-service"></a>Web 服务

您可以在**web 服务**中使用您的模型以及以下计算目标：

* Azure 容器实例
* Azure Kubernetes 服务
* 本地开发环境

若要将模型部署为 web 服务，必须提供以下项：

* 模型的模型或系综。
* 使用模型所需的依赖项。 例如，接受请求并调用模型的脚本、conda 依赖项等。
* 描述如何以及在何处部署模型的部署配置。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md)。

#### <a name="iot-edge-devices"></a>IoT Edge 设备

可以通过**Azure IoT Edge 模块**将模型与 IoT 设备一起使用。 IoT Edge 模块部署到硬件设备上，该设备在设备上启用推理或模型评分。

有关详细信息，请参阅[部署模型](how-to-deploy-and-where.md)。

### <a name="analytics"></a>分析

Microsoft Power BI 支持使用机器学习模型进行数据分析。 有关详细信息，请参阅[Power BI （预览版）中的 Azure 机器学习集成](https://docs.microsoft.com/power-bi/service-machine-learning-integration)。


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>监视 ML 应用程序的操作和 ML 相关问题

利用监视功能，您可以了解要发送到您的模型的数据以及返回的预测。

此信息可帮助你了解模型的使用方式。 收集的输入数据在训练模型的未来版本时也可能会很有用。

有关详细信息，请参阅[如何启用模型数据收集](how-to-enable-data-collection.md)。


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>捕获 ML 生命周期的端到端审核线索

Azure ML 使你能够跟踪所有 ML 资产的端到端审核记录。 尤其是在下列情况下：

- Azure ML[与 Git 集成](how-to-set-up-training-targets.md#gitintegration)，以跟踪你的代码所来自的存储库/分支/提交的信息。
- [AZURE ML 数据集](how-to-create-register-datasets.md)可帮助你跟踪和版本数据。
- Azure ML 运行历史记录存储用于定型模型的代码、数据和计算的快照。
- Azure ML 模型注册表捕获与模型关联的所有元数据（这会对它进行定型，以便在部署时进行定型）。

## <a name="automate-the-end-to-end-ml-lifecycle"></a>自动化端到端 ML 生命周期 

您可以使用 GitHub 和 Azure Pipelines 来创建用于训练模型的持续集成过程。 在典型方案中，当数据科研人员将更改签入项目的 Git 存储库时，Azure 管道将开始训练运行。 然后，可以检查运行的结果，以查看定型模型的性能特征。 还可以创建一个管道，用于将模型部署为 web 服务。

使用[Azure 机器学习扩展](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml)可以更轻松地使用 Azure Pipelines。 它为 Azure Pipelines 提供了以下增强功能：

* 定义服务连接时启用工作区选择。
* 允许在定型管道中创建的定型模型触发发布管道。

有关将 Azure Pipelines 与 Azure 机器学习配合使用的详细信息，请参阅使用 Azure Pipelines 文章和[Azure 机器学习 MLOps](https://aka.ms/mlops)存储库实现[ML 模型的持续集成和部署](/azure/devops/pipelines/targets/azure-machine-learning)。

## <a name="next-steps"></a>后续步骤

详细了解[如何以及在何处部署](how-to-deploy-and-where.md)具有 Azure 机器学习的模型。 有关部署的示例，请参阅[教程：在 Azure 容器实例](tutorial-deploy-models-with-aml.md)中部署图像分类模型。

了解如何[通过 Azure Pipelines 创建 ML 模型的持续集成和部署](/azure/devops/pipelines/targets/azure-machine-learning)。 

了解如何创建[使用部署为 Web 服务的模型](how-to-consume-web-service.md)的客户端应用程序和服务。
