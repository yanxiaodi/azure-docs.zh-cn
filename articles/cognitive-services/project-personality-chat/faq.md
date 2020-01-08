---
title: 常见问题解答 - 个性化聊天
titlesuffix: Azure Cognitive Services
description: 有关个性聊天的常见问题解答
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nitinme
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ROBOTS: NOINDEX
ms.openlocfilehash: d5f144fa0bf5325bfe2922fdc00c6689d894ff7e
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258577"
---
# <a name="frequently-asked-questions"></a>常见问题

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>为什么它不像其他聊天机器人一样回答我提出的每一个问题？

项目个性聊天将增强机器人进行常见闲聊的能力，以展示个性并创造更完整的用户体验。 它的设计初衷并不是要就与机器人的主要功能无关的话题进行长时间的对话。 虽然它可以对所有聊天做出响应，但在 beta 版本中，它被限制在常见的闲聊领域。

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>如何根据自己的品牌自定义个性？

从可用的默认角色中选择最接近的角色。 现在，你可以利用编辑库编辑回复，以更好地适应你的品牌。 将来，你可以上传一组来自所选个性的示例陈述，并找到最接近的角色 ID 版本。 还有一些方法可以重新训练和自定义模型。

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>此服务是否支持现有的智能代理（如 Zo）？

为 Zo、Cortana 和项目个性聊天提供支持的服务共享一些类似的技术，但它们是不同的堆栈。 它从 Zo 和 Cortana 的经验中吸取经验教训。

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>此服务会导致不好的客户体验吗？

为了提供更丰富的体验，个性聊天可以生成编辑数据集之外的响应，并尝试解释所有用户输入。 因此，有可能响应在上下文中看起来不合适。 基于 Zo 等智能代理的知识构建，已经实施了一系列控制措施，以帮助防止不宜的响应。 默认情况下，项目个性聊天设置为仅响应已识别的用户意图。 你可能想要测试项目个性聊天是否适合你的情况。 如果你发现任何需要进一步培训的内容，欢迎你提供反馈。 如果你将来将此服务用于你的客户，我们建议你考虑匿名日志记录，以帮助你确定实时用户交互存在的问题。
