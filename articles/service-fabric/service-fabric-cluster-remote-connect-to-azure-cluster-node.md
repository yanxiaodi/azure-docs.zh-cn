---
title: 远程连接 Azure Service Fabric 群集节点 | Microsoft Docs
description: 了解如何远程连接到规模集实例（Service Fabric 群集节点）。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: atsenthi
ms.openlocfilehash: 12508fd5297691f06bce46e056527672083c3a91
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599930"
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>远程连接到虚拟机规模集实例或群集节点
在 Azure 中运行的 Service Fabric 群集中，定义的每个群集节点类型都会[设置虚拟机单独规模](service-fabric-cluster-nodetypes.md)。  可以远程连接到特定规模集实例（群集节点）。  不同于单实例 VM，规模集实例本身没有虚拟 IP 地址。 可能很难找到可用来远程连接到特定实例的 IP 地址和端口。

若要查找可用于远程连接到特定实例的 IP 地址和端口，请完成以下步骤。

1. 获取远程桌面协议 (RDP) 的入站 NAT 规则。

    通常，群集中定义的每种节点类型有自己的虚拟 IP 地址和专用的负载均衡器。 默认情况下, 使用以下格式命名节点类型的负载均衡器:*LB-{cluster-name}-{node type}* ;例如, *mycluster-前端*。 
    
    在 Azure 门户中的负载均衡器页上，选择“设置” > “入站 NAT 规则”： 

    ![负载均衡器入站 NAT 规则](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/lb-window.png)

    以下屏幕截图显示名为 FrontEnd 的节点类型的入站 NAT 规则： 

    ![负载均衡器入站 NAT 规则](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/nat-rules.png)

    对于每个节点，IP 地址显示在“DESTINATION”列中，“TARGET”列提供规模集实例，“SERVICE”列提供端口号。 对于远程连接，从端口 3389 开始按升序将端口分配到每个节点。

    还可以在群集资源管理器模板的 `Microsoft.Network/loadBalancers` 部分找到入站 NAT 规则。
    
2. 若要确认节点的入站端口到目标端口映射，可以单击其规则并查看“目标端口”值。 以下屏幕截图显示上一步中“FrontEnd (实例 1)”节点的入站 NAT 规则。 请注意，虽然（入站）端口号为 3390，但是目标端口映射到端口 3389，即目标上 RDP 服务的端口。  

    ![目标端口映射](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/port-mapping.png)

    默认情况下，对于 Windows 群集，目标端口为端口 3389，它映射到目标节点上的 RDP 服务。 对于 Linux 群集，目标端口为端口 22，它映射到安全外壳 (SSH) 服务。

3. 远程连接到特定节点（规模集实例）。 可以使用创建群集时设置的用户名和密码，也可使用已配置的其他任意凭据。 

    以下屏幕截图显示在 Windows 群集中使用远程桌面连接连接到“FrontEnd (实例 1)”节点：
    
    ![远程桌面连接](./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/rdp-connect.png)

    在 Linux 节点上，可以借助 SSH 进行连接（为简洁起见，下例重复使用相同的 IP 地址和端口）：

    ``` bash
    ssh SomeUser@40.117.156.199 -p 3390
    ```


有关后续步骤，请阅读以下文章：
* 请参阅[“随地部署”功能的概述及其与 Azure 托管群集的比较](service-fabric-deploy-anywhere.md)。
* 了解[群集安全性](service-fabric-cluster-security.md)。
* 部署后在群集 VM 上[更新 RDP 端口范围值](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 为群集 VM [更改管理员用户名和密码](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

