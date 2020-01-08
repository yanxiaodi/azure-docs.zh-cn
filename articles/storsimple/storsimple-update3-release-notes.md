---
title: StorSimple 8000 系列 Update 3 发行说明 | Microsoft Docs
description: 介绍 StorSimple 8000 系列 Update 3 的新功能、问题和解决方法。
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d18feba4ded3dfccb8f774112a7dc8d42b12f1d5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60530959"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>适用于 StorSimple 8000 系列设备的 Update 3 发行说明

## <a name="overview"></a>概述
以下发行说明描述 StorSimple 8000 系列 Update 3 的新功能，并标识其重要的待解决问题。 其中也包含此版本中随附的 StorSimple 软件更新列表。 

Update 3 可应用于任何运行 Release (GA)、Update 0.1 到 Update 2.2 的 StorSimple 设备。 与 Update 3 相关联的设备版本是 6.3.9600.17759。

在 StorSimple 解决方案中部署更新之前，请查看发行说明中所包含的信息。

> [!IMPORTANT]
> * Update 3 包含设备软件、LSI 驱动程序和固件，以及 Storport 和 Spaceport 更新。 安装此更新大约需要 1.5-2 小时。 
> * 对于新版本，由于我们分阶段推出更新，可能不能立即看到更新。 请等待几天，再次扫描更新，因为很快就会提供这些更新。
> 
> 

## <a name="whats-new-in-update-3"></a>Update 3 中有哪些新增功能
Update 3 中以下重大改进和 Bug 修复。

* **自动化空间回收更改** — 从 Update 3 开始，空间回收算法是在系统的备用控制器上运行，使得执行速度更快。 有关执行空间回收所需的端口的详细信息，请参阅 [StorSimple 网络要求](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)。
* **性能增强** — Update 3 已提高对云的读写性能。
* **与迁移相关的改进** — 在此版本中，对从 5000/7000 系列设备到 8000 系列设备的迁移功能，进行了多个 Bug 修复和改进。 有关如何使用迁移功能的详细信息，请转到[从 5000/7000 系列设备到 8000 系列设备的迁移](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b)。 
* **与监视相关的修复** — 在此版本中，与监视图表、服务仪表板和设备仪表板相关的 Bug 均已修复。

## <a name="issues-fixed-in-update-3"></a>在 Update 3 中修复的问题
下表提供在 Update 3 中已修复问题的摘要。    

| 否 | Feature | 问题 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- |
| 第 |主机端数据迁移 |在早期版本中，在主机端数据迁移期间，StorSimple 云工具会进入脱机状态。 在此版本中已修复了此问题。 |否 |是 |
| 2 |本地固定卷 |在以前的版本中，本地固定卷存在与 I/O 失败、卷转换失败和数据路径失败相关的问题。 在此版本中已找到这些问题的根本原因并进行了修复。 |是 |否 |
| 3 |监视 |有多个与报告单位和监视以及设备仪表板图表相关的问题，其中针对本地固定卷显示了不正确的信息。 在此版本中已修复这些问题。 |是 |否 |
| 4 |大量写入 I/O |使用 StorSimple 时处理涉及大量写入操作的工作负荷时，用户会遇到工作集被分层到云中罕见错误。 在此版本中已修复这一 bug。 |是 |是 |
| 5 |备份 |在某些极少数情况下，在早期版本的软件中，用户执行远程克隆备份时，会遇到云错误，进而使操作出错。在此版本中，已修复此问题并且操作可成功完成。 |是 |是 |
| 6 |备份策略 |在某些极少数情况下，在早期版本的软件中，存在与删除备份策略相关的错误。 在此版本中已修复了此问题。 |是 |是 |

## <a name="known-issues-in-update-3"></a>Update 3 中的已知问题
下表提供了此版本中已知问题的摘要。

| 不。 | Feature | 问题 | 注释/解决方法 | 适用于物理设备 | 适用于虚拟设备 |
| --- | --- | --- | --- | --- | --- |
| 第 |磁盘仲裁 |在极少数情况下，如果 8600 设备的 EBOD 机箱中的大部分磁盘断开连接，导致没有磁盘仲裁，则会使存储池脱机。 即使磁盘重新连接，存储池也将保持脱机状态。 |需要重新启动设备。 如果问题仍然存在，请联系 Microsoft 支持部门以了解后续步骤。 |是 |否 |
| 2 |控制器 ID 错误 |更换控制器后，控制器 0 可能显示为控制器 1。 在更换控制器的过程中，从对等节点加载映像时，控制器 ID 刚开始可能显示为对等控制器的 ID。 在极少数情况下，此行为也可能在系统重新启动后出现。 |不需要用户操作。 控制器更换过程完成后，这种情况会自动解决。 |是 |否 |
| 3 |存储帐户 |此版本不支持使用存储服务删除存储帐户， 否则会导致无法检索用户数据。 | |是 |是 |
| 4 |设备故障转移 |不支持从同一源设备将某个卷容器多次故障转移到不同的目标设备。 从单个不活动的设备故障转移到多个设备，会使第一个故障转移设备上卷容器丢失数据所有权。 进行此类故障转移后，在 Azure 经典门户中查看这些卷容器时，会发现它们的显示或表现有所不同。 | |是 |否 |
| 5 |安装 |安装 StorSimple Adapter for SharePoint 期间，需要提供设备 IP 才能成功完成安装。 | |是 |否 |
| 6 |Web 代理 |如果 Web 代理配置将 HTTPS 作为指定的协议，则设备到服务通信将受到影响，并且设备将进入脱机状态。 在此过程中会生成支持包，从而耗用设备上的大量资源。 |请确保 Web 代理 URL 将 HTTP 作为指定的协议。 有关详细信息，请转至[配置设备的 Web 代理](storsimple-8000-configure-web-proxy.md)。 |是 |否 |
| 7 |Web 代理 |如果在注册的设备上配置并启用 Web 代理，将需要重新启动设备上的主动控制器。 | |是 |否 |
| 8 |云高延迟和高 I/O 工作负载 |当 StorSimple 设备同时遇到非常高的云延迟（秒级）和高 I/O 工作负载情况时，设备卷将进入降级状态，并且 I/O 可能会出现故障，发生“设备未就绪”错误。 |需要手动重新启动设备控制器或执行设备故障转移，才可以从这种情况中恢复。 |是 |否 |
| 9 |Azure PowerShell |使用 StorSimple cmdlet **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** 选择第一个对象以便创建新的 **VolumeContainer** 对象时，该 cmdlet 将返回所有对象。 |使用括号包装该 cmdlet，如下所示： **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |是 |是 |
| 10 |迁移 |当传递多个卷容器进行迁移时，只有第一个卷容器的最新备份的 ETA 准确。 此外，在迁移第一个卷容器中的前 4 个备份后，将开始并行迁移。 |建议一次迁移一个卷容器。 |是 |否 |
| 11 |迁移 |还原后，不会将卷添加到备份策略或虚拟磁盘组。 |需要将这些卷添加到备份策略以创建备份。 |是 |是 |
| 12 |迁移 |迁移完成后，5000/7000 系列设备不得访问已迁移的数据容器。 |建议在迁移完成并提交之后删除迁移的数据容器。 |是 |否 |
| 13 |克隆和 DR |运行更新 1 的 StorSimple 设备不能对运行更新 1 前的软件的设备克隆或执行灾难恢复。 |需要将目标设备更新为更新 1 以允许这些操作 |是 |是 |
| 14 |迁移 |当卷组没有关联的卷时，5000-7000 系列设备上用于迁移的配置备份可能会出现故障。 |删除所有不含关联卷的空卷组，并重新配置备份。 |是 |否 |
| 15 |Azure PowerShell cmdlet 和本地固定卷 |无法通过 Azure PowerShell cmdlet 创建本地固定卷。 （通过 Azure PowerShell 创建的任何卷都会被分层。） |始终使用 StorSimple Manager 服务来配置本地固定卷。 |是 |否 |
| 16 |本地固定卷的可用空间 |如果删除本地固定卷，可能不会立即更新新卷的可用空间。 StorSimple Manager 服务大约会每隔一小时更新本地可用的空间。 |请等待一个小时，再尝试创建新卷。 |是 |否 |
| 17 |本地固定卷 |还原作业会在“备份目录”中显示临时快照备份，但仅会在还原作业期间显示。 此外，它还在“**备份策略**”页上显示一个前缀为 **tmpCollection** 的虚拟磁盘组，但仅会在还原作业期间显示。 |如果还原作业仅具有本地固定卷或混合了本地固定卷与分层卷，则可能发生此问题。 如果还原作业仅包含分层卷，不会发生此问题。 无需用户干预。 |是 |否 |
| 18 |本地固定卷 |如果取消还原作业，并随即发生控制器故障转移，还原作业会显示“**失败**”而不是“**已取消**”。 如果还原作业失败，并随即发生控制器故障转移，还原作业会显示“**已取消**”而不是“**失败**’。 |如果还原作业仅具有本地固定卷或混合了本地固定卷与分层卷，则可能发生此问题。 如果还原作业仅包含分层卷，不会发生此问题。 无需用户干预。 |是 |否 |
| 19 |本地固定卷 |如果取消还原作业，或者如果恢复失败，且发生控制器故障转移，“**作业**”页上会显示其他还原作业。 |如果还原作业仅具有本地固定卷或混合了本地固定卷与分层卷，则可能发生此问题。 如果还原作业仅包含分层卷，不会发生此问题。 无需用户干预。 |是 |否 |
| 20 |本地固定卷 |如果尝试将分层卷（通过更新 1.2 或更早版本创建和克隆）转换为本地固定卷，并且设备空间不足或云服务中断，则克隆会受损。 |仅通过更新 2.1 前的软件创建和克隆的卷会发生该问题。 这种情况应该很少见。 | | |
| 21 |卷转换 |卷转换正在进行时（分层到本地固定，反之亦然），请勿更新连接到此卷上的 ACR。 更新 ACR 可能导致数据损坏。 |如果需要，请在卷转换之前更新 ACR，不要在转换进行时执行任何进一步的 ACR 更新。 | | |
| 22 |更新 |应用 Update 3 时，Azure 经典门户的“维护”  页会显示以下与 Update 2 相关的消息 -“StorSimple 8000 系列 Update 2 包括适用于 Microsoft 的功能，当我们检测到潜在问题时，Microsoft 会主动收集设备的日志信息”。 该消息可能会产生误导，因为它指示设备会被更新到 Update 2。 设备成功更新到 Update 3 后，此消息将消失。 |未来版本中将解决此行为。 |是 |否 |

## <a name="controller-and-firmware-updates-in-update-3"></a>Update 3 中的控制器和固件更新
此版本包含 LSI 驱动程序和固件更新。 有关如何安装 LSI 驱动程序和固件更新的详细信息，请参阅在 StorSimple 设备上[安装 Update 3](storsimple-install-update-3.md)。

## <a name="virtual-device-updates-in-update-3"></a>Update 3 中的虚拟设备更新
此更新不能应用于 StorSimple 云工具（也称为虚拟设备）。 将需要新建虚拟设备。 

## <a name="next-step"></a>后续步骤
了解如何在 StorSimple 设备上[安装 Update 3](storsimple-install-update-3.md)。

