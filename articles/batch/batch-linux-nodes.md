---
title: 在虚拟机计算节点上运行 Linux - Azure Batch | Microsoft 文档
description: 了解如何处理 Azure Batch 中 Linux 虚拟机池上的并行计算工作负荷。
services: batch
documentationcenter: python
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: na
ms.date: 06/01/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 18df43ebf3a20547917ddd372d922741b4cee849
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350105"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>在 Batch 池中预配 Linux 计算节点

可以使用 Azure Batch 在 Linux 和 Windows 虚拟机上运行并行计算工作负荷。 本文详细介绍如何使用 [Batch Python][py_batch_package] 和 [Batch .NET][api_net] 客户端库在 Batch 服务中创建 Linux 计算节点池。

> [!NOTE]
> 在 2017 年 7 月 5 日以后创建的所有 Batch 池都支持应用程序包。 在 2016 年 3 月 10 日和 2017 年 7 月 5 日期间创建的 Batch 池也支持应用程序包，但前提是该池是使用云服务配置创建的。 在 2016 年 3 月 10 日以前创建的 Batch 池不支持应用程序包。 若要详细了解如何使用应用程序包将应用程序部署到 Batch 节点，请参阅[使用 Batch 应用程序包将应用程序部署到计算节点](batch-application-packages.md)。
>
>

## <a name="virtual-machine-configuration"></a>虚拟机配置
在 Batch 中创建计算节点池时，可以使用两个选项来选择节点大小和操作系统：“云服务配置”和“虚拟机配置”。

“云服务配置”*只*提供 Windows 计算节点。 [Sizes for Cloud Services](../cloud-services/cloud-services-sizes-specs.md)（云服务的大小）中列出了可用的计算节点大小，[Azure Guest OS releases and SDK compatibility matrix](../cloud-services/cloud-services-guestos-update-matrix.md)（Azure 来宾 OS 版本和 SDK 兼容性对照表）中列出了可用的操作系统。 创建包含 Azure 云服务节点的池时，需指定上述文章中所述的节点大小和 OS 系列。 对于 Windows 计算节点池，最常使用的是云服务。

“虚拟机配置”为计算节点提供 Linux 和 Windows 映像。 [Sizes for virtual machines in Azure](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（Azure 中虚拟机的大小）(Linux) 和  [Sizes for virtual machines in Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)（Azure 中虚拟机的大小）(Windows) 中列出了可用的计算节点大小。 创建包含虚拟机配置节点的池时，必须指定节点的大小、虚拟机映像引用，以及要在节点上安装的 Batch 节点代理 SKU。

### <a name="virtual-machine-image-reference"></a>虚拟机映像引用

Batch 服务使用[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)提供虚拟机配置中的计算节点。 可以指定 [Azure 市场][vm_marketplace]中的一个映像，或者提供一个准备好的自定义映像。 有关自定义映像的详细信息，请参阅[使用共享映像库创建池](batch-sig-images.md)。

配置虚拟机映像引用时，需指定虚拟机映像的属性。 创建虚拟机映像引用时，需提供以下属性：

| **映像引用属性** | **示例** |
| --- | --- |
| Publisher |规范 |
| 产品/服务 |UbuntuServer |
| SKU |14.04.4-LTS |
| Version |latest |

> [!TIP]
> 可以在 [Navigate and select Linux virtual machine images in Azure with CLI or PowerShell](../virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（使用 CLI 或 PowerShell 在 Azure 中导航和选择 Linux 虚拟机映像）中详细了解这些属性，以及如何列出市场映像。 请注意，目前并非所有市场映像都与 Batch 兼容。 有关详细信息，请参阅[节点代理 SKU](#node-agent-sku)。
>
>

### <a name="node-agent-sku"></a>节点代理 SKU
Batch 节点代理是一个程序，它在池中的每个节点上运行，并在节点与 Batch 服务之间提供命令和控制接口。 节点代理对于不同操作系统有不同的实现（称为 SKU）。 从根本上讲，在创建虚拟机配置时，需要先指定虚拟机映像引用，然后指定要在其上安装映像的代理节点。 通常，每个节点代理 SKU 与多个虚拟机映像兼容。 下面是节点代理 SKU 的几个示例：

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [!IMPORTANT]
> 并非市场中的所有可用虚拟机映像都与当前可用的 Batch 节点代理兼容。 使用 Batch SDK 列出可用的节点代理 SKU 及其兼容的虚拟机映像。 有关详细信息以及如何在运行时检索有效映像列表的示例，请参阅本文后半部分的[虚拟机映像列表](#list-of-virtual-machine-images)。
>
>

## <a name="create-a-linux-pool-batch-python"></a>创建 Linux 池：批处理 Python
以下代码片段演示了如何使用[适用于 Python 的 Microsoft Azure Batch 客户端库][py_batch_package]创建 Ubuntu Server 计算节点池。 有关 Batch Python 模块的参考文档可在“阅读文档”上的 [azure.batch package][py_batch_docs] 包处找到。

此代码片段显式创建 [ImageReference][py_imagereference]，并指定它的每个属性（publisher、offer、SKU、version）。 但在生产代码中，建议使用[list_node_agent_skus][py_list_skus]方法在运行时从可用映像和节点代理 SKU 组合中进行确定和选择。

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="14.04.2-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

如前文所述，建议使用[list_node_agent_skus][py_list_skus]方法从当前支持的节点代理/应用商店映像组合中动态选择，而不是显式创建[ImageReference][py_imagereference] 。 以下 Python 代码片段演示如何使用此方法。

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(
    agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id=ubuntu1404agent.id)
```

## <a name="create-a-linux-pool-batch-net"></a>创建 Linux 池：批处理 .NET
以下代码片段示范如何使用 [Batch .NET][nuget_batch_net] 客户端库创建 Ubuntu Server 计算节点池。 可以在 docs.microsoft.com 上找到 [Batch .NET 参考文档][api_net]。

以下代码片段使用 [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] 方法从当前支持的市场映像和节点代理 SKU 组合列表中进行选择。 这种做法非常有效，因为支持的组合列表可能随着时间改变。 通常情况下，添加支持的组合。

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

尽管上述代码片段使用 [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] 方法动态列出了支持的映像和节点代理 SKU 组合并从中做出选择（建议的做法），但也可以显式配置 [ImageReference][net_imagereference]：

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "14.04.2-LTS",
    version: "latest");
```

## <a name="list-of-virtual-machine-images"></a>虚拟机映像列表
下表列出了本文上次更新时，与可用 Batch 节点代理兼容的市场虚拟机映像。 请务必注意，此列表并非永久不变，因为可能随时会添加或删除映像和节点代理。 建议 Batch 应用程序和服务始终使用[list_node_agent_skus][py_list_skus] （Python）或[ListNodeAgentSkus][net_list_skus] （Batch .net）来确定和选择当前可用的 sku。

> [!WARNING]
> 以下列表可随时更改。 请始终使用 Batch API 中提供的列出节点代理 SKU 方法来列出运行 Batch 作业时兼容的虚拟机和节点代理 SKU。
>
>

| **发布者** | **产品** | **映像 SKU** | **版本** | **节点代理 SKU ID** |
| ------------- | --------- | ------------- | ----------- | --------------------- |
| 或批处理 | rendering-centos73 | 呈现 | latest | batch.node.centos 7 |
| 或批处理 | rendering-windows2016 | 呈现 | latest | batch.node.windows amd64 |
| 规范 | UbuntuServer | 16.04-LTS | latest | batch.node.ubuntu 16.04 |
| 规范 | UbuntuServer | 14.04.5-LTS | latest | batch.node.ubuntu 14.04 |
| Credativ | Debian | 9 | latest | batch.node.debian 9 |
| Credativ | Debian | 8 | latest | batch.node.debian 8 |
| microsoft-ads | linux-data-science-vm | linuxdsvm | latest | batch.node.centos 7 |
| microsoft-ads | standard-data-science-vm | standard-data-science-vm | latest | batch.node.windows amd64 |
| microsoft-azure-batch | centos-container | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | centos-container-rdma | 7-4 | latest | batch.node.centos 7 |
| microsoft-azure-batch | ubuntu-server-container | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| microsoft-azure-batch | ubuntu-server-container-rdma | 16-04-lts | latest | batch.node.ubuntu 16.04 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2016-Datacenter-with-Containers | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter-smalldisk | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | latest | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1-smalldisk | latest | batch.node.windows amd64 |
| OpenLogic | CentOS | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.4 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.3 | latest | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7.1 | latest | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7.4 | latest | batch.node.centos 7 |
| SUSE | SLES-HPC | 12-SP2 | latest | batch.node.opensuse 42.1 |

## <a name="connect-to-linux-nodes-using-ssh"></a>使用 SSH 连接到 Linux 节点
在开发期间或进行故障排除时，可能会发现需要登录到池中的节点。 不同于 Windows 计算节点，无法使用远程桌面协议 (RDP) 连接到 Linux 节点。 相反，Batch 服务在每个节点上启用 SSH 访问以建立远程连接。

以下 Python 代码片段会在池中的每个节点上创建一个用户（远程连接时需要）。 然后列显每个节点的安全外壳 (SSH) 连接信息。

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

下面是针对包含四个 Linux 节点的池运行上述代码后的示例输出：

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

在节点上创建用户时不需要指定密码，而可以指定 SSH 公钥。 在 Python SDK 中，请在 [ComputeNodeUser][py_computenodeuser] 上使用 **ssh_public_key** 参数。 在 .NET 中，请使用 [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key] 属性。

## <a name="pricing"></a>定价
Azure Batch 构建在 Azure 云服务和 Azure 虚拟机技术基础之上。 Batch 服务本身是免费提供的，这意味着，只需支付 Batch 解决方案使用的计算资源费用。 如果选择“云服务配置”，则要根据[云服务定价][cloud_services_pricing]结构付费。 如果选择“虚拟机配置”，则要根据[虚拟机定价][vm_pricing]结构收费。 

如果使用[应用程序包](batch-application-packages.md)将应用程序部署到 Batch 节点，系统还会对应用程序包使用的 Azure 存储资源收费。 通常，Azure 存储的成本是最低的。 

## <a name="next-steps"></a>后续步骤

GitHub 上 [azure-batch-samples][github_samples] 存储库中的 [Python 代码示例][github_samples_py]包含演示如何执行常见 Batch 操作（例如创建池、作业和任务）的多个脚本。 Python 示例随附的 [README][github_py_readme] 文件包含有关如何安装所需包的详细信息。

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: /dotnet/api/microsoft.azure.batch.computenodeuser?view=azure-dotnet
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: /dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey?view=azure-dotnet#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey
[nuget_batch_net]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: https://azure-sdk-for-python.readthedocs.io/batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: /python/api/azure-batch/azure.batch.models.computenodeuser
[py_imagereference]: /python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference
[py_list_skus]: https://docs.microsoft.com/python/api/azure-batch/azure.batch.operations.AccountOperations?view=azure-python
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/
