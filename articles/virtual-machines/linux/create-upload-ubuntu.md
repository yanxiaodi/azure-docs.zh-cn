---
title: 在 Azure 中创建和上传 Ubuntu Linux VHD
description: 了解如何创建和上传包含 Ubuntu Linux 操作系统的 Azure 虚拟硬盘 (VHD)。
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: szark
ms.openlocfilehash: cdf2c6c0d5621223655fc4571affcdde4563ac97
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258266"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>为 Azure 准备 Ubuntu 虚拟机
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>正式 Ubuntu 云映像
Ubuntu 现已发布正式 Azure VHD，可从 [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) 下载。 如果需要为 Azure 构建自己专用的 Ubuntu 映像，而不是使用以下手动过程，则我们建议先使用这些已知良好的 VHD，并根据需要进行自定义。 始终可以在以下位置找到最新的映像版本：

* Ubuntu 12.04/Precise：[ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty：[ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial： [ubuntu-16.04-cloudimg-disk1-](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic：[bionic-server-cloudimg-amd64.vhd.zip](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vhd.zip)
* Ubuntu 18.10/Cosmic：[cosmic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>先决条件
本文假设已在虚拟硬盘中安装 Ubuntu Linux 操作系统。 可使用多种工具创建 .vhd 文件，如 Hyper-V 等虚拟化解决方案。 有关说明，请参阅 [安装 Hyper-V 角色和配置虚拟机](https://technet.microsoft.com/library/hh846766.aspx)。

**Ubuntu 安装说明**

* 另请参阅 [常规 Linux 安装说明](create-upload-generic.md#general-linux-installation-notes) ，以获取更多有关如何为 Azure 准备 Linux 的提示。
* Azure 不支持 VHDX 格式，仅支持 **固定大小的 VHD**。  可使用 Hyper-V 管理器或 convert-vhd cmdlet 将磁盘转换为 VHD 格式。
* 在安装 Linux 系统时，建议使用标准分区而不是 LVM（通常是许多安装的默认值）。 这会避免 LVM 与克隆 VM 发生名称冲突，特别是在 OS 磁盘需要连接到另一台 VM 以进行故障排除的情况下。 如果需要，可以在数据磁盘上使用 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 不要在操作系统磁盘上配置交换分区。 可以配置 Linux 代理，以在临时资源磁盘上创建交换文件。  可以在下面的步骤中找到有关此内容的详细信息。
* Azure 上的所有 VHD 必须已将虚拟大小调整为 1MB。 从原始磁盘转换为 VHD 时，必须确保在转换前原始磁盘大小是 1MB 的倍数。 有关详细信息，请参阅 [Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)。

## <a name="manual-steps"></a>手动步骤
> [!NOTE]
> 尝试为 Azure 创建自己的自定义 Ubuntu 映像之前，请考虑改用 [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) 中的预建且经过测试的映像。
> 
> 

1. 在 Hyper-V 管理器的中间窗格中，选择虚拟机。

2. 单击“连接” 打开虚拟机窗口。

3. 替换映像中的当前存储库，以使用 Ubuntu 的 Azure 存储库。 这些步骤可能会由于 Ubuntu 版本的不同而稍有差异。
   
    编辑 `/etc/apt/sources.list` 之前，建议进行备份：
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04：
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04：
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04：
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Ubuntu Azure 映像现在遵循*硬件支持* (HWE) 内核要求。 通过运行以下命令将操作系统更新为最新内核：

    Ubuntu 12.04：
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04：
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04：
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    **另请参阅：**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. 修改 Grub 的内核引导行以使其包含 Azure 的其他内核参数。 为此，请在文本编辑器中打开 `/etc/default/grub`，找到名为 `GRUB_CMDLINE_LINUX_DEFAULT` 的变量（或根据需要添加它）并对它进行编辑使其包含以下参数：
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    保存并关闭此文件，运行 `sudo update-grub`。 这还将确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 技术支持人员调试问题。

6. 请确保已安装 SSH 服务器且已将其配置为在引导时启动。  这通常是默认设置。

7. 安装 Azure Linux 代理：
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  安装 `walinuxagent` 包时会删除 `NetworkManager` 和 `NetworkManager-gnome` 包（如果已安装它们）。


1. 运行以下命令可取消对虚拟机的设置并且对其进行准备以便在 Azure 上进行设置：
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. 在 Hyper-V 管理器中单击“操作”->“关闭”。 Linux VHD 现已准备好上传到 Azure。

## <a name="references"></a>参考资料
[Ubuntu 硬件支持 (HWE) 内核](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>后续步骤
现在，可以使用 Ubuntu Linux 虚拟硬盘在 Azure 中创建新的 Azure 虚拟机了。 如果是首次将 .vhd 文件上传到 Azure，请参阅[从自定义磁盘创建 Linux VM](upload-vhd.md#option-1-upload-a-vhd)。

