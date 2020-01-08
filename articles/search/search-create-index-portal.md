---
title: 在 Azure 门户中创建 Azure 搜索索引 - Azure 搜索
description: 了解如何使用内置门户索引设计器为 Azure 搜索创建索引。
manager: nitinme
author: heidisteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: heidist
ms.openlocfilehash: fec81cd9660348d492b1dabd24ac689f2b06e880
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638807"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>在门户中创建 Azure 搜索索引

Azure 搜索在门户中包含一个内置索引设计器，可用于原型或创建在 Azure 搜索服务上托管的[搜索索引](search-what-is-an-index.md)。 该工具用于架构构造。 保存定义时，Azure 搜索中将完全表示空索引。 如何使用可搜索的数据加载它取决于你。

索引设计器只是创建索引的一种方法。 以编程方式，你可以使用 [.NET](search-create-index-dotnet.md) 或 [REST](search-create-index-rest-api.md) API 创建索引。

## <a name="start-index-designer"></a>启动索引设计器

1. 登录到 [Azure 门户](https://portal.azure.com)，并打开服务仪表板。 可以单击跳转栏中的“所有服务”，搜索当前订阅中的现有“搜索服务”。 

2. 单击页面顶部的命令栏中的“添加索引”链接。

   ![在命令栏中添加索引链接](media/search-create-index-portal/add-index.png "Add index link in the command bar")

3. 命名 Azure 搜索索引。 索引名称在索引操作和查询操作中进行引用。 索引名称将成为在到索引的连接上使用的且用于在 Azure 搜索 REST API 中发送 HTTP 请求的终结点 URL 的一部分。

   * 以字母开头。
   * 仅使用小写字母、数字或短划线（“-”）。
   * 名称的长度上限为 60 个字符。

## <a name="add-fields"></a>添加字段

索引构成包括一个“字段集合”，它定义了索引中的可搜索数据。 总而言之，字段集合指定了你单独上传的文档的结构。 字段集合包括必需字段和可选字段（已命名和已键入的），并具有决定了字段使用方式的索引属性。

1. 添加字段来完整指定将要上传的文档，为每个文档设置[数据类型](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)。 例如，如果文档包含 hotel-id、hotel-name、address、city 和 region，请在索引中为每个字段创建对应的字段。 有关设置属性方面的帮助信息，请查看[下一部分中的设计指南](#design)。

2. 指定 Edm.String 类型的 key 字段。 此字段的值必须唯一标识每个文档。 默认情况下，该字段名为 *id*，但可以将其重命名，只要该字符串符合[命名规则](https://docs.microsoft.com/rest/api/searchservice/Naming-rules)即可。 例如，如果字段集合包含 hotel-id，则可以为密钥选择该字段。 对于每个 Azure 搜索索引来说，键字段都是必需的，并且它必须是一个字符串。

3. 在每个字段上设置属性。 索引设计器会排除对数据类型无效的任何属性，但不会建议要包含的内容。 查看下一部分中的指导，以了解属性的用途。

    Azure 搜索 API 文档包括了代码示例，其中采用了简单的 *hotels* 索引。 在下面的屏幕截图中，可以看到索引定义，包括在定义索引期间指定的法语语言分析器，可以在门户中练习重新创建该索引定义。

    ![酒店演示索引](media/search-create-index-portal/field-definitions.png "Hotels demo index")

4. 完成时，单击“创建”以保存并创建索引。

<a name="design"></a>

## <a name="set-attributes"></a>设置属性

尽管可以随时添加新字段，但在索引的生存期内现有字段定义将被锁定。 因此，开发人员通常使用门户创建简单索引、测试创意，或者使用门户页面来查找设置。 如果采用基于代码的方式以便可以轻松重新生成索引，那么对索引进行频繁迭代的设计就更为高效。

在保存索引之前，将分析器和建议器与字段进行关联。 请确保在创建索引定义时将语言分析器或建议器添加到索引定义中。

字符串字段通常标记为“可搜索”和“可检索”。 用来缩小搜索结果范围的字段包括“可排序”、“可筛选”和“可查找”。

字段属性决定了字段的使用方式，例如，是否用于全文搜索、分面导航和排序等操作中。 下表介绍了每个属性。

|特性|描述|  
|---------------|-----------------|  
|**可搜索**|可全文搜索，在编制索引期间遵从语法分析，例如分词。 如果将某个可搜索字段设置为“sunny day”之类的值，在内部它将拆分为单独的标记“sunny”和“day”。 有关详细信息，请参阅[全文搜索工作原理](search-lucene-query-architecture.md)。|  
|**可筛选**|在 **$filter** 查询中引用。 `Edm.String` 或 `Collection(Edm.String)` 类型的可筛选字段不进行分词，因此，比较仅用于查找完全匹配项。 例如，如果将此类字段 f 设置为“sunny day”，则 `$filter=f eq 'sunny'` 将找不到任何匹配项，但 `$filter=f eq 'sunny day'` 可找到。 |  
|**可排序**|默认情况下，系统按分数对结果进行排序，但可以配置基于文档中字段的排序。 `Collection(Edm.String)` 类型的字段不能为“可排序”。 |  
|**可查找**|通常用于包括了按类别（例如特定城市中的宾馆）的命中次数的搜索结果呈现中。 此选项无法与 `Edm.GeographyPoint` 类型的字段一起使用。 `Edm.String` 类型的**可筛选**、**可排序**或**可查找**字段的长度最多可以是 32 千字节。 有关详细信息，请参阅[创建索引 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)。|  
|**键**|文档在索引内的唯一标识符。 必须仅选择单个字段作为键字段，并且它必须是 `Edm.String` 类型的。|  
|**可检索**|决定了是否可以在搜索结果中返回此字段。 当希望将某个字段（例如“利润”）用作筛选器、排序或评分机制，但不希望该字段显示给最终用户时，这很有用。 对于 `key` 字段，此属性必须为 `true`。|  

## <a name="next-steps"></a>后续步骤

在创建 Azure 搜索索引后，可以前进到下一步骤：[将可搜索数据上传到索引中](search-what-is-data-import.md)。

另外，也可以[更深入地了解索引](search-what-is-an-index.md)。 除了字段集合之外，索引还指定分析器、建议器、评分配置文件和 CORS 设置。 门户提供了用于定义以下最常用元素的选项卡式页面：字段、分析器和建议器。 若要创建或修改其他元素，可以使用 REST API 或 .NET SDK。

## <a name="see-also"></a>请参阅

 [全文搜索工作原理](search-lucene-query-architecture.md)  
 [搜索服务 REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

