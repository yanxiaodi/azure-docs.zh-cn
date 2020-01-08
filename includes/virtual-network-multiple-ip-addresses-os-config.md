---
title: include 文件
description: include 文件
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 5aeb0e01192c0635def8eef0c73aa2d14b7921e2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172873"
---
## <a name="os-config"></a>将 IP 地址添加到 VM 操作系统

连接并登录到使用多个专用 IP 地址创建的 VM。 必须手动添加 VM 中的所有专用 IP 地址（包括主要地址）。 根据 VM 操作系统完成后面的步骤。

### <a name="windows"></a>Windows

1. 在命令提示符下，键入 *ipconfig /all*。  只能看到*主要*专用 IP 地址（通过 DHCP）。
2. 在命令提示符下键入 *ncpa.cpl*，打开“网络连接”窗口。 
3. 打开相应适配器的属性：**本地区域连接**。
4. 双击“Internet 协议版本 4 (IPv4)”。
5. 单击“使用下面的 IP 地址”并输入以下值： 

    * **IP 地址**：输入 *Primary* 专用 IP 地址
    * **子网掩码**：根据子网设置此值。 例如，如果子网为 /24 子网，则子网掩码为 255.255.255.0。
    * **默认网关**：子网中的第一个 IP 地址。 如果子网为 10.0.0.0/24，则网关 IP 地址为 10.0.0.1。
    * 选择“使用下面的 DNS 服务器地址”  并输入以下值：
        * **首选 DNS 服务器**：如果不使用自己的 DNS 服务器，请输入 168.63.129.16。  如果使用自己的 DNS 服务器，请输入服务器的 IP 地址。
    * 选择“高级”按钮，并添加其他 IP 地址。  将在前面的步骤中添加到 Azure 网络接口的每个辅助专用 IP 地址添加到分配有分配给 Azure 网络接口的主 IP 地址的 Windows 网络接口。

        切勿在虚拟机的操作系统中手动分配已分配给 Azure 虚拟机的公共 IP 地址。 在操作系统中手动设置该 IP 地址时，请确保它与分配给 Azure [网络接口](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings)的专用 IP 地址是同一地址，否则可能会丢失与虚拟机的连接。 详细了解[专用 IP 地址](../articles/virtual-network/virtual-network-network-interface-addresses.md#private)设置。 绝不要在操作系统中分配 Azure 公用 IP 地址。

    * 单击“确定”关闭“TCP/IP 设置”，并再次单击“确定”关闭适配器设置。   将重新建立 RDP 连接。

6. 在命令提示符下，键入 *ipconfig /all*。 此时会显示添加的所有 IP 地址，DHCP 已关闭。
7. 将 Windows 配置为使用 Azure 中主 IP 配置的专用 IP 地址作为 Windows 的主 IP 地址。 有关详细信息，请参阅[无法通过具有多个 IP 地址的 Azure Windows VM 访问 Internet](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse)。 

### <a name="validation-windows"></a>验证 (Windows)

要确保能够从辅助 IP 配置通过与之关联的公共 IP 连接到 Internet，请在通过上述步骤将其正确添加以后，使用以下命令：

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>对于辅助 IP 配置，仅当该配置存在关联的 IP 地址的情况下，才能 ping Internet。 对于主 IP 配置，不需公共 IP 地址也可 ping Internet。

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)
我们建议查看最新 Linux 分发版的文档。 

1. 打开终端窗口。
2. 请确保以 root 用户身份操作。 否则，请输入以下命令：

    ```bash
    sudo -i
    ```

3. 更新网络接口（假设为“eth0”）的配置文件。

   * 保留 dhcp 的现有行项。 主 IP 地址会保留之前的配置。
   * 使用以下命令添加其他静态 IP 地址的配置：

       ```bash
       cd /etc/network/interfaces.d/
       ls
       ```

     应会看到一个 .cfg 文件。
4. 打开 文件。 该文件的末尾应会显示以下命令行：

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. 在此文件包含的命令行后面添加以下命令行：

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    netmask <your subnet mask>
    ```

6. 使用以下命令保存该文件：

    ```bash
    :wq
    ```

7. 使用以下命令重置网络接口：

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > 如果使用远程连接，请在同一行中同时运行 ifdown 和 ifup。
    >

8. 使用以下命令验证 IP 地址是否已添加到网络接口：

    ```bash
    ip addr list eth0
    ```

    应会在列表中看到添加的 IP 地址。

### <a name="linux-red-hat-centos-and-others"></a>Linux（Red Hat、CentOS 和其他操作系统）

1. 打开终端窗口。
2. 请确保以 root 用户身份操作。 否则，请输入以下命令：

    ```bash
    sudo -i
    ```

3. 输入密码，根据提示的说明操作。 切换为 root 用户后，使用以下命令导航到网络脚本文件夹：

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. 使用以下命令列出相关的 ifcfg 文件：

    ```bash
    ls ifcfg-*
    ```

    应会看到其中一个文件是 *ifcfg-eth0*。

5. 若要添加 IP 地址，请为其创建配置文件，如下所示。 请注意，必须为每个 IP 配置创建一个文件。

    ```bash
    touch ifcfg-eth0:0
    ```

6. 使用以下命令打开 *ifcfg-eth0:0* 文件：

    ```bash
    vi ifcfg-eth0:0
    ```

7. 在此示例中，请使用以下命令向文件 *eth0:0* 添加内容。 请务必更新基于 IP 地址的信息。

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. 使用以下命令保存该文件：

    ```bash
    :wq
    ```

9. 运行以下命令重新启动网络服务，确保更改成功：

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    应会在返回的列表中看到添加的 IP 地址 *eth0:0*。

### <a name="validation-linux"></a>验证 (Linux)

要确保能够从辅助 IP 配置通过与之关联的公共 IP 连接到 Internet，请使用以下命令：

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>对于辅助 IP 配置，仅当该配置具有与之关联的公共 IP 地址的情况下，才能 ping 到 Internet。 对于主 IP 配置，不需公共 IP 地址也可 ping 到 Internet。

对于 Linux VM，在尝试验证来自辅助 NIC 的出站连接时，可能需要添加适当的路由。 可通过多种方式来执行此操作。 请参阅针对 Linux 分发的相应文档。 下面是实现此目的的一种方法：

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- 确保执行以下替换：
    - **10.0.0.5** 替换为专用 IP 地址，该地址有一个与之关联的公共 IP 地址。
    - **10.0.0.1** 替换为默认网关
    - **eth2** 替换为辅助 NIC 的名称
