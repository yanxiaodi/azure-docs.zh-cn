---
title: 快速入门 - 使用 Azure 门户创建服务总线主题和订阅 | Microsoft Docs
description: 本快速入门将介绍如何使用 Azure 门户创建服务总线主题和订阅。
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: a392f8b11a7ab1ad72f4da289c54e34b022f1ea6
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990315"
---
# <a name="quickstart-use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>快速入门：使用 Azure 门户创建一个服务总线主题和多个对该主题的订阅
在本快速入门中，将使用 Azure 门户创建服务总线主题，然后创建对该主题的订阅。 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>什么是服务总线主题和订阅？
服务总线主题和订阅支持 *发布/订阅* 消息通信模型。 在使用主题和订阅时，分布式应用程序的组件不会直接相互通信，而是通过充当中介的主题交换消息。

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

与每条消息都由单个使用方处理的服务总线队列相比，主题和订阅通过发布/订阅模式提供一对多通信方式。 可向一个主题注册多个订阅。 当消息发送到主题时，每个订阅会分别对该消息进行处理。 主题订阅类似于接收发送至该主题的消息副本的虚拟队列。 可以选择基于每个订阅注册主题的筛选规则，这样就可以筛选或限制哪些主题订阅接收发送至某个主题的哪些消息。

利用服务总线主题和订阅，可以进行扩展以处理跨大量用户和应用程序的许多消息。

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 服务总线资源管理器允许用户连接到服务总线命名空间并以一种简单的方式管理消息传送实体。 该工具提供高级功能，如导入/导出功能或用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。 

## <a name="next-steps"></a>后续步骤
要了解如何将消息发送到主题并通过订阅接收这些消息，请参阅以下文章：在目录中选择编程语言。 

> [!div class="nextstepaction"]
> [发布和订阅消息](service-bus-dotnet-how-to-use-topics-subscriptions.md)
