---
title: Azure 安全中心支持的平台 |Microsoft Docs
description: 本文档提供 Azure 安全中心支持的平台列表。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: 1d13db922ae84e4032304a8865ba6fcdafa65748
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201637"
---
# <a name="supported-platforms"></a>受支持的平台 

## 虚拟机/服务器<a name="vm-server"></a>

安全中心支持不同类型的混合环境上的虚拟机/服务器：

* 仅 Azure
* Azure 和本地
* Azure 和其他云
* Azure、其他云和本地

对于在 Azure 订阅上激活的 Azure 环境，Azure 安全中心会自动发现在该订阅中部署的 IaaS 资源。

> [!NOTE]
> 若要接收整套安全功能，您必须具有[Log Analytics 代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)，该代理由 Azure 安全中心使用，并已安装并[正确配置为将数据发送到 Azure 安全中心](security-center-enable-data-collection.md#manual-agent)。


以下部分列出了 Azure 安全中心使用的[Log Analytics 代理](../azure-monitor/platform/agents-overview.md#log-analytics-agent)可以运行的受支持的服务器操作系统。

### Windows server 操作系统<a name="os-windows"></a>

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> 与 Microsoft Defender ATP 集成仅支持 Windows Server 2012 R2 和 Windows Server 2016。

若要详细了解上面列出的 Windows 操作系统所支持的功能，请参阅[虚拟机/服务器支持的功能](security-center-services.md##vm-server-features)。

### Linux 操作系统<a name="os-linux"></a>

64 位

* CentOS 6 和 7
* Amazon Linux 2017.09
* Oracle Linux 6 和 7
* Red Hat Enterprise Linux Server 6 和 7
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS、16.04 LTS 和 18.04 LTS
* SUSE Linux Enterprise Server 12

32 位
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 和 9
* Ubuntu Linux 14.04 LTS 和 16.04 LTS

> [!NOTE]
> 由于受支持的 Linux 操作系统的列表不断变化, 因此, 如果需要, 请单击[此处](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems)查看受支持版本的最新列表, 以防自上次发布本主题以来发生了更改。

若要详细了解上面列出的适用于 Linux 操作系统的支持功能，请参阅[虚拟机/服务器支持的功能](security-center-services.md##vm-server-features)。

### 托管虚拟机服务<a name="virtual-machine"></a>

还会在客户订阅中创建虚拟机，作为某些 Azure 托管服务的一部分，例如 Azure Kubernetes （AKS）、Azure Databricks 等。 这些虚拟机也被 Azure 安全中心发现，可以根据上面列出的受支持的[Windows/Linux 操作系统](#os-windows)来安装和配置 Log analytics 代理。

### 云服务<a name="cloud-services"></a>

还支持在云服务中运行的虚拟机。 仅监视云服务 Web 和在生产槽运行的辅助角色。 若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。

## PaaS 服务<a name="paas-services"></a>

Azure 安全中心支持以下 Azure PaaS 资源：

* SQL
* PostGreSQL
* MySQL
* CosmosDB
* 存储帐户
* 应用服务
* Functions
* 云服务
* VNet
* Subnet
* NIC
* NSG
* Batch 帐户
* Service fabric 帐户
* 自动化帐户
* 负载均衡器
* 搜索
* 服务总线命名空间
* 流分析
* 事件中心命名空间
* 逻辑应用
* Redis
* Data Lake Analytics
* Data Lake Store
* 密钥保管库

若要详细了解适用于上述 PaaS 资源列表的支持的功能，请参阅[paas 服务支持的功能](security-center-services.md#paas-services)。

## <a name="next-steps"></a>后续步骤

- 了解[安全中心如何收集数据和 Log Analytics 代理](security-center-enable-data-collection.md)。
- 了解[安全中心如何管理和保护数据](security-center-data-security.md)。
- 了解如何[规划并理解设计注意事项，以便采用 Azure 安全中心](security-center-planning-and-operations-guide.md)。
- 了解[不同云环境的可用功能](security-center-services.md)。
- 详细了解[Azure 安全中心中的 vm & 服务器的威胁检测](security-center-alerts-iaas.md)。
- 查找[有关使用 Azure 安全中心的常见问题](security-center-faq.md)。
- 查找[关于 Azure 安全性和符合性的博客文章](https://blogs.msdn.com/b/azuresecurity/)。
