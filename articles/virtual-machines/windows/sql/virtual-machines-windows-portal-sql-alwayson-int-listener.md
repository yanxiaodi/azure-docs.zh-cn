---
title: 在 Azure 虚拟机中创建 SQL Server 可用性组侦听器 | Microsoft Docs
description: 有关为 Azure 虚拟机中的 SQL Server Always On 可用性组创建侦听器的分步说明
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.openlocfilehash: c9c8379787619608421256120139f07c8dbd8d14
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102241"
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>在 Azure 中为 Always On 可用性组配置负载均衡器
本文说明如何在使用 Azure 资源管理器运行的 Azure 虚拟机中为 SQL Server Always On 可用性组创建负载均衡器。 当 SQL Server 实例位于 Azure 虚拟机上时，AlwaysOn 可用性组需要负载均衡器。 负载均衡器存储可用性组侦听器的 IP 地址。 如果可用性组跨多个区域，则每个区域都需要一个负载均衡器。

若要完成此任务，需要在使用 Resource Manager 运行的 Azure 虚拟机上部署一个 SQL Server 可用性组。 这两个 SQL Server 虚拟机必须属于同一个可用性集。 可以使用 [Microsoft 模板](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)在 Resource Manager 中自动创建可用性组。 此模板会自动创建内部负载均衡器。 

如果需要，可以[手动配置可用性组](virtual-machines-windows-portal-sql-availability-group-tutorial.md)。

本文要求事先配置可用性组。  

相关主题包括：

* [在 Azure VM (GUI) 中配置 Always On 可用性组](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [使用 Azure 资源管理器和 PowerShell 配置 VNet 到 VNet 连接](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

执行本文中的每个步骤可以在 Azure 门户中创建和配置负载均衡器。 完成该过程后，将配置群集，以将负载均衡器中的 IP 地址用于可用性组侦听器。

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>在 Azure 门户中创建并配置负载均衡器
在任务的这一部分，将执行以下步骤：

1. 在 Azure 门户中创建负载均衡器并配置 IP 地址。
2. 配置后端池。
3. 创建探测。 
4. 设置负载均衡规则。

> [!NOTE]
> 如果 SQL Server 实例在多个资源组和区域中，请执行每个步骤两次，在每个资源组中各执行一次。
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>步骤 1：创建负载均衡器并配置 IP 地址
首先，请创建负载均衡器。 

1. 在 Azure 门户中，打开包含 SQL Server 虚拟机的资源组。 

2. 在资源组中，单击“添加”。

3. 搜索“负载均衡器”，并在搜索结果中选择 **Microsoft** 发布的“负载均衡器”。

4. 在“负载均衡器”边栏选项卡上，单击“创建”。

5. 在“创建负载均衡器”对话框中配置负载均衡器，如下所示：

   | 设置 | ReplTest1 |
   | --- | --- |
   | **名称** |表示负载均衡器的文本名称。 例如 **sqlLB**。 |
   | 类型 |**内部**：大多数实施方案使用内部负载均衡器，它可让同一虚拟网络中的应用程序连接到可用性组。  </br> **外部**：可让应用程序通过公共 Internet 连接连接到可用性组。 |
   | **虚拟网络** |选择 SQL Server 实例所在的虚拟网络。 |
   | **子网** |选择 SQL Server 实例所在的子网。 |
   | IP 地址分配 |**静态** |
   | **专用 IP 地址** |指定子网中的某个可用 IP 地址。 在群集上创建侦听器时，请使用此 IP 地址。 本文稍后的 PowerShell 脚本会将此地址用于 `$ILBIP` 变量。 |
   | **订阅** |如果有多个订阅，可能会显示此字段。 选择要与此资源关联的订阅。 它通常是与可用性组的所有资源相同的订阅。 |
   | **资源组** |选择 SQL Server 实例所在的资源组。 |
   | **Location** |选择 SQL Server 实例所在的 Azure 位置。 |

6. 单击“创建”。 

Azure 将创建负载均衡器。 该负载均衡器属于特定的网络、子网、资源组和位置。 在 Azure 完成任务后，请在 Azure 中验证负载均衡器设置。 

### <a name="step-2-configure-the-back-end-pool"></a>步骤 2：配置后端池
Azure 将后端地址池称作*后端池*。 在本例中，后端池是可用性组中两个 SQL Server 实例的地址。 

1. 在资源组中，单击已创建的负载均衡器。 

2. 在“设置”中，单击“后端池”。

3. 在“后端池”中，单击“添加”创建后端地址池。 

4. 在“添加后端池”中的“名称”下，键入后端池的名称。

5. 在“虚拟机”下，单击“添加虚拟机”。 

6. 在“选择虚拟机”下，单击“选择可用性集”，并指定 SQL Server 虚拟机所属的可用性集。

7. 选择可用性集后，请单击“选择虚拟机”，选择在可用性组中托管 SQL Server 实例的两个虚拟机，然后单击“选择”。 

8. 单击“确定”，关闭“选择虚拟机”和“添加后端池”边栏选项卡。 

Azure 将更新后端地址池的设置。 现在，可用性集具有包含两个 SQL Server 实例的池。

### <a name="step-3-create-a-probe"></a>步骤 3：创建探测
探测定义 Azure 如何确认哪一个 SQL Server 实例当前拥有可用性组侦听器。 Azure 根据创建探测时定义的端口上的 IP 地址来探测服务。

1. 在负载均衡器的“设置”边栏选项卡上，单击“运行状况探测”。 

2. 在“运行状况探测”边栏选项卡上，单击“添加”。

3. 在“添加探测”边栏选项卡上配置探测。 使用以下值配置探测：

   | 设置 | ReplTest1 |
   | --- | --- |
   | **名称** |表示探测的文本名称。 例如 **SQLAlwaysOnEndPointProbe**。 |
   | **协议** |**TCP** |
   | 端口 |可以使用任何可用的端口。 例如 *59999*。 |
   | **间隔** |*5* |
   | **不正常阈值** |*2* |

4.  单击 **“确定”** 。 

> [!NOTE]
> 确保指定的端口已在两个 SQL Server 实例的防火墙上打开。 这两个实例需要所用 TCP 端口的入站规则。 有关详细信息，请参阅 [Add or Edit Firewall Rule](https://technet.microsoft.com/library/cc753558.aspx)（添加或编辑防火墙规则）。 
> 
> 

Azure 创建探测，并使用它来测试哪个 SQL Server 实例具有可用性组的侦听器。

### <a name="step-4-set-the-load-balancing-rules"></a>步骤 4：设置负载均衡规则
负载均衡规则设置负载均衡器将流量路由到 SQL Server 实例的方式。 对此负载均衡器，需要启用直接服务器返回，因为在两个 SQL Server 实例中，每次只有一个拥有可用性组侦听器资源。

1. 在负载均衡器的“设置”边栏选项卡上，单击“负载均衡规则”。 

2. 在“负载均衡规则”边栏选项卡上，单击“添加”。

3. 使用“添加负载均衡规则”边栏选项卡配置负载均衡规则。 使用以下设置： 

   | 设置 | ReplTest1 |
   | --- | --- |
   | **名称** |表示负载均衡规则的文本名称。 例如 **SQLAlwaysOnEndPointListener**。 |
   | 协议 |**TCP** |
   | **端口** |*1433* |
   | **后端端口** |*1433*.将忽略此值，因为此规则使用“浮动 IP (直接服务器返回)”。 |
   | **探测** |使用为此负载均衡器创建的探测的名称。 |
   | **会话暂留** |**无** |
   | **空闲超时(分钟)** |*4* |
   | **浮动 IP (直接服务器返回)** |**已启用** |

   > [!NOTE]
   > 可能需要在边栏选项卡中向下滚动才能查看所有设置。
   > 

4. 单击 **“确定”** 。 
5. Azure 将配置负载均衡规则。 负载均衡器现已配置为将流量路由到托管可用性组侦听器的 SQL Server 实例。 

此时，资源组有一个连接到这两个 SQL Server 计算机的负载均衡器。 负载均衡器还包含 SQL Server Always On 可用性组侦听器的 IP 地址，以便任一计算机可以响应针对可用性组的请求。

> [!NOTE]
> 如果 SQL Server 实例位于两个不同的区域，请在另一个区域重复上述步骤。 每个区域都需要一个负载均衡器。 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>将群集配置为使用负载均衡器 IP 地址
下一步是在群集上配置侦听器，然后将侦听器联机。 请执行以下操作： 

1. 在故障转移群集上创建可用性组侦听器。 

2. 使侦听器联机。

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>步骤 5：在故障转移群集上创建可用性组侦听器
在此步骤中，在故障转移群集管理器和 SQL Server Management Studio 中手动创建可用性组侦听器。

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>验证侦听器的配置

如果正确配置了群集资源和依赖项，应能够查看 SQL Server Management Studio 中的侦听器。 若要设置侦听器端口，请执行以下操作：

1. 启动 SQL Server Management Studio，并连接到主副本。

2. 转到“AlwaysOn 高可用性” > “可用性组” > “可用性组侦听器”。  
    现在应看到在故障转移群集管理器中创建的侦听器名称。 

3. 右键单击侦听器名称，并单击“属性”。

4. 在“端口”框中，通过使用先前使用过的 $EndpointPort 为可用性组侦听器指定端口号（默认值为 1433），然后单击“确定”。

现在，在 Resource Manager 模式下运行的 Azure 虚拟机中有了一个可用性组。 

## <a name="test-the-connection-to-the-listener"></a>测试与侦听器的连接
若要测试连接，请执行以下操作：

1. 通过 RDP 连接到同一虚拟网络中不拥有副本的 SQL Server 实例。 此服务可以是群集中的其他 SQL Server 实例。

2. 使用 sqlcmd 实用工具测试连接。 例如，以下脚本通过侦听器与 Windows 身份验证来与主副本建立 **sqlcmd** 连接：
   
        sqlcmd -S <listenerName> -E

SQLCMD 连接会自动连接到托管主副本的 SQL Server 实例。 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>为其他可用性组创建一个 IP 地址

每个可用性组使用单独的侦听器。 每个侦听器都有自己的 IP 地址。 使用同一个负载均衡器以保留其他侦听器的 IP 地址。 创建可用性组后，请将 IP 地址添加到负载均衡器，然后配置侦听器。

若要使用 Azure 门户将 IP 地址添加到负载均衡器，请执行以下操作：

1. 在 Azure 门户中，打开包含负载均衡器的资源组，并单击负载均衡器。 

2. 在“设置”下面，单击“前端 IP 池”，然后单击“添加”。 

3. 在“添加前端 IP 地址”下，为前端分配名称。 

4. 验证“虚拟网络”和“子网”是否与 SQL Server 实例相同。

5. 设置侦听器的 IP 地址。 
   
   >[!TIP]
   >可将 IP 地址设置为静态，并键入子网中当前未使用的地址。 也可将 IP 地址设置为动态，并保存新的前端 IP 池。 执行此操作时，Azure 门户会自动向池分配可用的 IP 地址。 然后可以重新打开前端 IP 池，并将分配更改为静态。 

6. 保存侦听器的 IP 地址。 

7. 使用以下设置添加运行状况探测：

   |设置 |ReplTest1
   |:-----|:----
   |**名称** |用于标识探测的名称。
   |协议 |TCP
   |端口 |一个未使用的 TCP 端口，必须在所有虚拟机上可用。 此端口不能用于任何其他用途。 两个侦听器不能使用相同的探测端口。 
   |间隔 |探测尝试之间的时间长短。 使用默认值 (5)。
   |不正常阈值 |在将虚拟机视为运行状况不正常之前连续阈值应失败的次数。

8. 单击“确定”保存探测。 

9. 创建负载均衡规则。 单击“负载均衡规则”，然后单击“+ 添加”。

10. 使用以下设置配置新的负载均衡规则：

    |设置 |ReplTest1
    |:-----|:----
    |**名称** |用于标识负载均衡规则的名称。 
    |“前端 IP 地址” |选择所创建的 IP 地址。 
    |**协议** |TCP
    |端口 |使用 SQL Server 实例正在使用的端口。 在不更改的情况下，默认实例使用端口 1433。 
    |后端端口 |使用与“端口”相同的值。
    |后端池 |包含具有 SQL Server 实例的虚拟机的池。 
    |运行状况探测 |选择所创建的探测。
    |**会话持久性** |无
    |**空闲超时(分钟)** |默认值 (4)
    |**浮动 IP (直接服务器返回)** | Enabled

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>将可用性组配置为使用新 IP 地址

若要完成配置群集，请重复配置第一个可用性组时所遵照的步骤。 也就是说，[将群集配置为使用新 IP 地址](#configure-the-cluster-to-use-the-load-balancer-ip-address)。 

为侦听器添加了 IP 地址后，可执行以下操作配置其他可用性组： 

1. 验证两个 SQL Server 虚拟机上新 IP 地址的探测端口是否处于打开状态。 

2. [在群集管理器中，添加客户端访问点](#addcap)。

3. [配置可用性组的 IP 资源](#congroup)。

   >[!IMPORTANT]
   >创建 IP 地址时，请使用已添加到负载均衡器的 IP 地址。  

4. [使 SQL Server 可用性组资源依赖于客户端接入点](#dependencyGroup)。

5. [使客户端接入点资源依赖于 IP 地址](#listname)。
 
6. [在 PowerShell 中设置群集参数](#setparam)。

将可用性组配置为使用新 IP 地址时，请配置到侦听器的连接。 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>向分布式可用性组添加负载均衡规则

如果分布式可用性组中使用了可用性组，则负载均衡器需应用其他规则。 此规则会存储分布式可用性组侦听器所用的端口。

>[!IMPORTANT]
>此步骤仅适用于[分布式可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)中未涉及到任何可用性组的情况。 

1. 在参与到分布式可用性组的每个服务器上，针对分布式可用性组侦听器 TCP 端口创建一个入站规则。 在很多示例中，文档都使用 5022。 

1. 在 Azure 门户中，依次单击负载均衡器、“负载均衡规则”和“+添加”。 

1. 通过以下设置创建负载均衡规则：

   |设置 |ReplTest1
   |:-----|:----
   |**名称** |用于定义分布式可用性组的负载均衡规则的名称。 
   |“前端 IP 地址” |与可用性组使用相同的前端 IP 地址。
   |协议 |TCP
   |端口 |5022 - 用于 [分布式可用性组终结点侦听器](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups) 的端口。</br> 可为任何可用端口。  
   |后端端口 | 5022 - 使用与“端口”相同的值。
   |后端池 |包含具有 SQL Server 实例的虚拟机的池。 
   |运行状况探测 |选择所创建的探测。
   |**会话持久性** |无
   |**空闲超时(分钟)** |默认值 (4)
   |**浮动 IP (直接服务器返回)** | Enabled

在参与到分布式可用性组的其他可用性组上，针对负载均衡器重复这些步骤。

如果要限制 Azure 网络安全组的访问权限，请确保“允许”规则包含后端 SQL Server VM IP 地址、可用性组侦听器的负载均衡器浮动 IP 地址、群集核心 IP 地址（如适用）。

## <a name="next-steps"></a>后续步骤

- [在位于不同区域的 Azure 虚拟机上配置 SQL Server Always On 可用性组](virtual-machines-windows-portal-sql-availability-group-dr.md)
