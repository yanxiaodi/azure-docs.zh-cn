---
title: 从 Azure 下载 Windows VHD | Microsoft Docs
description: 使用 Azure 门户下载 Windows VHD。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: c1c09382102045dd248b6771d8d0ea1ef090b6eb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079624"
---
# <a name="download-a-windows-vhd-from-azure"></a>从 Azure 下载 Windows VHD

本文介绍如何使用 Azure 门户从 Azure 下载 Windows 虚拟硬盘 (VHD) 文件。

## <a name="stop-the-vm"></a>停止 VM

如果 VHD 附加到正在运行的 VM，则不能从 Azure 下载。 需要停止 VM，才能下载 VHD。 如果要使用 VHD 作为[映像](tutorial-custom-images.md)创建包含新磁盘的其他 VM，则需要使用 [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) 通用化文件中包含的操作系统，然后停止 VM。 若要使用 VHD 作为现有 VM 的新实例的磁盘或数据磁盘，只需停止并解除分配 VM。

若要使用 VHD 作为映像创建其他 VM，请完成以下步骤：

1.  登录到 [Azure 门户](https://portal.azure.com/)（如果未登录）。
2.  [连接到 VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
3.  在 VM 上，以管理员身份打开“命令提示符”窗口。
4.  将目录切换到 *%windir%\system32\sysprep*，然后运行 sysprep.exe。
5.  在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，确保已选中“通用化”。
6.  在“关闭选项”中选择“关闭”，然后单击“确定”。 

若要使用 VHD 作为现有 VM 的新实例的磁盘或数据磁盘，请完成以下步骤：

1.  在 Azure 门户的“中心”菜单上，单击“虚拟机”。
2.  从列表中选择 VM。
3.  在 VM 的边栏选项卡上，单击“停止”。

    ![停止 VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>生成 SAS URL

若要下载 VHD 文件，需要生成[共享访问签名 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL。 生成 URL 时，将为 URL 分配到期时间。

1.  在 VM 的边栏选项卡的菜单上，单击“磁盘”。
2.  为 VM 选择操作系统磁盘, 并单击 "**磁盘导出**"。
3.  将 URL 的到期时间设置为 *36000*。
4.  单击“生成 URL”。

    ![生成 URL](./media/download-vhd/export-generate-new.png)

> [!NOTE]
> 从默认值增加到期时间，以便提供足够时间来下载 Windows Server 操作系统的大型 VHD 文件。 可能需要包含 Windows Server 操作系统的 VHD 文件，以便根据连接花几个小时下载。 如果下载的是数据磁盘的 VHD，则默认时间就足够了。 
> 
> 

## <a name="download-vhd"></a>下载 VHD

1.  在生成的 URL 下，单击“下载 VHD 文件”。

    ![下载 VHD](./media/download-vhd/export-download.png)

2.  可能需要单击浏览器中的“保存”以开始下载。 VHD 文件的默认名称为 *abcd*。

    ![单击浏览器中的“保存”](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[将 VHD 文件上传到 Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
- [从存储帐户中的非托管磁盘创建托管磁盘](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- [使用 PowerShell 管理 Azure 磁盘](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

