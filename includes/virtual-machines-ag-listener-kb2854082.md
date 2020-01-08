---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172898"
---
接下来，如果群集中的任何服务器运行的是 Windows Server 2008 R2 或 Windows Server 2012，则必须验证群集中的每个本地服务器或 Azure VM 上是否安装了修补程序 [KB2854082](https://support.microsoft.com/kb/2854082)。 位于群集中但不在可用性组中的任何服务器或 VM 也应安装此修补程序。

在每个群集节点的远程桌面会话中，将 [KB2854082](https://support.microsoft.com/kb/2854082) 下载到本地目录。 然后，按顺序在每个群集节点上安装该修补程序。 如果群集节点上当前运行了群集服务，修补程序安装结束后会重新启动服务器。

> [!WARNING]
> 停止群集服务或重新启动服务器会影响群集和可用性组的仲裁运行状况，并可能导致群集进入脱机状态。 若要在安装过程中维持群集的高可用性，请确保：
> 
> * 群集处于最佳仲裁运行状况。 
> * 在任何节点上安装此修补程序之前，所有群集节点均处于联机状态。
> * 在集群中的任何其他节点上安装此修补程序之前，请允许在一个节点上完成此修补程序的整个安装过程，包括完全重启服务器。
> 
> 

