---
title: Python:对 Azure Data Lake Storage Gen1 执行的文件系统操作 | Microsoft Docs
description: 了解如何使用 Python SDK 处理 Data Lake Storage Gen1 文件系统。
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f2ee982e2c1e1c363a391779721f848b8ae6afd5
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088892"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>使用 Python 在 Azure Data Lake Storage Gen1 上执行文件系统操作
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

本文介绍了如何使用 Python SDK 在 Azure Data Lake Storage Gen1 上执行文件系统操作。 若要了解如何使用 Python 对 Data Lake Storage Gen1 执行帐户管理操作，请参阅[使用 Python 在 Data Lake Storage Gen1 上执行帐户管理操作](data-lake-store-get-started-python.md)。

## <a name="prerequisites"></a>先决条件

* **Python**。 可以从[此处](https://www.python.org/downloads/)下载 Python。 本文使用的是 Python 3.6.2。

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Storage Gen1 帐户**。 请遵循[通过 Azure 门户开始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) 中的说明进行操作。

## <a name="install-the-modules"></a>安装模块

若要通过 Python 使用 Data Lake Storage Gen1，需要安装三个模块。

* `azure-mgmt-resource` 模块，包括用于 Active Directory 的 Azure 模块，等等。
* `azure-mgmt-datalake-store` 模块，包括 Azure Data Lake Storage Gen1 帐户管理操作。 有关此模块的详细信息，请参阅 [azure-mgmt-datalake-store 模块参考](/python/api/azure-mgmt-datalake-store/)。
* `azure-datalake-store` 模块，包括 Azure Data Lake Storage Gen1 文件系统操作。 有关此模块的详细信息，请参阅 [azure-datalake-store file-system 模块参考](https://azure-datalake-store.readthedocs.io/en/latest/)。

使用以下命令安装这些模块。

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

1. 在所选的 IDE 中创建新的 Python 应用程序，例如，**mysample.py**。

2. 添加以下代码行导入所需的模块

   ```
   ## Use this only for Azure AD service-to-service authentication
   from azure.common.credentials import ServicePrincipalCredentials

   ## Use this only for Azure AD end-user authentication
   from azure.common.credentials import UserPassCredentials

   ## Use this only for Azure AD multi-factor authentication
   from msrestazure.azure_active_directory import AADTokenCredentials

   ## Required for Azure Data Lake Storage Gen1 account management
   from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
   from azure.mgmt.datalake.store.models import DataLakeStoreAccount

   ## Required for Azure Data Lake Storage Gen1 filesystem management
   from azure.datalake.store import core, lib, multithread

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. 将更改保存到 mysample.py。

## <a name="authentication"></a>身份验证

本部分介绍有关使用 Azure AD 进行身份验证的不同方式。 可用选项包括：

* 有关应用程序的最终用户身份验证，请参阅[使用 Python 通过 Data Lake Storage Gen1 进行最终用户身份验证](data-lake-store-end-user-authenticate-python.md)。
* 有关应用程序的服务到服务身份验证，请参阅[使用 Python 通过 Data Lake Storage Gen1 进行服务到服务身份验证](data-lake-store-service-to-service-authenticate-python.md)。

## <a name="create-filesystem-client"></a>创建文件系统客户端

以下代码段首先创建 Data Lake Storage Gen1 帐户客户端。 它使用客户端对象来创建一个 Data Lake Storage Gen1 帐户。 最后，此代码段创建一个文件系统客户端对象。

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>创建目录

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>上传文件


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>下载文件

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>删除目录

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>后续步骤
* [使用 Python 在 Data Lake Storage Gen1 上执行帐户管理操作](data-lake-store-get-started-python.md)。

## <a name="see-also"></a>请参阅

* [Azure Data Lake Storage Gen1 Python（文件系统）参考](https://azure-datalake-store.readthedocs.io/en/latest)
* [与 Azure Data Lake Storage Gen1 兼容的开源大数据应用程序](data-lake-store-compatible-oss-other-applications.md)
