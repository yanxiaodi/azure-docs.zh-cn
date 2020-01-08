---
title: 查询表达式语法 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解如何在学术知识 API 中使用查询表达式语法。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 3b87e04c2d6380a0ee4157e73db0cd4057fadee1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704929"
---
# <a name="query-expression-syntax"></a>查询表达式语法

我们已经看到对 interpret请求的响应包括查询表达式。 解释用户查询的语法为每个解释创建了一个查询表达式。 然后，可以使用查询表达式发出 evaluate请求以检索实体搜索结果。

你还可以构造自己的查询表达式，并在 evaluate请求中使用它们。 如果要构建自己的用户界面来创建查询表达式以响应用户的操作，这将非常有用。 为此，你需要了解查询表达式的语法。  

可以包含在查询表达式中的每个实体属性都具有特定数据类型和一组可能的查询运算符。 [实体属性](EntityAttributes.md)中指定了每个属性的实体属性集和支持的运算符。 单值查询要求该属性支持 Equals操作。 前缀查询要求该属性支持 StartsWith 操作。 数字范围查询要求该属性支持 IsBetween 操作。

某些实体数据存储为复合属性，如属性名称中的点“.”所示。 例如，作者/关联信息表示为复合属性。 它包含 4 个组件：AuN、AuId、AfN、AfId。 这些组件是形成单个实体属性值的单独数据片段。


**字符串属性：单值**（包括与同义词的匹配）  
Ti='通过潜在语义分析进行索引'  
Composite(AA.AuN='sue dumais')

**字符串属性：精确单值**（仅匹配规范值）  
Ti=='通过潜在语义分析进行索引'  
Composite(AA.AuN=='susan t dumais')
     
**字符串属性：前缀值**   
Ti='通过潜在 seman 进行索引'...  
Composite(AA.AuN='sue du'...)

**数值属性：单值**  
Y=2010
 
**数值属性：范围值**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y=\[2010, 2012\)（仅包括左边界值：2010、2011）  
Y=\[2010, 2012\]（包括两个边界值：2010、2011、2012）
 
**数值属性：前缀值**  
Y='19'... (以 19 开头的任何数字值) 
 
**日期属性：单值**  
D='2010-02-04'

**日期属性：范围值**  
D>'2010-02-03'  
D=['2010-02-03','2010-02-05']

And/Or 查询：  
And(Y=1985, Ti='电子系统无序')  
Or(Ti='电子系统无序', Ti='容错原则和实践')  
And(Or(Y=1985,Y=2008), Ti='电子系统无序')
 
复合查询：  
要查询复合属性的组件，需要将引用复合属性的查询表达式的一部分括在 Composite() 函数中。 

例如，要按作者姓名查询论文，请使用以下查询：
```
Composite(AA.AuN='mike smith')
```
<br>要在作者位于特定机构时查询特定作者的论文，请使用以下查询：
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Composite() 函数将复合属性的两个部分绑定在一起。 这表示我们只能在“Mike Smith”就读于哈佛大学期间获得其中一位作者是“Mike Smith”的论文。 

要查询在特定机构与（其他）作者有关的特定作者的论文，请使用以下查询：
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>在此版本中，因为在 And() 之前将 Composite() 单独应用于作者和隶属关系，我们得到的所有论文中，其中一位作者是“Mike Smith”，并且其中一位作者的隶属关系是“哈佛”。 虽然听起来类似于前面的查询示例，但这不是一回事。

通常情况下，请考虑以下示例：我们有一个复合属性 C，它有两个组件 A 和 B。一个实体可能有多个 C 值。以下是我们的实体：
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>查询 
```
Composite(And(C.A=1, C.B=2))
```

<br>仅匹配具有 C 值的实体，其中组件 C.A 为 1 且组件 C.B 为 2。 仅 E1 匹配此查询。

查询 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>匹配具有 C 值的实体，其中 C.A 是 1 并且还具有另外一个 C 值，其中 C.B 是 2。 E1 和 E2 均匹配此查询。

请注意：  
- 你无法在 Composite() 函数之外引用复合属性的一部分。
- 你无法在同一个 Composite() 函数中引用两个不同复合属性的部分。
- 你无法在 Composite() 函数中引用不属于复合属性的属性。
