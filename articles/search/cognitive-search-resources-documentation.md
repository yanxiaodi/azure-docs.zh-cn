---
title: 认知搜索文档资源 - Azure 搜索
description: 与 Azure 搜索中的认知搜索工作负载相关的文章、教程、示例和博客文章的批注列表。
services: search
manager: nitinme
author: HeidiSteen
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 7267f40a981b984ab945d956ff3552157267cd43
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265463"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>认知搜索工作负载的文档资源

认知搜索现在已正式发布，它是 Azure 搜索索引中的新扩充层，用于查找非文本源和无差别文本中的潜在信息，将其转换为 Azure 搜索中的可全文搜索内容。

下文是认知搜索的完整文档。

## <a name="getting-started"></a>入门
+ [什么是认知搜索？](cognitive-search-concept-intro.md)
+ [快速入门：在门户中尝试认知搜索](cognitive-search-quickstart-blob.md)
+ [教程：了解认知搜索 API](cognitive-search-tutorial-blob.md)
+ 示例：[创建认知搜索的自定义技能](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>操作说明指南
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [如何在技能集中引用注释](cognitive-search-concept-annotations-syntax.md)
+ [如何将字段映射到索引](cognitive-search-output-field-mapping.md)
+ [处理和提取图像中的信息](cognitive-search-concept-image-scenarios.md)
+ [如何重新生成 Azure 搜索索引](search-howto-reindex.md)
+ [如何定义自定义技能接口](cognitive-search-custom-skill-interface.md)
+ [故障排除提示](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>参考

+ [预定义技能](cognitive-search-predefined-skills.md)
  + [KeyPhraseExtractionSkill。](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [TranslationSkill。](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ 自定义技能
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [不推荐使用的技能](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [创建技能集 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [创建索引器 (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>请参阅

+ [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Azure 搜索中的索引器](search-indexer-overview.md)
+ [什么是 Azure 搜索？](search-what-is-azure-search.md)
