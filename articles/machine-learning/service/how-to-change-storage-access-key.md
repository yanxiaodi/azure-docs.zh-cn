---
title: 更改存储帐户访问密钥
titleSuffix: Azure Machine Learning
description: 了解如何更改工作区使用的 Azure 存储帐户的访问密钥。 Azure 机器学习使用 Azure 存储帐户存储数据和模型。 重新生成存储帐户的访问密钥时，必须更新 Azure 机器学习，才能使用新密钥。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/16/2019
ms.openlocfilehash: 6c87d4553c7b0fd34513d761558a06cd527c4e3b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034957"
---
# <a name="regenerate-storage-account-access-keys"></a>重新生成存储帐户访问密钥

了解如何更改 Azure 机器学习使用的 Azure 存储帐户的访问密钥。 Azure 机器学习可以使用存储帐户来存储数据或定型模型。

出于安全考虑，可能需要更改 Azure 存储帐户的访问密钥。 重新生成访问密钥时，必须将 Azure 机器学习更新为使用新密钥。 Azure 机器学习可以将存储帐户同时用于模型存储和数据存储。

## <a name="prerequisites"></a>先决条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建工作区一](how-to-manage-workspace.md)文。

* [AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。

* [AZURE 机器学习 CLI 扩展](reference-azure-machine-learning-cli.md)。

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>需要更新的内容

存储帐户可由 Azure 机器学习工作区（存储日志、模型、快照等）和数据存储使用。 更新工作区的过程是单个 Azure CLI 命令，可在更新存储密钥后运行。 更多地涉及更新数据存储的过程，并且需要发现哪些数据存储当前正在使用存储帐户，然后重新注册。

> [!IMPORTANT]
> 同时使用 Azure CLI 和使用 Python 的数据存储更新工作区。 仅更新其中一项，而不是足够的，并且可能会导致错误，直到两者都更新为止。

若要发现数据存储使用的存储帐户，请使用以下代码：

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

此代码查找使用 Azure 存储的任何已注册数据存储，并列出以下信息：

* 数据存储名称：在其下注册存储帐户的数据存储的名称。
* 存储帐户名称：Azure 存储帐户的名称。
* 容器：此注册使用的存储帐户中的容器。

它还指明了数据存储是用于 Azure Blob 还是 Azure 文件共享，因为有不同的方法可用于重新注册每种类型的数据存储。

如果你计划为重新生成访问密钥的存储帐户存在条目，请保存数据存储名称、存储帐户名称和容器名称。

## <a name="update-the-access-key"></a>更新访问密钥

若要更新 Azure 机器学习以使用新密钥，请执行以下步骤：

> [!IMPORTANT]
> 执行所有步骤，使用 CLI 更新工作区，并使用 Python 数据存储。 仅更新其中一项可能会导致错误，直到两者都更新。

1. 重新生成密钥。 有关重新生成访问密钥的信息，请参阅[管理存储帐户](/azure/storage/common/storage-account-manage#access-keys)一文。 保存新密钥。

1. 若要将工作区更新为使用新密钥，请执行以下步骤：

    1. 使用以下 Azure CLI 命令登录到包含工作区的 Azure 订阅：

        ```azurecli-interactive
        az login
        ```

    1. 若要将工作区更新为使用新密钥，请使用以下命令。 将`myworkspace`替换为 Azure 机器学习工作区名称，将`myresourcegroup`替换为包含工作区的 Azure 资源组的名称。

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

        此命令会自动同步工作区使用的 Azure 存储帐户的新密钥。

1. 若要重新注册使用存储帐户的数据存储，请使用 "[需要更新的内容](#whattoupdate)" 部分中的值以及步骤1中的键和以下代码：

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    由于`overwrite=True`指定了，此代码将覆盖现有注册，并对其进行更新以使用新密钥。

## <a name="next-steps"></a>后续步骤

有关注册数据存储的详细信息，请参阅[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)类引用。
