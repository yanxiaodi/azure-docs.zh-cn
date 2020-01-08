---
title: 从 Azure 下载 Linux VHD | Microsoft Docs
description: 使用 Azure CLI 和 Azure 门户下载 Linux VHD。
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
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: ed79df03a42c1558b975cd1c21c79716d50d4616
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083485"
---
# <a name="download-a-linux-vhd-from-azure"></a>从 Azure 下载 Linux VHD

本文介绍如何使用 Azure CLI 和 Azure 门户从 Azure 下载 Linux 虚拟硬盘 (VHD) 文件。 

如果尚未安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2)，请安装。

## <a name="stop-the-vm"></a>停止 VM

如果 VHD 附加到正在运行的 VM，则不能从 Azure 下载。 需要停止 VM，才能下载 VHD。 如果要使用 VHD 作为[映像](tutorial-custom-images.md)创建包含新磁盘的其他 VM，则需要取消设置并通用化文件中包含的操作系统，然后停止 VM。 若要使用 VHD 作为现有 VM 的新实例的磁盘或数据磁盘，只需停止并解除分配 VM。

若要使用 VHD 作为映像创建其他 VM，请完成以下步骤：

1. 使用 SSH、帐户名称和 VM 的公共 IP 地址连接到它并对其取消设置。 可以使用 [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show) 查找公共 IP 地址。 +user 参数还会删除上次预配的用户帐户。 如果正在将帐户凭据收录到 VM，请省略此 +user 参数。 以下示例删除上次预配的用户帐户：

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. 使用 [az login](https://docs.microsoft.com/cli/azure/reference-index) 登录到 Azure 帐户。
3. 停止并解除分配 VM。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. 通用化 VM。 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

若要使用 VHD 作为现有 VM 的新实例的磁盘或数据磁盘，请完成以下步骤：

1.  登录到 [Azure 门户](https://portal.azure.com/)。
2.  在左侧菜单中，选择“虚拟机”。
3.  从列表中选择 VM。
4.  在 VM 的页面上, 选择 "**停止**"。

    ![停止 VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>生成 SAS URL

若要下载 VHD 文件，需要生成[共享访问签名 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL。 生成 URL 时，将为 URL 分配到期时间。

1.  在 VM 页面的菜单上, 选择 "**磁盘**"。
2.  为 VM 选择操作系统磁盘, 并选择 "**磁盘导出**"。
3.  选择 "**生成 URL**"。

    ![生成 URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>下载 VHD

1.  在生成的 URL 下, 选择 **"下载 VHD 文件**"。
**
    ![下载 VHD](./media/download-vhd/export-download.png)

2.  你可能需要在浏览器中选择 "**保存**" 以开始下载。 VHD 文件的默认名称为 *abcd*。

    ![在浏览器中选择 "保存"](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure CLI 从自定义磁盘上传并创建 Linux VM](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 
- [使用 Azure CLI 管理 Azure 磁盘](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

