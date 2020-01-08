---
title: '将 VM 的 D: 盘设为数据磁盘 | Microsoft Docs'
description: '介绍如何更改 Windows VM 的盘符，以使用 D: 驱动器作为数据驱动器。'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 846bb7a5ea6c3f363a2811cf3feb30e37ff30504
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079879"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>使用 D: 盘作为 Windows VM 上的数据驱动器
如果应用程序需要使用 D 盘存储数据，请按照以下说明使用其他驱动器号作为临时磁盘。 切勿使用临时磁盘来存储需要保存的数据。

如果调整虚拟机大小或**停止（解除分配）** 虚拟机，这可能会触发将虚拟机放置于新虚拟机监控程序的操作。 计划中或计划外的维护事件也可能触发此放置操作。 在此方案中，临时磁盘将重新分配给第一个可用的盘符。 如果应用程序明确需要 D: 盘，则需要遵循以下步骤暂时移动 pagefile.sys，并连接新的数据磁盘并为其分配驱动器号 D，再将 pagefile.sys 移回到临时驱动器。 完成后，如果 VM 移到不同的虚拟机监控程序，Azure 将不收回 D:。

有关 Azure 如何使用临时磁盘的详细信息，请参阅 [Understanding the temporary drive on Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)（了解 Microsoft Azure 虚拟机上的临时驱动器）

## <a name="attach-the-data-disk"></a>附加数据磁盘
首先，需要将数据磁盘附加到虚拟机。 若要使用门户执行此操作，请参阅[如何在 Azure 门户中附加托管数据磁盘](attach-managed-disk-portal.md)。

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>将 pagefile.sys 暂时移到 C 驱动器
1. 连接到虚拟机。 
2. 右键单击“开始”菜单，并选择“系统”。
3. 在左侧菜单中，选择“高级系统设置”。
4. 在“性能”部分中，选择“设置”。
5. 选择“高级”选项卡。
6. 在“虚拟内存”部分中，选择“更改”。
7. 选择 **C** 盘，并依次单击“系统管理的大小”、“设置”。
8. 选择 **D** 盘，并依次单击“无分页文件”、“设置”。
9. 单击“应用”。 你会收到警告，指出计算机需要重新启动才能使更改生效。
10. 重启虚拟机。

## <a name="change-the-drive-letters"></a>更改驱动器号
1. VM 重新启动后，重新登录到 VM。
2. 单击“开始”菜单，键入 **diskmgmt.msc**，并按 Enter。 此时会启动“磁盘管理”。
3. 右键单击 **D**（临时存储驱动器），并选择“更改驱动器号和路径”。
4. 在“驱动器号”下，选择一个新驱动器，如 **T**，并单击“确定”。 
5. 右键单击数据磁盘，并选择“更改驱动器号和路径”。
6. 在“驱动器号”下，选择驱动器 **D**，并单击“确定”。 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>将 pagefile.sys 移回临时存储驱动器
1. 右键单击“开始”菜单，并选择“系统”。
2. 在左侧菜单中，选择“高级系统设置”。
3. 在“性能”部分中，选择“设置”。
4. 选择“高级”选项卡。
5. 在“虚拟内存”部分中，选择“更改”。
6. 选择 OS 驱动器 **C**，并依次单击“无分页文件”、“设置”。
7. 选择临时存储驱动器 **T**，并依次单击“系统管理的大小”、“设置”。
8. 单击“应用”。 将收到警告，指出计算机需要重新启动才能使更改生效。
9. 重启虚拟机。

## <a name="next-steps"></a>后续步骤
* 可以通过[附加更多数据磁盘](attach-managed-disk-portal.md)来增加虚拟机的可用存储空间。

