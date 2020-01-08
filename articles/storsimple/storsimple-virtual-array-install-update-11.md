---
title: 在 StorSimple 虚拟阵列上安装 Update 1.1 | Microsoft Docs
description: 介绍了如何使用 Azure 门户和本地 Web UI 为 StorSimple 虚拟阵列应用更新
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2018
ms.author: alkohli
ms.openlocfilehash: 88b903d68e4398b4e30b0b7435279c29bee6cd6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61408577"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>在 StorSimple 虚拟阵列上安装 Update 1.1

## <a name="overview"></a>概述

本文介绍了通过本地 Web UI 和 Azure 门户在 StorSimple 虚拟阵列上安装 Update 1.1 所需的步骤。

应用软件更新或修补程序，使 StorSimple Virtual Array 保持最新。 在应用更新之前，建议使卷或共享依次在主机和设备上脱机。 这能在最大程度上减少发生数据损坏的可能性。 在卷或共享离线后，还应手动备份设备。

> [!IMPORTANT]
> - Update 1.1 对应于设备上的 **10.0.10307.0** 软件版本。 有关此更新中新增功能的信息，请转到 [Update 1.1 发行说明](storsimple-virtual-array-update-11-release-notes.md)。
>
> - 请记住，安装更新或修补程序会重新启动设备。 假定 StorSimple Virtual Array 是单节点设备，任何正在进行的 I/O 都将中断，设备也会停机。
>
> - 只有当虚拟阵列运行 Update 1 时，才能在 Azure 门户中获取 Update 1.1。 对于运行 Update 0.6 版本的虚拟阵列，必须先安装 Update 1.0，然后再应用 Update 1.1。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

如果要运行 Update 0.2 和更高版本，建议通过 Azure 门户安装更新。 门户过程需要用户扫描、下载和安装更新。 根据 Virtual Array 运行的软件版本，通过 Azure 门户应用的更新会有所不同。

 - 如果虚拟阵列运行 Update 1，Azure 门户会直接在设备上安装 Update 1.1 (10.0.10307.0)。 此过程大约需要 10 分钟来完成。
 - 如果虚拟阵列在运行 Update 0.6，则通过两个阶段完成更新。 Azure 门户首先在设备上安装 Update 1.0 (10.0.10296.0)。 虚拟阵列进行重启，然后门户将在设备上安装 Update 1.1 (10.0.10307.0)。 此过程完成时间大约为 15 分钟。


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

完成安装后，转到 StorSimple Device Manager 服务。 选择“设备”  ，并选择并单击刚刚更新的设备。 转到“设置”>“管理”>“设备更新”  。 显示的软件版本应该是 **10.0.10307.0**。

![更新后的软件版本](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>使用本地 Web UI

通过两个步骤使用本地 Web UI：

* 下载更新或修补程序
* 安装更新或修补程序

> [!IMPORTANT] 
> **仅当运行 Update 1 (10.0.10296.0) 时才继续此更新。如果运行的是 Update 0.6，请先在设备上[安装 Update 1](storsimple-virtual-array-install-update-1.md)，然后再应用 Update 1.1。**

### <a name="download-the-update-or-the-hotfix"></a>下载更新或修补程序

执行以下步骤，从 Microsoft 更新目录下载 Update 1.1。

#### <a name="to-download-the-update-or-the-hotfix"></a>下载更新或修补程序

1. 启动 Internet Explorer，并转到 [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)。

2. 如果是首次在此计算机上使用 Microsoft 更新目录，请在系统提示是否安装 Microsoft 更新目录外接程序时单击“安装”  。

3. 在 Microsoft 更新目录的搜索框中，输入要下载的修补程序的知识库 (KB) 编号。 输入代表 Update 1.1 的 **4337628**，然后单击“搜索”  。
   
    此时会显示修补程序列表，例如 **StorSimple 虚拟阵列 Update 1.1**。
   
    ![搜索目录](./media/storsimple-virtual-array-install-update-11/download1.png)

4. 单击“下载”  。

5. 将这两个文件下载到一个文件夹中。 也可以将该文件夹复制到可通过设备访问的网络共享。

6. 打开文件所在的文件夹。

    ![程序包中的文件](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    可看到两个文件：
    -  Microsoft 更新独立程序包文件 `WindowsTH-KB3011067-x64`。 此文件用于更新设备软件。
    - 包含 6 月累计更新 `Windows8.1-KB4284815-x64` 的文件。 有关此汇总所含内容的详细信息，请转到 [6 月月度安全性汇总](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815)。

### <a name="install-the-update-or-the-hotfix"></a>安装更新或修补程序

安装更新或修补程序之前，请确保：

 - 更新或修补程序已本地下载到主机上，或可通过网络共享访问。
 - 虚拟阵列在运行 Update 1 (10.0.10296.0)。 如果运行的是 Update 0.6，请首先[安装 Update 1](storsimple-virtual-array-install-update-1.md)，然后再安装 Update 1.1。

此过程完成时间大约为 4 分钟。 执行以下步骤，安装更新或修补程序。

#### <a name="to-install-the-update-or-the-hotfix"></a>安装更新或修补程序

1. 在本地 Web UI 中，转到“维护”   > “软件更新”  。 记下运行的软件版本。 **仅当运行 Update 1 (10.0.10296.0) 时才继续此更新。如果运行的是 Update 0.6，请先在设备上[安装 Update 1](storsimple-virtual-array-install-update-1.md)，然后再应用 Update 1.1。**
   
    ![更新设备](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. 在“更新文件路径”  中，输入更新或修补程序的文件名。 也可以浏览到网络共享上的更新或修补程序安装文件。 单击“应用”  。
   
    ![更新设备](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. 显示一条警告。 假定 Virtual Array 为单节点设备，应用更新后，设备将重启并且会出现停机。 单击选中图标。
   
   ![更新设备](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. 更新启动。 成功更新设备后，该设备将重新启动。 本地 UI 在此期间不可访问。
   
    ![更新设备](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. 重新启动完成后，会转到“登录”  页。 若要验证设备软件是否已更新，请在本地 Web UI 中，转到“维护”   > “软件更新”  。 对于 Update 1.1，显示的软件版本应该是 **10.0.0.0.0.10307**。
   
   > [!NOTE]
   > 我们在本地 Web UI 和 Azure 门户中报告的软件版本稍有不同。 例如，针对同一版本，本地 Web UI 报告 **10.0.0.0.0.10307**，而 Azure 门户则报告 **10.0.10307.0**。
   
    ![更新设备](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. 重复步骤 2-4，使用文件 `Windows8.1-KB4284815-x64` 安装 Windows 安全修补程序。 安装后会重启 Virtual Array，用户需登录本地 Web UI。


## <a name="next-steps"></a>后续步骤

详细了解如何[管理 StorSimple Virtual Array](storsimple-ova-web-ui-admin.md)。
