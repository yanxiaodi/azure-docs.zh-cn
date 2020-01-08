---
title: Azure VMware 解决方案中通过 CloudSimple 的 Vlan 和子网
description: 了解 CloudSimple 私有云中的 Vlan 和子网
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7af191893d6b3cf1c38e5ff44a7a8a04509347a8
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543796"
---
# <a name="vlans-and-subnets-overview"></a>Vlan 和子网概述

CloudSimple 提供了每个区域的网络, 其中部署了 CloudSimple 服务。  网络是单个 TCP 第3层地址空间, 默认情况下启用路由。  在此区域中创建的所有私有云和子网都可以相互通信, 而无需任何其他配置。  可以使用 Vlan 在 vCenter 上创建分布式端口组。

![CloudSimple 网络拓扑](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLAN

为每个私有云创建 VLAN (第2层网络)。  第2层流量保持在私有云的边界内, 使你能够隔离私有云中的本地流量。  在私有云上创建的 VLAN 只能用于在该私有云中创建分布式端口组。  在私有云上创建的 VLAN 自动在连接到私有云的主机的所有交换机上进行配置。

## <a name="subnets"></a>子网

创建 VLAN 时, 可以通过定义子网的地址空间来创建子网。 地址空间中的 IP 地址被分配为子网网关。 单个专用第3层地址空间按客户和区域分配。 你可以在网络区域中配置任何与本地网络或 Azure 虚拟网络的 RFC 1918 非重叠地址空间。

默认情况下, 所有子网都可以相互通信, 从而减少了私有云之间的路由配置开销。 同一区域中的 Pc 上的西部数据将保留在同一第3层网络中, 并在区域内通过本地网络基础结构传输。 区域内私有云之间的通信不需要传出。 此方法消除了在不同的私有云中部署不同工作负荷时的任何 WAN/出口性能损失。

## <a name="vspherevsan-subnets-cidr-range"></a>vSphere/vSAN 子网 CIDR 范围

私有云是作为由 vCenter 服务器管理的隔离 VMware 堆栈 (ESXi 主机、vCenter、vSAN 和 NSX) 环境创建的。  管理组件部署在为 vSphere/vSAN 子网 CIDR 选择的网络中。  在部署过程中, 网络 CIDR 范围分为不同的子网。

* 最小 vSphere/vSAN 子网 CIDR 范围前缀: **/24**
* 最大 vSphere/vSAN 子网 CIDR 范围前缀: **/21**

> [!IMPORTANT]
> VSphere/vSAN CIDR 范围中的 IP 地址保留供私有云基础结构使用。  请勿在任何虚拟机上使用此范围内的 IP 地址。

### <a name="vspherevsan-subnets-cidr-range-limits"></a>vSphere/vSAN 子网 CIDR 范围限制

选择 vSphere/vSAN 子网 CIDR 范围大小会影响私有云的大小。  下表显示了根据 vSphere/vSAN 子网 CIDR 的大小, 可以拥有的最大节点数。

| 指定的 vSphere/vSAN 子网 CIDR 前缀长度 | 节点数上限 |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>在私有云上创建的管理子网

在创建私有云时, 将创建以下管理子网。

* **系统管理**。 VLAN 和子网, 适用于 ESXi 主机的管理网络、DNS 服务器、vCenter 服务器。
* **VMotion**。 VLAN 和子网, 用于 ESXi 主机的 vMotion 网络。
* **VSAN**。 VLAN 和 ESXi 主机的 vSAN 网络的子网。
* **NsxtEdgeUplink1**。 Vlan 和子网, 用于向外部网络提供 VLAN 上行链路。
* **NsxtEdgeUplink2**。 Vlan 和子网, 用于向外部网络提供 VLAN 上行链路。
* **NsxtEdgeTransport**。 传输区域的 VLAN 和子网控制 NSX 中第2层网络的范围。
* **NsxtHostTransport**。 用于主机传输区域的 VLAN 和子网。

### <a name="management-network-cidr-range-breakdown"></a>管理网络 CIDR 范围细目

指定的 vSphere/vSAN 子网 CIDR 范围分为多个子网。  下表显示了允许的前缀细目的示例。  该示例使用192.168.0.0 作为 CIDR 范围。

例如：

| 指定的 vSphere/vSAN 子网 CIDR/prefix | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| 系统管理 | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| vMotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| NSX-T 主机传输 | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| NSX-T 边缘传输 | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NSX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| NSX-T Edge uplink2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>后续步骤

* [创建和管理 Vlan 和子网](create-vlan-subnet.md)
