---
title: 在运行 Linux 的虚拟机上配置软件 RAID | Microsoft Docs
description: 了解如何使用 mdadm 在 Azure 中的 Linux 上配置 RAID。
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: gwallace
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: d0658af090d9a3f39bee69f5103a78a329fe189c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083795"
---
# <a name="configure-software-raid-on-linux"></a>在 Linux 上配置软件 RAID
在 Azure 中的 Linux 虚拟机上使用软件 RAID 将多个附加的数据磁盘呈现为一个单一的 RAID 设备，是一种常见的情形。 通常，使用这种方法可以改进性能，而且与只使用单独一块磁盘相比，吞吐量也会有所改进。

## <a name="attaching-data-disks"></a>附加数据磁盘
配置 RAID 设备需要两个或更多的空数据磁盘。  创建 RAID 设备的主要原因是提高磁盘 IO 的性能。  根据 IO 需求，可以选择附加存储在标准存储且一个磁盘最多具有 500 IO/ps 的磁盘，或高级存储且一个磁盘最多具有 5000 IO/ps 的磁盘。 本文不详细介绍如何为 Linux 虚拟机预配和附加数据磁盘。  请参阅 Microsoft Azure 文章[附加磁盘](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，详细了解如何在 Azure 上为 Linux 虚拟机附加空数据磁盘。

## <a name="install-the-mdadm-utility"></a>安装 mdadm 实用程序
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS 和 Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES 和 openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>创建磁盘分区
在此示例中，我们在 /dev/sdc 上创建单个磁盘分区。 该新磁盘分区将命名为 /dev/sdc1。

1. 启动 `fdisk`，以开始创建分区

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. 在提示符下按 n 键，创建**新**分区：

    ```bash
    Command (m for help): n
    ```

1. 接下来，按 p 键，创建**主**分区：

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. 按 1 键，以选择分区号 1：

    ```bash
    Partition number (1-4): 1
    ```

1. 选择新分区的起始点，或者按 `<enter>` 键接受默认值，将该分区置于驱动器可用空间的开始处：

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. 选择分区大小，例如，键入“+10G”以创建一个 10 GB 的分区。 或者，按 `<enter>` 键创建一个跨整个驱动器的单一分区：

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. 接下来，将该分区的 ID 和**类型**从默认的 ID“83”(Linux) 更改为 ID“fd”(Linux raid auto)：

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. 最后，将分区表写入驱动器并退出 fdisk：

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>创建 RAID 阵列
1. 以下示例将给位于三个不同数据磁盘（sdc1、sdd1、sde1）上的三个分区设置带区（RAID 级别 0）。  运行此命令之后，会创建一个名为 **/dev/md127** 的新 RAID 设备。 另请注意，如果这些数据磁盘以前属于另一失效的 RAID 阵列，则可能有必要将 `--force` 参数添加到 `mdadm` 命令：

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. 在新 RAID 设备上创建文件系统
   
    **CentOS、Oracle Linux、SLES 12、openSUSE 和 Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** - 启用 boot.md 并创建 mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > 在 SUSE 系统中完成这些更改之后，可能需要重新引导。 在 SLES 12 中，此步骤*不*是必需的。
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>将新文件系统添加到 /etc/fstab
> [!IMPORTANT]
> 错误地编辑 /etc/fstab 文件可能会导致系统无法引导。 如果没有把握，请参考分发的文档来获取有关如何正确编辑该文件的信息。 另外，建议在编辑之前创建 /etc/fstab 文件的备份。

1. 为新文件系统创建所需的安装点，例如：

    ```bash
    sudo mkdir /data
    ```
1. 在编辑 /etc/fstab 时，**UUID** 应该用于引用文件系统而不是设备名称。  使用 `blkid` 实用程序来确定新文件系统的 UUID：

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. 在文本编辑器中打开 /etc/fstab，并为新文件系统添加条目，例如：

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    或在 **SLES 11** 上：

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    然后，保存并关闭 /etc/fstab。

1. 测试该 /etc/fstab 条目是否正确：

    ```bash  
    sudo mount -a
    ```

    如果此命令导致错误消息，请检查 /etc/fstab 文件中的语法。
   
    接下来，运行 `mount` 命令以确保文件系统已装入：

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. （可选）故障安全引导参数
   
    **fstab 配置**
   
    许多分发版包括 `nobootwait` 或 `nofail` 装载参数，这些参数可以添加到 /etc/fstab 文件中。 这些参数允许装入某特定文件系统时失败，并且允许 Linux 系统继续引导，即使它无法正确装入 RAID 文件系统也无妨。 请参阅分发版文档，了解有关这些参数的详细信息。
   
    示例 (Ubuntu)：

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux 引导参数**
   
    除了以上参数，还可以使用内核参数“`bootdegraded=true`”来启用系统引导功能，即使发现 RAID 已损坏或降级（例如，由于无意中从虚拟机中移除了数据驱动器而发现这种情况）也无妨。 默认情况下，这样也可能会导致系统无法引导。
   
    请参阅发行版文档，了解如何正确编辑内核参数。 例如，在许多分发（CentOS、Oracle Linux、SLES 11）中，可以手动将这些参数添加到“`/boot/grub/menu.lst`”文件。  在 Ubuntu 上，此参数可添加到“/etc/default/grub”上的 `GRUB_CMDLINE_LINUX_DEFAULT` 变量中。


## <a name="trimunmap-support"></a>TRIM/UNMAP 支持
某些 Linux 内核支持 TRIM/UNMAP 操作以放弃磁盘上未使用的块。 这些操作主要适用于标准存储，以通知 Azure 已删除的页不再有效可以丢弃。 如果创建了较大的文件，然后将其删除，则放弃页可以节省成本。

> [!NOTE]
> 如果将阵列的区块大小设置为小于默认值 (512 KB)，则 RAID 可能不会发出丢弃命令。 这是因为“主机”上的 unmap 粒度也是 512KB。 如果通过 mdadm 的 `--chunk=` 参数修改数组的块区大小，则 TRIM/unmap 请求可能被内核忽略。

在 Linux VM 中有两种方法可以启用 TRIM 支持。 与往常一样，有关建议的方法，请参阅分发：

- 在 `/etc/fstab` 中使用 `discard` 装载选项，例如：

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- 在某些情况下，`discard` 选项可能会影响性能。 此处，还可以从命令行手动运行 `fstrim` 命令，或将其添加到 crontab 以定期运行：

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
