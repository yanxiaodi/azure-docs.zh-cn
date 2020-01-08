---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 055909d51fcd1228e8eb26189ba682e09aee6a1a
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390582"
---
`inferenceconfig.json`文档中的项映射到[InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)类的参数。 下表描述了 JSON 文档中的实体与方法的参数之间的映射：

| JSON 实体 | 方法参数 | 描述 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 包含要为映像运行的代码的本地文件的路径。 |
| `runtime` | `runtime` | 要用于映像的运行时。 当前支持的运行`spark-py`时`python`是和。 |
| `condaFile` | `conda_file` | 可选。 本地文件的路径，该文件包含要用于映像的 Conda 环境定义。 |
| `extraDockerFileSteps` | `extra_docker_file_steps` | 可选。 本地文件的路径，该文件包含设置映像时要运行的其他 Docker 步骤。 |
| `sourceDirectory` | `source_directory` | 可选。 包含创建映像的所有文件的文件夹的路径。 |
| `enableGpu` | `enable_gpu` | 可选。 是否在映像中启用 GPU 支持。 GPU 映像必须用于 Azure 服务，如 Azure 容器实例、Azure 机器学习计算、Azure 虚拟机和 Azure Kubernetes 服务。 默认值为 False。 |
| `baseImage` | `base_image` | 可选。 要用作基础映像的自定义映像。 如果未提供任何基本映像，则该映像将基于提供的运行时参数。 |
| `baseImageRegistry` | `base_image_registry` | 可选。 包含基本映像的映像注册表。 |
| `cudaVersion` | `cuda_version` | 可选。 要为需要 GPU 支持的映像安装的 CUDA 版本。 GPU 映像必须用于 Azure 服务，如 Azure 容器实例、Azure 机器学习计算、Azure 虚拟机和 Azure Kubernetes 服务。 支持的版本为9.0、9.1 和10.0。 如果`enable_gpu`设置了，则默认值为9.1。 |
| `description` | `description` | 图像的说明。 |

以下 JSON 是用于 CLI 的示例推理配置：

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```