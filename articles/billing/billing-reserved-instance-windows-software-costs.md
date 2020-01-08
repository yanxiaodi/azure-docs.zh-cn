---
title: Azure 的预留软件成本 | Microsoft Docs
description: 了解 Azure 虚拟机预留实例成本中不包含哪些软件计量。
services: billing
documentationcenter: ''
author: yashar
manager: yashar
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e74765ec09fb9cd2d219ceb3d784b4030c83e028
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719641"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Azure 虚拟机预留实例未包含的软件成本

虚拟机预留实例和 SQL 保留容量折扣仅适用于基础结构成本，而不适用于软件成本。 如果你使用 Windows VM，并且对虚拟机预留实例没有 Azure 混合权益，则会针对以下部分中列出的软件计量向你收取费用。 对于 SQL PaaS 部署，如果未选择 Azure 混合权益，则将继续使用单独的计量器收取 IP 成本。

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>预留成本中未包括的 Windows 软件计量

| 计量 ID | 使用情况文件中的计量名称 | VM 使用的系列 |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | 预留-Windows Server 突增（1 核心） | B 系列 |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | 预留-Windows Server 突增（2 核心） | B 系列 |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | 预留-Windows Server 突增（4 核心） | B 系列 |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | 预留-Windows Server 突增（8 核心） | B 系列 |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | 预留-Windows Server（1 核心） | B 系列以外的所有系列 |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | 预留-Windows Server（2 核心） | B 系列以外的所有系列 |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | 预留-Windows Server（4 核心） | B 系列以外的所有系列 |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | 预留-Windows Server（6 核心） | B 系列以外的所有系列 |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | 预留-Windows Server（8 核心） | B 系列以外的所有系列 |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | 预留-Windows Server（12 核心） | B 系列以外的所有系列 |
| 02968a6b-1654-4495-ada6-13f378ba7172 | 预留-Windows Server（16 核心） | B 系列以外的所有系列 |
| 175434d8-75f9-474b-9906-5d151b6bed84 | 预留-Windows Server（20 核心） | B 系列以外的所有系列 |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | 预留-Windows Server（24 核心） | B 系列以外的所有系列 |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | 预留-Windows Server（32 核心） | B 系列以外的所有系列 |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | 预留-Windows Server（40 核心） | B 系列以外的所有系列 |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | 预留-Windows Server（64 核心） | B 系列以外的所有系列 |
| da612742-e7cc-4ca3-9334-0fb7234059cd | 预留-Windows Server（72 核心） | B 系列以外的所有系列 |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | 预留-Windows Server（128 核心） | B 系列以外的所有系列 |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | 预留-Windows Server（256 核心） | B 系列以外的所有系列 |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | 预留-Windows Server（96 核心） | B 系列以外的所有系列 |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>预留成本中未包括的云服务软件计量

| 计量 ID | 使用情况文件中的计量名称 |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|云服务 1 vCPU 许可证|
|e0434559-19ee-4132-9c46-05ad4044f3f7|云服务 2 vCPU 许可证|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|云服务 4 vCPU 许可证|
|13103090-ca72-4825-ab12-7f16c4931d95|云服务 8 vCPU 许可证|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|云服务 16 vCPU 许可证|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|云服务 20 vCPU 许可证|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|云服务 32 vCPU 许可证|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|云服务 64 vCPU 许可证|
|7a803026-244c-4659-834c-11e6b2d6b76f|云服务 80 vCPU 许可证|

## <a name="rates-for-azure-meters"></a>Azure 计量的费率

可以通过 Azure RateCard API 来获取上述每个计量的成本。 有关如何获取 azure 计量的费率的信息，请参阅[获取 Azure 订阅中使用的资源的价格和元数据信息](/previous-versions/azure/reference/mt219004(v=azure.100))。

## <a name="next-steps"></a>后续步骤
若要详细了解 Azure 预留，请参阅以下文章：

- [什么是 Azure 预留？](billing-save-compute-costs-reservations.md)
- [通过 Azure 虚拟机预留实例为虚拟机预付费](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [管理 Azure 预留](billing-manage-reserved-vm-instance.md)
- [了解预留折扣的应用方式](billing-understand-vm-reservation-charges.md)
- [了解即用即付订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
- [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
