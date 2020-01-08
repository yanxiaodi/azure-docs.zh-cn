---
title: 对话学习器默认配置 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解默认对话学习器配置。
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: aaef6f5498e5a8da65d1c829feae8b3e85dba0fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705296"
---
# <a name="default-values-and-boundaries"></a>默认值和边界

本文档介绍对话学习器的默认配置和密钥服务边界。

## <a name="default-configuration"></a>默认配置

参数 | 默认值
--- | --- 
默认会话超时 | 30 分钟

## <a name="boundaries"></a>边界

参数 | 限制
--- | --- 
创作 API，每月的最大 HTTP 调用数 | 5M
创作 API，每秒的最大 HTTP 调用数 | 25
会话 API，每月的最大 HTTP 调用数 | 500K
会话 API，每秒的最大 HTTP 调用数 | 10
每个模型的最大自定义（非编程）实体数 | 请参阅 [LUIS 边界文档](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)；事实上，实际数量可能会略小
每个模型的最大预构建实体数 | 请参阅 [LUIS 边界文档](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)
每个模型的最大实体数（总计） | 100
每个模型的最大操作数 | 32
每个模型的最大定型对话数 | 1000
每个定型对话的最大用户数 | 100
每个模型的最大记录对话数 | 没有预设的限制，但记录对话在被丢弃之前仅保留一段固定期限。  此外，对话学习器 UI 一次将显示 100 个记录对话。 
每个用户的最大模型数 | 没有预设的限制
最大顺序非等待操作数 | 5 (*)

(*) 在 5 个顺序非等待操作后，将屏蔽所有非等待操作，并且对话学习器将在可用的等待操作之间进行选择。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [对话学习器入门](./quickstart.md)
