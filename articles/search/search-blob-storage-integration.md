---
title: 将全文搜索添加到 Azure Blob 存储 - Azure 搜索
description: 在代码中使用 HTTP REST API 抓取 Azure Blob 存储中用于 Azure 搜索索引的文本内容。
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: nitinme
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: f0801931b57302ae1d627dab783a40d2407c19ac
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650086"
---
# <a name="searching-blob-storage-with-azure-search"></a>使用 Azure 搜索来搜索 Blob 存储

在 Azure Blob 存储中存储的各种内容类型之间进行搜索可能是一个很难解决的问题。 但是，使用 Azure 搜索，只需单击几下，便可以为 Blob 中的内容编制索引以及对其进行搜索。 在 Blob 存储中进行搜索需要预配 Azure 搜索服务。 可以在[定价页](https://aka.ms/azspricing)上找到 Azure 搜索的各种服务限制和定价层。

## <a name="what-is-azure-search"></a>什么是 Azure 搜索？
[Azure 搜索](https://aka.ms/whatisazsearch)是一种搜索服务，通过它开发人员可以轻松地在 Web 和移动应用程序中添加可靠的全文搜索体验。 作为一项服务，Azure 搜索不需要管理任何搜索基础结构，同时提供了 [99.9% 运行时间 SLA](https://aka.ms/azuresearchsla)。

## <a name="index-and-search-enterprise-document-formats"></a>索引和搜索企业文档格式
通过对 Azure Blob 存储中的[文档提取](https://aka.ms/azsblobindexer)的支持，可以索引以下内容：

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

通过从这些文件类型中提取文本和元数据，可以使用单个查询跨多个文件格式进行搜索。 

## <a name="search-through-your-blob-metadata"></a>在 blob 元数据中进行搜索
用于实现在任何内容类型的 blob 中轻松进行排序的一个常用方案是为自定义元数据和每个 blob 的系统属性编制索引。 通过这种方式，系统会对所有 blob 的信息编制索引，无论文档类型是什么，都是如此。 然后可以在所有 Blob 存储内容中继续执行排序、筛选和 facet 操作。

[了解有关为 blob 元数据编制索引的详细信息。](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>图像搜索
Azure 搜索的全文搜索、分面导航和排序功能现在可以应用于 blob 中存储的图像的元数据了。

认知搜索包括图像处理技能，例如[光学字符识别 (OCR)](cognitive-search-skill-ocr.md) 和[可视特征](cognitive-search-skill-image-analysis.md)的标识，这些技能可以用于为每个图像中发现的可视内容编制索引。

## <a name="index-and-search-through-json-blobs"></a>在 JSON blob 中编制索引和执行搜索
可以将 Azure 搜索配置为提取在包含 JSON 的 blob 中找到的结构化内容。 Azure 搜索可以读取 JSON blob 并将结构化内容解析为 Azure 搜索文档的合适字段。 Azure 搜索还可以获取包含 JSON 对象数组的 blob 并将每个元素映射到单独的 Azure 搜索文档。

JSON 解析当前不可通过门户进行配置。 [了解有关 Azure 搜索中的 JSON 解析的详细信息。](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>快速入门
可以直接从 Blob 存储门户页将 Azure 搜索添加到 blob。

![](./media/search-blob-storage-integration/blob-blade.png)

单击“添加 Azure 搜索”将启动一个工作流，可以在其中选择现有 Azure 搜索服务或创建一个新服务。 如果创建新服务，则会离开存储帐户的门户。 可以导航回存储门户页并重新选择“添加 Azure 搜索”选项，然后可以选择现有服务。

## <a name="next-steps"></a>后续步骤
在完整[文档](https://aka.ms/azsblobindexer)中详细了解 Azure 搜索 Blob 索引器。
