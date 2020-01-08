---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: fe4ecc237b56575f99844d3ec074225fadb69d3c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673242"
---
## <a name="configure-your-application-to-access-azure-storage"></a>创建用于访问 Azure 存储的应用程序
有两种方法可以对要访问存储服务的应用程序进行身份验证：

* 共享密钥：共享密钥仅用于测试目的
* 共享访问签名 (SAS)：对生产应用程序使用 SAS

### <a name="shared-key"></a>共享密钥
共享密钥身份验证意味着应用程序将使用帐户名和帐户密钥访问存储服务。 为了快速说明如何使用此库，我们会在此入门指南中使用共享密钥身份验证。

> [!WARNING] 
> **请仅将共享密钥身份验证用于测试用途！** 为关联的存储帐户提供完全读/写访问权限的帐户名和帐户密钥将分发给下载应用的每个人。 这**不**是好的做法，你会面临向不受信任的客户端泄露密钥的风险。
> 
> 

使用共享密钥身份验证时，会创建一个[连接字符串](../articles/storage/common/storage-configure-connection-string.md)。 连接字符串由以下部分组成：  

* **DefaultEndpointsProtocol** - 可以选择 HTTP 或 HTTPS。 但是，强烈建议使用 HTTPS。
* **帐户名** - 存储帐户的名称
* **帐户密钥** - 在 [Azure 门户](https://portal.azure.com)上，导航到存储帐户，并单击“密钥”  图标以查看此信息。
* （可选）**EndpointSuffix** - 用于区域中具有不同终结点后缀的存储服务，例如 Azure 中国或 Azure 调控。

以下是使用共享密钥身份验证的连接字符串示例：

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>共享访问签名 (SAS)
对于移动应用程序，针对 Azure 存储服务对客户端请求进行身份验证的建议方法是使用共享访问签名 (SAS)。 SAS 允许使用指定的权限集向客户端授予在指定的时间内对资源的访问权限。
作为存储帐户所有者，需要为移动客户端生成要使用的 SAS。 要生成 SAS，可能需要编写单独的服务，该服务生成要分发给客户端的 SAS。 出于测试目的，可以使用 [Microsoft Azure 存储资源管理器](https://storageexplorer.com)或 [Azure 门户](https://portal.azure.com)来生成 SAS。 创建 SAS 时，可以指定 SAS 有效的时间间隔，以及 SAS 授予客户端的权限。

以下示例演示如何使用 Microsoft Azure 存储资源管理器来生成 SAS。

1. [安装 Microsoft Azure 存储资源管理器](https://storageexplorer.com)（如果尚未安装）
2. 连接到订阅
3. 单击用户的存储帐户，并单击左下方的“操作”选项卡。 单击“获取共享访问签名”，生成 SAS 的连接字符串。
4. 下面是 SAS 连接字符串的示例，该字符串为存储帐户的 Blob 服务授予对服务、容器和对象级别的读取与写入权限。
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

可以看到，使用 SAS 时，不会在应用程序中公开帐户密钥。 可以查阅[共享访问签名：了解 SAS 模型](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)，详细了解 SAS 以及 SAS 使用方面的最佳做法。

