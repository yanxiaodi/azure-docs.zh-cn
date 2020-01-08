---
title: Azure 应用程序“托管应用程序”产品/服务发布指南
description: 本文介绍在市场中发布托管应用程序的要求
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: evansma
ms.service: marketplace
ms.topic: article
ms.date: 06/14/2018
ms.author: v-qiwe
ms.openlocfilehash: 29546b0969751a43959a55860fc22e9f3c3e225b
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154952"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Azure 应用程序：“托管应用程序”产品/服务发布指南

托管应用程序是在市场中发布解决方案的主要方式之一。 使用本指南了解此产品/服务的要求。 

以下是通过市场部署和计费的事务产品/服务。 用户看到的号召性用语是“立即获取”。

需要满足以下条件时，请使用“Azure 应用：托管应用”产品/服务类型：
- 使用 VM 为客户部署基于订阅的解决方案，或部署基于 IaaS 的整个解决方案。
- 你或客户要求解决方案由合作伙伴管理。

>[!NOTE]
>例如，合作伙伴可能是 SI 或托管服务提供商 (MSP)。  

## <a name="managed-application-offer"></a>“托管应用程序”产品/服务

|要求 |详细信息  |
|---------|---------|
|部署到客户的 Azure 订阅 | 托管的应用必须部署在客户的订阅，并可以由第三方。 | 
|计费和计量    |  资源将在客户的 Azure 订阅中预配。 即用即付 (PAYGO) 虚拟机将通过 Microsoft 与客户进行交易，通过客户的 Azure 订阅 (PAYGO) 进行计费。 <br> 对于自带的自己的许可证，而 Microsoft 将向在客户订阅中，所产生的基础结构成本将 transact 您直接许可给客户的费用的软件。        |
|与 Azure 兼容的虚拟硬盘 (VHD)    |   必须基于 Windows 或 Linux 构建 VM。<ul> <ul> <li>有关创建 Linux VHD 的详细信息，请参阅 [Azure 认可的 Linux 发行版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。</li> <li>有关创建 Windows VHD 的详细信息，请参阅[创建与 Azure 兼容的 VHD](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)。</li> </ul> |

>[!NOTE]
> 托管应用必须可通过市场部署。 如果客户沟通是个问题，应该在启用商机分享后与感兴趣的客户交流。  

>[!Note]
>云解决方案提供商 (CSP) 合作伙伴通道参加现已推出。  请参阅[云解决方案提供商](./cloud-solution-providers.md)营销通过 Microsoft CSP 产品/服务的详细信息的合作伙伴渠道。

## <a name="next-steps"></a>后续步骤
如果尚未注册， 

- 请在市场中[注册](https://azuremarketplace.microsoft.com/sell)。

如果已注册并正在创建新套餐或正在使用现有套餐，

- [登录到云合作伙伴门户](https://cloudpartner.azure.com)若要创建或完成产品/服务。
