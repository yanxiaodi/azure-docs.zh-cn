---
title: Azure 示例基础结构演练 | Microsoft Docs
description: 了解用于在 Azure 中部署示例基础结构的关键设计和实施准则。
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71b0dd15d183f3209c7424c537dde1e3df29d097
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083139"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>适用于 Linux VM 的 Azure 示例基础结构演练
本文将逐步讲述如何构建示例应用程序基础结构。 我们详细介绍如何设计简单在线商店的基础结构，此在线商店可将关于命名约定、可用性集、虚拟网络及负载均衡器的所有准则和决策聚集在一起；以及如何实际部署你的虚拟机 (VM)。

## <a name="example-workload"></a>示例工作负荷
Adventure Works Cycles 想要在 Azure 中生成一个在线商店应用程序，该应用程序将包含：

* 两个在 Web 层中运行客户端前端的 nginx 服务器
* 两个在应用程序层中处理数据和订单的 nginx 服务器
* 包含在分片群集中的两个 MongoDB 服务器，用于在数据库层中存储产品数据和订单
* 位于身份验证层中、用于客户帐户和供应商的两个 Active Directory 域控制器
* 所有服务器皆位于两个子网中：
  * Web 服务器位于前端子网中 
  * 应用程序服务器、MongoDB 群集和域控制器位于后端子网中

![不同应用程序基础结构层的关系图](./media/infrastructure-example/example-tiers.png)

当客户浏览在线商店时，传入的安全 Web 流量必须在 Web 服务器之间进行负载均衡。 来自 Web 服务器的 HTTP 请求形式的订单处理流量必须在应用程序服务器之间进行负载均衡。 此外，基础结构必须设计为具有高可用性。

生成的设计必须引入：

* Azure 订阅和帐户
* 单个资源组
* Azure 托管磁盘
* 包含两个子网的虚拟网络
* 具有类似角色的 VM 的可用性集
* 虚拟机

以上各项都将遵循以下命名约定：

* Adventure Works Cycles 使用 **[IT 工作负荷]-[位置]-[Azure 资源]** 作为前缀
  * 在本示例中，IT 工作负荷名为 **azos** (Azure On-line Store)，位置为 **use**（美国东部 2）
* 虚拟网络使用 AZOS-USE-VN<strong>[数字]</strong>
* 可用性集使用 use azos-use-as- **[角色]**
* 虚拟机名称使用 azos-use-vm- **[VM 名称]**

## <a name="azure-subscriptions-and-accounts"></a>Azure 订阅和帐户
Adventure Works Cycles 使用名为 Adventure Works 企业订阅的企业订阅为此 IT 工作负荷提供计费。

## <a name="storage"></a>存储
Adventure Works Cycles 确定其应使用 Azure 托管磁盘。 创建 VM 时，会使用两种存储可用的存储层：

* **标准存储**用于 Web 服务器、应用程序服务器和域控制器及其数据磁盘。
* 用于 MongoDB 分片群集服务器及其数据磁盘的**高级存储**。

## <a name="virtual-network-and-subnets"></a>虚拟网络和子网
由于虚拟网络不需要持续连接到 Adventure Work Cycles 本地网络，因此，他们决定选择仅限云的虚拟网络。

他们通过 Azure 门户使用以下设置创建了仅限云的虚拟网络：

* 姓名：AZOS-USE-VN01
* 位置：美国东部 2
* 虚拟网络地址空间：10.0.0.0/8
* 第一个子网：
  * 姓名：前端
  * 地址空间：10.0.1.0/24
* 第二个子网：
  * 姓名：BackEnd
  * 地址空间：10.0.2.0/24

## <a name="availability-sets"></a>可用性集
为了维护其在线商店的所有四个层的高可用性，Adventure Works Cycles 决定使用四个可用性集：

* **azos-use-as-web** 用于 Web 服务器
* **azos-use-as-app** 用于应用程序服务器
* **azos-use-as-db** 用于 MongoDB 分片群集中的服务器
* **azos-use-as-dc** 用于域控制器

## <a name="virtual-machines"></a>虚拟机
Adventure Works Cycles 决定为其 Azure VM 使用以下名称：

* **azos-use-vm-web01** 用于第一个 Web 服务器
* **azos-use-vm-web02** 用于第二个 Web 服务器
* **azos-use-vm-app01** 用于第一个应用程序服务器
* **azos-use-vm-app02** 用于第二个应用程序服务器
* **azos-use-vm-db01** 用于群集中的第一个 MongoDB 服务器
* **azos-use-vm-db02** 用于群集中的第二个 MongoDB 服务器
* **azos-use-vm-dc01** 用于第一个域控制器
* **azos-use-vm-dc02** 用于第二个域控制器

这是生成的配置。

![在 Azure 中部署的最终应用程序基础结构](./media/infrastructure-example/example-config.png)

此配置引入以下项：

* 包含两个子网（FrontEnd 和 BackEnd）的仅限云虚拟网络
* 同时使用标准磁盘和高级磁盘的 Azure 托管磁盘
* 四个可用性集，每个在线商店层一个
* 四个层中的虚拟机
* 用于从 Internet 到 Web 服务器的基于 HTTPS 的 Web 流量的外部负载均衡集
* 用于从 Web 服务器到应用程序服务器的未加密 Web 流量的内部负载均衡集
* 单个资源组
