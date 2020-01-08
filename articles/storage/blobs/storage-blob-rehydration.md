---
title: 从存档层解除冻结 blob 数据
description: 将 blob 从存档存储中解除冻结, 以便可以访问数据。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/07/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2e7d56a1461dfd89a7309288aadb0ba245d0f885
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958208"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>从存档层解除冻结 blob 数据

当 blob 位于 "存档" 访问层中时, 它被视为 "脱机", 因此无法读取或修改。 Blob 元数据仍处于联机状态并且可用, 允许列出 blob 及其属性。 读取和修改 blob 数据仅适用于联机层, 如 "热" 或 "冷"。 有两个选项可用于检索和访问存储在存档访问层中的数据。

1. 使用 "[设置 Blob 层](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)" 操作将[存档的 blob 解除冻结到联机层](#rehydrate-an-archived-blob-to-an-online-tier), 将存档的 blob 解除冻结为热或冷。
2. [将已存档的 Blob 复制到联机层](#copy-an-archived-blob-to-an-online-tier)-通过使用 "[复制 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) " 操作创建存档的 blob 的新副本。 指定不同的 blob 名称和 "热" 层或 "冷" 层的目标层。

 有关层的详细信息, 请参阅[Azure Blob 存储: "热"、"冷" 和 "存档" 访问层](storage-blob-storage-tiers.md)。

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>将存档的 blob 解除冻结到联机层

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>将已存档的 blob 复制到联机层

如果不想解除冻结 blob, 可以选择 "[复制 blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob) " 操作。 当你在 "热" 层或 "冷" 层中处理新 blob 时, 原始 blob 将在 "存档" 中保持不变。 使用复制过程时, 可以将可选的*x-解除冻结*属性设置为标准或高 (预览)。

只能将存档 blob 复制到联机目标层。 不支持将存档 blob 复制到另一个存档 blob。

从存档中复制 blob 会耗费时间。 在后台, "**复制 Blob** " 操作临时解除冻结存档源 Blob, 以便在目标层中创建新的联机 Blob。 在临时解除冻结存档完成并且数据写入到新 blob 之前, 此新 blob 不可用。

## <a name="pricing-and-billing"></a>定价和计费

从存档到热层或冷层的解除冻结 blob 按读取操作和数据检索收费。 与标准优先级相比, 使用高优先级 (预览版) 的操作和数据检索成本更高。 高优先级解除冻结在帐单上显示为单独的行项。 如果高优先级请求返回的存档 blob 数超过5小时, 则不会向你收取高优先级检索速率。 不过, 标准检索速率仍适用。

从存档中将 blob 复制到热层或冷层会按读取操作和数据检索收费。 创建新副本需要支付写入操作的费用。 当你复制到联机 blob 时, 较早的删除费用不适用于, 因为源 blob 在存档层中保持不变。 高优先级费用确实适用。

存档层中的 blob 应存储至少180天。 在180天前删除或解除冻结存档的 blob 将导致较早的删除费用。

> [!NOTE]
> 有关块 blob 和数据解除冻结定价的详细信息, 请参阅[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。 有关出站数据传输费用的详细信息, 请参阅[数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/)。

## <a name="next-steps"></a>后续步骤

* [了解 Blob 存储层](storage-blob-storage-tiers.md)
* [按区域查看 Blob 存储帐户和 GPv2 帐户中的热层、冷层和存档层定价](https://azure.microsoft.com/pricing/details/storage/)
* [管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)
* [检查数据传输定价](https://azure.microsoft.com/pricing/details/data-transfers/)
