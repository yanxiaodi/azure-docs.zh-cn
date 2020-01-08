---
title: 使用 Windows 安全性保护 Windows 上运行的群集 | Microsoft 文档
description: 了解如何使用 Windows 安全性在 Windows 上运行的独立群集中配置节点到节点安全性和客户端到节点安全性。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/24/2017
ms.author: dekapur
ms.openlocfilehash: ccc726f54821d316c745f6af9c63d7ed13986d79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65761926"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>使用 Windows 安全性保护 Windows 上的独立群集
为了防止有人未经授权访问某个 Service Fabric 群集，必须保护该群集。 当群集运行生产工作负荷时，安全性就尤为重要。 本文介绍如何在 *ClusterConfig.JSON* 文件中使用 Windows 安全性配置节点到节点和客户端到节点的安全性。  该过程对应于 [Create a standalone cluster running on Windows](service-fabric-cluster-creation-for-windows-server.md)（创建在 Windows 上运行的独立群集）中的安全性配置步骤。 有关 Service Fabric 如何使用 Windows 安全性的详细信息，请参阅[群集安全方案](service-fabric-cluster-security.md)。

> [!NOTE]
> 由于升级群集后无法更改安全性选项，因此，应慎重地为节点到节点安全性选择适当的选项。 若要更改安全性选项，必须重建整个群集。
>
>

## <a name="configure-windows-security-using-gmsa"></a>使用 gMSA 配置 Windows 安全性  
该示例*ClusterConfig.gMSA.Windows.MultiMachine.JSON*配置文件一起下载[Microsoft.Azure.ServiceFabric.WindowsServer。\<版本 >.zip](https://go.microsoft.com/fwlink/?LinkId=730690)独立群集包包含用于配置 Windows 安全使用的模板[组托管服务帐户 (gMSA)](https://technet.microsoft.com/library/hh831782.aspx):  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {  
        "ClustergMSAIdentity": "[gMSA Identity]",
        "ClusterSPN": "[Registered SPN for the gMSA account]",
        "ClientIdentities": [
            {
                "Identity": "domain\\username",
                "IsAdmin": true
            }
        ]
    }
}
```

| **配置设置** | **说明** |
| --- | --- |
| ClusterCredentialType |设置为“Windows”可为节点到节点的通信启用 Windows 安全性  。  | 
| ServerCredentialType |设置为“Windows”可为客户端到节点的通信启用 Windows 安全性  。 |
| WindowsIdentities |包含群集和客户端标识。 |
| ClustergMSAIdentity |配置节点到节点安全性。 组托管服务帐户。 |
| ClusterSPN |gMSA 帐户的已注册 SPN|
| ClientIdentities |配置客户端到节点安全性。 客户端用户帐户的数组。 |
| 标识 |为客户端标识添加域用户 domain\username。 |
| IsAdmin |设置为 true 可指定域用户具有管理员客户端访问权限，设置为 false 可指定域用户具有用户客户端访问权限。 |

> [!NOTE]
> ClustergMSAIdentity 值格式为"mysfgmsa@mydomain"。

若需要在 gMSA 下运行 Service Fabric，可通过设置“ClustergMSAIdentity”  来配置[节点到节点安全性](service-fabric-cluster-security.md#node-to-node-security)。 若要在节点之间建立信任关系，这些节点必须能够相互识别。 这可以通过两种不同的方法实现：指定包含群集中所有节点的组托管服务帐户，或者指定包含群集中所有节点的域计算机组。 强烈建议使用[组托管服务帐户 (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) 方法，尤其针对拥有 10 个以上节点的较大群集或可能会增大或收缩的群集。  
此方法不需要创建群集管理员对其有访问权限、可在其中添加和删除成员的域组。 这些帐户对于自动密码管理也同样有用。 有关详细信息，请参阅[组托管服务帐户入门](https://technet.microsoft.com/library/jj128431.aspx)。  
 
使用 **ClientIdentities** 配置[客户端到节点安全性](service-fabric-cluster-security.md#client-to-node-security)。 若要在客户端与群集之间建立信任关系，必须对群集进行配置，使其知道可以信任哪些客户端标识。 可以通过两种不同方法实现此目的：指定可以连接的域组用户，或者指定可以连接的域节点用户。 Service Fabric 针对连接到 Service Fabric 群集的客户端支持两种不同的访问控制类型：管理员和用户。 访问控制可让群集管理员针对不同的用户组限制某些类型的特定群集操作的访问权限，使群集更加安全。  管理员对管理功能（包括读取/写入功能）拥有完全访问权限。 默认情况下，用户只有管理功能的读取访问权限（例如查询功能），以及解析应用程序和服务的能力。 有关访问控制的详细信息，请参阅 [Service Fabric 客户端的基于角色的访问控制](service-fabric-cluster-security-roles.md)。  
 
以下示例**安全性**部分使用 gMSA 配置 Windows 安全性，并指定 *ServiceFabric.clusterA.contoso.com* gMSA 中的计算机位于群集中，同时还指定 *CONTOSO\usera* 拥有管理客户端访问权限：  
  
```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClustergMSAIdentity" : "ServiceFabric.clusterA.contoso.com",
        "ClusterSPN" : "http/servicefabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
}
```
  
## <a name="configure-windows-security-using-a-machine-group"></a>使用计算机组配置 Windows 安全性  
将弃用此模型。 建议使用上文详述的 gMSA。 该示例*ClusterConfig.Windows.MultiMachine.JSON*配置文件一起下载[Microsoft.Azure.ServiceFabric.WindowsServer。\<版本 >.zip](https://go.microsoft.com/fwlink/?LinkId=730690)独立群集包包含用于配置 Windows 安全性的模板。  在“属性”部分配置 Windows 安全性：  

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
        "ClientIdentities": [{
            "Identity": "[domain\username]",
            "IsAdmin": true
        }]
    }
}
```

| **配置设置** | **说明** |
| --- | --- |
| ClusterCredentialType |设置为“Windows”可为节点到节点的通信启用 Windows 安全性  。  |
| ServerCredentialType |设置为“Windows”可为客户端到节点的通信启用 Windows 安全性  。 |
| WindowsIdentities |包含群集和客户端标识。 |
| ClusterIdentity |使用计算机组名 domain\machinegroup 配置节点到节点安全性。 |
| ClientIdentities |配置客户端到节点安全性。 客户端用户帐户的数组。 |  
| 标识 |为客户端标识添加域用户 domain\username。 |  
| IsAdmin |设置为 true 可指定域用户具有管理员客户端访问权限，设置为 false 可指定域用户具有用户客户端访问权限。 |  

如果希望在 Active Directory 域内使用计算机组，可通过设置“ClusterIdentity”  来配置[节点到节点安全性](service-fabric-cluster-security.md#node-to-node-security)。 有关详细信息，请参阅在 [Active Directory 中创建计算机组](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx)。

使用 ClientIdentities 配置[客户端到节点安全性](service-fabric-cluster-security.md#client-to-node-security)。  若要在客户端与群集之间建立信任关系，必须对群集进行配置，使群集知道可以信任哪些客户端标识。 可通过两种不同的方式建立信任：

- 指定用户可以连接到的域组。
- 指定用户可以连接到的域节点。

Service Fabric 针对连接到 Service Fabric 群集的客户端支持两种不同的访问控制类型：管理员和用户。 访问控制可让群集管理员针对不同的用户组限制某些类型的特定群集操作的访问权限，使群集更加安全。  管理员拥有对管理功能（包括读取/写入功能）的完全访问权限。 默认情况下，用户只有管理功能的读取访问权限（例如查询功能），以及解析应用程序和服务的能力。  

以下示例**安全性**部分配置 Windows 安全性，指定 *ServiceFabric/clusterA.contoso.com* 中的计算机位于群集中，并指定 *CONTOSO\usera* 拥有管理客户端访问权限：

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
            "IsAdmin": true
        }]
    }
},
```

> [!NOTE]
> 不应在域控制器上部署 Service Fabric。 使用计算机组或组托管服务帐户 (gMSA) 时，请确保 ClusterConfig.json 不包含域控制器的 IP 地址。
>
>

## <a name="next-steps"></a>后续步骤
在 ClusterConfig.JSON 文件中配置 Windows 安全性之后，请继续执行[创建 Windows 上运行的独立群集](service-fabric-cluster-creation-for-windows-server.md)中的群集创建过程。 

有关节点到节点安全性、客户端到节点安全性和基于角色的访问控制的详细信息，请参阅[群集安全方案](service-fabric-cluster-security.md)。

有关使用 PowerShell 或 FabricClient 进行连接的示例，请参阅[连接到安全群集](service-fabric-connect-to-secure-cluster.md)。
