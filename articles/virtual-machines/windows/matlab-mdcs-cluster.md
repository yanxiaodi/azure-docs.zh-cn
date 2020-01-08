---
title: 虚拟机上的 MATLAB 群集 | Microsoft Docs
description: 使用 Microsoft Azure 虚拟机可以创建 MATLAB 分布式计算服务器群集，运行计算密集型并行 MATLAB 工作负荷。
services: virtual-machines-windows
documentationcenter: ''
author: mscurrell
manager: gwallace
editor: ''
ms.assetid: e9980ce9-124a-41f1-b9ec-f444c8ea5c72
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: Windows
ms.workload: infrastructure-services
ms.date: 05/09/2016
ms.author: markscu
ms.openlocfilehash: d57d9bfa964759e639c2cf40d86bd603b1900ce7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103015"
---
# <a name="create-matlab-distributed-computing-server-clusters-on-azure-vms"></a>在 Azure VM 上创建 MATLAB 分布式计算服务器群集
使用 Microsoft Azure 虚拟机可以创建一个或多个 MATLAB 分布式计算服务器群集，运行计算密集型并行 MATLAB 工作负荷。 在 VM 上安装 MATLAB 分布式计算服务器软件以用作基本映像，并使用 Azure 快速入门模板或 Azure PowerShell 脚本（可在 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) 上获取）来部署和管理群集。 部署之后，可连接到群集来运行工作负荷。

## <a name="about-matlab-and-matlab-distributed-computing-server"></a>关于 MATLAB 和 MATLAB 分布式计算服务器
[MATLAB](https://www.mathworks.com/products/matlab/) 平台经过优化，可用于解决工程和科研问题。 需要进行大规模仿真和数据处理任务的 MATLAB 用户可以借助 MathWorks 并行计算产品，利用计算群集与网格服务来加快其计算密集型工作负荷的运行速度。 [并行计算工具箱](https://www.mathworks.com/products/parallel-computing/)可让 MATLAB 用户并行处理应用程序，并利用多核处理器、GPU 和计算群集。 [MATLAB 分布式计算服务器](https://www.mathworks.com/products/distriben/)可让 MATLAB 用户利用计算群集中的多台计算机。

通过使用 Azure 虚拟机，可以创建 MATLAB 分布式计算服务器群集，这些群集都可使用相同的机制以本地群集的形式提交并行工作，如交互式作业、批处理作业、独立的任务和通信任务。 相比于预配和使用传统的本地硬件，将 Azure 与 MATLAB 平台结合使用可带来诸多好处：有多种不同的虚拟机大小可选、可按需创建群集以便只为使用的计算资源付费，并且能够大规模测试模型。  

## <a name="prerequisites"></a>先决条件
* **客户端计算机** - 部署后，需要使用一台基于 Windows 的客户端计算机来与 Azure 和 MATLAB 分布式计算服务器群集通信。
* **Azure PowerShell** - 请参阅 [How to install and configure Azure PowerShell](/powershell/azure/overview)（如何安装和配置 Azure PowerShell），在客户端计算机上安装该软件。
* **Azure 订阅** - 如果没有订阅，只需要花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/free/)。 对于较大的群集，请考虑即用即付订阅或其他购买选项。
* **vCPU 配额** - 可能需要增大 vCPU 配额才能部署大型群集或多个 MATLAB 分布式计算服务器群集。 若要增加配额，可免费[建立联机客户支持请求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。
* **MATLAB、并行计算工具箱和 MATLAB 分布式计算服务器许可证** - 脚本假设所有许可证都使用 [MathWorks Hosted License Manager](https://www.mathworks.com/help/install/license-management.html)。  
* **MATLAB 分布式计算服务器软件** - 会安装在用作群集 VM 基本 VM 映像的 VM 上。

## <a name="high-level-steps"></a>大致步骤
若要对 MATLAB 分布式计算服务器群集使用 Azure 虚拟机，必须执行以下概要步骤。 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) 上的快速入门模板和脚本随附的文档中提供了详细说明。

1. **创建基本 VM 映像**  

   * 在此 VM 上下载并安装 MATLAB 分布式计算服务器软件。

     > [!NOTE]
     > 此过程可能需要几个小时，但使用的每个 MATLAB 版本只需执行此操作一次。   
     >
     >
2. **创建一个或多个群集**  

   * 使用提供的 PowerShell 脚本或使用快速入门模板，通过基本 VM 映像创建群集。   
   * 使用提供的 PowerShell 脚本管理群集，可列出、暂停、继续和删除群集。

## <a name="cluster-configurations"></a>群集配置
目前，通过群集创建脚本和模板，可让你创建单个 MATLAB 分布式计算服务器拓扑。 如果需要，可以另外创建一个或多个群集，每个群集可以包含不同数量的辅助角色 VM、使用不同的 VM 大小，等等。

### <a name="matlab-client-and-cluster-in-azure"></a>Azure 中的 MATLAB 客户端和群集
MATLAB 客户端节点、MATLAB 作业计划程序节点和 MATLAB 分布式计算服务器“辅助角色”节点全都配置为虚拟网络中的 Azure VM，如下图所示。


* 若要使用群集，请通过远程桌面连接到客户端节点。 客户端节点运行 MATLAB 客户端。
* 客户端节点具有可供所有辅助角色访问的文件共享。
* MathWorks Hosted License Manager 用于检查所有 MATLAB 软件的许可证。
* 默认情况下，会在辅助角色 VM 上为每个 vCPU 创建一个 MATLAB 分布式计算服务器辅助角色，但可以指定任意数目。

## <a name="use-an-azure-based-cluster"></a>使用基于 Azure 的群集
与其他类型的 MATLAB 分布式计算服务器群集一样，需要在 MATLAB 客户端（位于客户端 VM 上）中使用群集配置文件管理器来创建 MATLAB 作业计划程序群集配置文件。

![群集配置文件管理器](./media/matlab-mdcs-cluster/cluster_profile_manager.png)

## <a name="next-steps"></a>后续步骤
* 有关在 Azure 中部署和管理 MATLAB 分布式计算服务器群集的详细说明，请参阅包含模板和脚本的 [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/matlab-cluster) 存储库。
* 有关 MATLAB 和 MATLAB 分布式计算服务器的详细文档，请转到 [MathWorks 站点](https://www.mathworks.com/)。
