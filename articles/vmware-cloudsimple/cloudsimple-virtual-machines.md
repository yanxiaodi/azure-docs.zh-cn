---
title: Azure VMware 解决方案 (按 CloudSimple)-虚拟机概述
description: 了解 CloudSimple 虚拟机及其优点。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 213ab51dae20d281a1a0e0f8ea18f4bde888e64d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877902"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple 虚拟机概述

CloudSimple 可让你从 Azure 门户管理 VMware 虚拟机 (Vm)。  你的 vSphere 群集中的群集或资源池由 Azure 通过映射到你的订阅进行管理。

若要从 Azure 创建 CloudSimple VM, 你的私有云 vCenter 上必须存在一个 VM 模板。  模板用于自定义操作系统和应用程序。  可以强制模板 VM 满足企业安全策略要求。  可以使用模板创建 Vm, 然后使用自助服务模型从 Azure 门户中使用。

## <a name="benefits"></a>优点

Azure 门户中的虚拟机 CloudSimple 为用户提供自助服务机制, 以创建和管理 VMware 虚拟机。

* 在私有云 vCenter 上创建 CloudSimple VM
* 管理 VM 属性
  * 添加/删除磁盘
  * 添加/删除 Nic
* CloudSimple VM 的电源操作
  * 开机和关机
  * 重置 VM
* 删除 VM

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 上使用 VMware vm](quickstart-create-vmware-virtual-machine.md)
* 了解如何[映射 Azure 订阅](azure-subscription-mapping.md)
