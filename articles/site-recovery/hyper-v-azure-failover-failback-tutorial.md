---
title: 在灾难恢复到 Azure 期间使用 Azure Site Recovery 对 Hyper-V VM 进行故障转移和故障回复 | Microsoft Docs
description: 了解如何在灾难恢复到 Azure 期间使用 Azure Site Recovery 服务对 Hyper-V VM 进行故障转移和故障回复。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4b9680b00905126d261562d7bec64bb931c1cda3
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845714"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>对复制到 Azure 的 Hyper-V VM 进行故障转移和故障回复

本教程介绍如何将 Hyper-V VM 故障转移到 Azure。 故障转移后，可故障回复到本地站点（若可行）。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 验证 Hyper-V VM 属性以检查是否符合 Azure 要求
> * 运行到 Azure 的故障转移
> * 从 Azure 故障回复到本地
> * 反向复制本地 VM，使之开始再次复制到 Azure

本教程为系列教程中的第五个教程。 它假设你已完成前面教程中的任务。    

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)
3. 为 [Hyper-V VM](tutorial-hyper-v-to-azure.md) 或为[托管在 System Center VMM 云中的 Hyper-V VM](tutorial-hyper-v-vmm-to-azure.md) 设置灾难恢复
4. [运行灾难恢复演练](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>准备故障转移和故障回复

确保 VM 上无快照，并且本地 VM 在故障回复期间已关闭。 这有助于确保复制期间的数据一致性。 在故障回复期间不要打开本地 VM。 

故障转移和故障回复具有三个阶段：

1. **故障转移到 Azure**：将 Hyper-V VM 从本地站点故障转移到 Azure。
2. **故障回复到本地**：在本地站点可用时，将 Azure VM 故障转移到本地站点。 它开始将数据从 Azure 同步到本地，并在完成同步后，启动本地的 VM。  
3. **反向复制本地 VM**：故障回复到本地后，反向复制本地 VM，开始将其复制到 Azure。

## <a name="verify-vm-properties"></a>验证 VM 属性

在故障转移前验证 VM 属性，确保 VM 符合 [Azure 要求](hyper-v-azure-support-matrix.md#replicated-vms)。

在“受保护的项”  中，单击“复制的项”  >“虚拟机”。

1. “复制的项”窗格中具有 VM 信息、运行状况状态和最新可用恢复点的摘要  。 单击“属性”  可查看更多详细信息。

1. 在“计算和网络”  中，可修改 Azure 名称、资源组、目标大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和托管的磁盘设置

1. 可查看和修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。

1. 在“磁盘”  中，可以看到关于 VM 上的操作系统和数据磁盘的信息。

## <a name="failover-to-azure"></a>故障转移到 Azure

1. 在“设置” > “复制的项”中，单击“VM”>“故障转移”    。
2. 在“故障转移”中，选择“最新”恢复点   。 
3. 选择“在开始故障转移前关闭计算机”  。 在触发故障转移之前，Site Recovery 会尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。 
4. 验证故障转移后，单击“提交”  。 这会删除所有可用的恢复点。

> [!WARNING]
> **不会取消正在进行的故障转移**：如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>将 Azure VM 故障回复到本地并反向复制本地 VM

故障回复操作基本上是从 Azure 故障转移到本地站点，在反向复制中它会开始将 VM 从本地站点复制到 Azure。

1. 在“设置” > “复制的项”中，单击“VM”>“计划内故障转移”    。
2. 在“确认计划故障转移”中，验证故障转移方向（从 Azure），并选择源和目标位置  。
3. 选择“在故障转移前同步数据(仅同步增量更改)”  。 此选项可最大程度减小 VM 停机时间，因为它在不关闭 VM 的情况下进行同步操作。
4. 启动故障转移。 可以在“**作业**”选项卡上跟踪故障转移进度。
5. 请在完成初始数据同步且准备好关闭 Azure VM 后，单击“作业”> 计划故障转移作业名称 >“完成故障转移”   。 这会关闭 Azure VM，将最新更改传输到本地，并启动本地 VM。
6. 登录到本地 VM，检查它是否按预期方式可用。
7. 本地 VM 当前处于“提交挂起”状态。  单击“提交”。  这会删除 Azure VM 及其磁盘，并准备要进行反向复制的本地 VM。
若要开始将本地 VM 复制到 Azure，请启用“反向复制”  。 这会触发复制自关闭 Azure VM 以来发生的增量更改。  
