---
title: Azure Monitor 日志中的搜索查询 | Microsoft Docs
description: 本文提供有关在 Azure Monitor 日志中使用搜索查询的入门教程。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.openlocfilehash: b118740f3a57e168c5dfb071c199bcf424bd5113
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295563"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Azure Monitor 日志中的搜索查询
Azure Monitor 日志查询可以从表名或 search 命令开始。 本教程介绍基于搜索的查询。 每种方法各有优势。

基于表的查询首先限定查询范围，因此往往比搜索查询更加高效。 搜索查询的结构化程度不高，因此，在跨列或表搜索特定的值时，它是更好的选择。 **搜索**可以在给定表或所有表的所有列中扫描指定的值。 处理的数据量可能十分巨大，正因如此，这些查询可能需要更长的时间才能完成，并且可能返回极大的结果集。

## <a name="search-a-term"></a>搜索词语
**search** 命令通常用于搜索特定的词语。 以下示例在所有表的所有列中扫描词语“error”：

```Kusto
search "error"
| take 100
```

如上所示的无范围查询尽管用法简单，但并不高效，且可能返回大量不相关的结果。 更好的做法是在相关表甚至特定的列中执行搜索。

### <a name="table-scoping"></a>表范围限定
若要在特定的表中搜索某个词语，请紧靠在 **search** 运算符的后面添加 `in (table-name)`：

```Kusto
search in (Event) "error"
| take 100
```

或者在多个表中：
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>表和列范围限定
默认情况下，**search** 将评估数据集中的所有列。 如果只想搜索特定的列，请使用以下语法：

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> 如果使用 `==` 而不是 `:`，则结果将包含如下所述的记录：其中的 *Source* 列包含确切值“error”（大小写完全与此相同）。 使用: 将包括记录其中*源*具有值，例如"错误代码 404"或"错误"。

## <a name="case-sensitivity"></a>区分大小写
默认情况下，词语搜索不区分大小写，因此，搜索“dns”可能会产生“DNS”、“dns”或“Dns”等结果。 若要执行区分大小写的搜索，请使用 `kind` 选项：

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>使用通配符
**search** 命令支持在词语的开头、末尾或中间使用通配符。

若要搜索以“win”开头的词语：
```Kusto
search in (Event) "win*"
| take 100
```

若要搜索以“.com”结尾的词语：
```Kusto
search in (Event) "*.com"
| take 100
```

若要搜索包含“www”的词语：
```Kusto
search in (Event) "*www*"
| take 100
```

若要搜索词以“corp”开头、以“.com”结尾的词语（例如“corp.mydomain.com”）

```Kusto
search in (Event) "corp*.com"
| take 100
```

此外，可以只使用通配符来获取表中的所有内容：`search in (Event) *`，但结果与只是编写 `Event` 相同。

> [!TIP]
> 尽管可以使用 `search *` 来获取每个表中的每个列，但我们建议始终将查询范围限定为特定的表。 无范围查询可能需要花费一段时间才能完成，并且可能返回过多的结果。

## <a name="add-and--or-to-search-queries"></a>将 *and* / *or* 添加到搜索查询
使用 **and** 可以搜索包含多个词语的记录：

```Kusto
search in (Event) "error" and "register"
| take 100
```

使用 **or** 可以获取至少包含一个词语的记录：

```Kusto
search in (Event) "error" or "register"
| take 100
```

如果有多个搜索条件，可以使用括号将其合并到同一个查询：

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

此示例的结果是既包含词语“error”，也包含“register”或者以“marshal”开头的内容的记录。

## <a name="pipe-search-queries"></a>使用竖线分隔搜索查询
与其他任何命令一样，可以使用竖线分隔 **search**，以便可以筛选、排序和聚合搜索结果。 例如，若要获取包含“win”的 *Event* 记录数：

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>后续步骤

- 在 [Kusto 查询语言站点](/azure/kusto/query/)上参阅其他教程。
