---
title: 将数据集添加到现有 Azure 数据共享预览
description: 向现有数据共享添加数据集
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: bd8cd7af72c349060eb035dc32e9ddd1a7f9920e
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327509"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>如何将数据集添加到 Azure 数据共享预览中的现有共享

本文介绍如何使用 Azure 数据共享预览版向预先存在的数据共享添加数据集。 这样, 就可以与相同的收件人共享更多的数据, 而不必创建新的共享。

有关如何在创建共享时添加数据集的信息, 请参阅[共享数据](share-your-data.md)教程。

## <a name="navigate-to-a-sent-data-share"></a>导航到已发送的数据共享

在 Azure 数据共享预览中, 导航到已发送的共享, 然后选择 "**数据集**" 选项卡。单击 " **+ 添加数据集**" 按钮, 添加更多数据集。

![添加数据集](./media/how-to/how-to-add-datasets/add-datasets.png)

在右侧面板中, 选择要添加的数据集类型, 然后单击 "**下一步**"。 选择要添加的数据的订阅和资源组。 使用下拉箭头, 查找并选中要添加的数据旁边的复选框。

![添加数据集](./media/how-to/how-to-add-datasets/add-datasets-side.png)

单击 "**添加数据集**" 后, 这些数据集将添加到你的共享中。 注意:要使快照能够查看新的数据集, 必须由使用者触发快照。 如果已配置快照设置, 则在下一个计划的快照完成后, 使用者将看到新的数据集。 如果未配置快照设置, 则使用者必须手动触发数据的完整副本或增量副本才能接收更新。 有关快照的详细信息, 请参阅[快照](terminology.md)。

## <a name="next-steps"></a>后续步骤
了解有关如何[将收件人添加到现有数据共享的](how-to-add-recipients.md)详细信息。