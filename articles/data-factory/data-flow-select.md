---
title: Azure 数据工厂映射数据流选择转换
description: Azure 数据工厂映射数据流选择转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 3c81ec5e213364ed6f159fd20e12879a098caad4
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774990"
---
# <a name="mapping-data-flow-select-transformation"></a>映射数据流选择转换
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

此转换用于列选择性 (减少列数)、别名列和流名称, 以及重新排序列。

## <a name="how-to-use-select-transformation"></a>如何使用选择转换
使用选择转换可为整个流或该流中的列指定别名、分配其他名称（别名），以及稍后在数据流中引用这些新名称。 此转换对于自联接方案很有用。 在 ADF 数据流中实现自联接的方法是，采用流并使用“新建分支”对其进行分支，之后立即添加“选择”转换。 该流现在具有新名称，可使用新名称重新联接到原始流，创建自联接：

![自联接](media/data-flow/selfjoin.png "自联接")

在上面的关系图中，选择转换位于顶部。 这会将原始流的别名指定为“OrigSourceBatting”。 在下面突出显示的联接转换中, 可以看到, 我们使用了 Select alias stream 作为右侧联接, 这使我们可以在内部联接的左侧 & 右侧引用相同的键。

选择转换还可用作从数据流中取消选择列的方法。 例如，如果接收器中定义了 6 列，但只想选取特定的 3 列用于转换并随后流动到接收器，则可使用选择转换仅选择这 3 列。

![选择转换](media/data-flow/newselect1.png "选择别名")

## <a name="options"></a>选项
* “选择”的默认设置是包括所有传入列并保留其原始名称。 可以通过设置选择转换的名称来指定流的别名。
* 若要指定单个列的别名，请取消选择“全选”并使用底部的列映射。
* 选择 "跳过重复项" 以消除输入或输出元数据中的重复列。

![跳过重复项](media/data-flow/select-skip-dup.png "跳过重复项")

* 选择跳过重复项时, 结果将显示在 "检查" 选项卡中。ADF 将保留列的第一次出现, 您将看到此同一列的每个后续匹配项已从您的流中删除。

> [!NOTE]
> 若要清除映射规则, 请按 "**重置**" 按钮。

## <a name="mapping"></a>映射
默认情况下, Select 转换将自动映射所有列, 这将在输出中将所有传入列传递到相同的名称。 在 "选择设置" 中设置的输出流名称将为流定义新的别名。 如果保留选择 "自动映射集", 则可以为整个流提供相同的所有列的别名。

![选择转换规则](media/data-flow/rule2.png "基于规则的映射")

如果要对列进行别名、删除、重命名或重新排序, 必须先关闭 "自动映射"。 默认情况下, 你将看到为你输入的默认规则 "所有输入列"。 如果希望始终允许所有传入列在其输出中映射到相同的名称, 可以将此规则保留原样。

但是, 如果要添加自定义规则, 请单击 "添加映射"。 字段映射将向你提供传入和传出列名的列表, 以映射和别名。 选择 "基于规则的映射" 可创建模式匹配规则。

## <a name="rule-based-mapping"></a>基于规则的映射
当你选择 "基于规则的映射" 时, 将指示 ADF 评估匹配的表达式, 以匹配传入模式规则并定义传出字段名称。 可以添加基于字段和基于规则的映射的任意组合。 然后, 在运行时, 将基于源传入的元数据在运行时生成字段名称。 在调试过程中, 可以使用 "数据预览" 窗格查看生成的字段的名称。

[列模式文档](concepts-data-flow-column-pattern.md)中提供了有关模式匹配的更多详细信息。

## <a name="next-steps"></a>后续步骤
* 使用 "选择重命名"、"重新排序" 和 "别名" 列后, 使用[接收器转换](data-flow-sink.md)将数据插入到数据存储中。
