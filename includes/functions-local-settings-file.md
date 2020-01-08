---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: fef5cd38461fec67790fb67faf8e466d46b247fc
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669533"
---
## <a name="local-settings-file"></a>本地设置文件

本地. json 文件存储本地开发工具使用的应用设置、连接字符串和设置。 仅当在本地运行项目时, 才使用本地. json 文件中的设置。 本地设置文件具有以下结构:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

在本地运行项目时, 支持这些设置:

| 设置      | 描述                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 如果将此设置设置为`true`, 则使用本地计算机密钥对所有值进行加密。 与 `func settings` 命令配合使用。 默认值为 `false`。 |
| **`Values`** | 在本地运行项目时使用的应用程序设置和连接字符串的数组。 与 Azure 中的函数应用的应用程序设置相对应, 这些键值 (字符串字符串) 对与类似[`AzureWebJobsStorage`]。 许多触发器和绑定都有一个属性, 该属性引用连接字符串应用设置, `Connection`例如[Blob 存储触发器](../articles/azure-functions/functions-bindings-storage-blob.md#trigger---configuration)。 对于这些属性, 需要在`Values`数组中定义一个应用程序设置。 <br/>对于 HTTP 之外的触发器，[`AzureWebJobsStorage`] 是一个必需的应用设置。 <br/>2\.x 版 Functions 运行时需要 [`FUNCTIONS_WORKER_RUNTIME`] 设置，该设置是由 Core Tools 为项目生成的。 <br/> 如果已在本地安装[Azure 存储模拟器](../articles/storage/common/storage-use-emulator.md)并且将设置[`AzureWebJobsStorage`]为`UseDevelopmentStorage=true`, 则核心工具将使用仿真程序。 在开发过程中, 模拟器非常有用, 但在部署之前应使用实际的存储连接进行测试。<br/> 值必须是字符串，而不能是 JSON 对象或数组。 设置名称不能包含冒号 (`:`) 或双下划线 (`__`)。 这些字符由运行时保留。  |
| **`Host`** | 在本地运行项目时, 此节中的设置自定义函数主机进程。 这些设置与 host json 设置不同, 后者也适用于在 Azure 中运行项目的情况。 |
| **`LocalHttpPort`** | 设置运行本地 Functions 主机时使用的默认端口（`func host start` 和 `func run`）。 `--port`命令行选项优先于此设置。 |
| **`CORS`** | 定义[跨域资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)可以使用的来源。 以逗号分隔的列表提供来源，其中不含空格。 支持通配符值 (\*)，它允许使用任何来源的请求。 |
| **`CORSCredentials`** |  当设置为`true`时, `withCredentials`允许请求。 |
| **`ConnectionStrings`** | 一个集合。 请勿将此集合用于函数绑定所使用的连接字符串。 此集合仅由通常从配置文件的`ConnectionStrings`部分获取连接字符串的框架 (如[实体框架](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)) 使用。 此对象中的连接字符串添加到提供者类型为 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) 的环境中。 此集合中的项目未与其他应用设置一起发布到 Azure。 必须将这些值显式添加到函数应用设置的 `Connection strings` 集合中。 如果要[`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)在函数代码中创建, 则应将连接字符串值与门户中 "**应用程序设置**" 中的其他连接存储在一起。 |

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
