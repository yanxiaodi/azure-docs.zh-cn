---
title: Azure 数据工厂映射数据流查找转换
description: Azure 数据工厂映射数据流查找转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 197f5ba9d6921f4a9921b7074b9e05162d3e37b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868127"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure 数据工厂映射数据流查找转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用“查找”将其他源的参考数据添加到数据流中。 查找转换需要一个定义的源，它指向你的引用表并匹配关键字段。

![查找转换](media/data-flow/lookup1.png "查找")

选择要在传入流字段和参考源字段之间匹配的关键字段。 必须先在数据流设计画布上创建一个新源，以在右侧用于查找。

找到匹配项后，参考源中生成的行和列将添加到数据流中。 可以在数据流的末尾选择要包含在接收器中的感兴趣的字段。

## <a name="match--no-match"></a>匹配 / 没有匹配项

之后在查找转换，可用于后续转换使用表达式函数检查每个匹配行的结果`isMatch()`根据是否在查找导致行匹配或不在逻辑中进一步进行选择。

## <a name="optimizations"></a>优化

在数据工厂中的数据流中执行向外扩展 Spark 环境。 如果你的数据集可以放入辅助角色节点的内存空间中，我们可以优化查找性能。

![广播联接](media/data-flow/broadcast.png "广播联接")

### <a name="broadcast-join"></a>广播的联接

选择左侧和/或右侧广播联接请求 ADF 从查找关系的任何一侧的整个数据集推送到内存中。

### <a name="data-partitioning"></a>数据分区

此外可以指定通过选择"设置分区"优化选项卡上的查找转换来创建可以更好地适合于每个辅助角色的内存的数据集的分区的数据。

## <a name="next-steps"></a>后续步骤

[加入](data-flow-join.md)并[Exists](data-flow-exists.md)转换 ADF 映射数据流中执行类似的任务。 看一看这些转换下一步。
