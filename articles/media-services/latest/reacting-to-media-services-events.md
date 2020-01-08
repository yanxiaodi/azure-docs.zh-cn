---
title: 响应 Azure 媒体服务事件 | Microsoft Docs
description: 使用 Azure 事件网格订阅媒体服务事件。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/08/2019
ms.author: juliako
ms.openlocfilehash: d8cb8fdebb5a7e4bcbc9f979c98085e90ebd4c68
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147159"
---
# <a name="handling-event-grid-events"></a>处理事件网格事件

媒体服务事件允许应用程序使用新式无服务器体系结构对不同事件（例如，作业状态更改事件）进行响应。 为此，它无需复杂的代码或高价低效的轮询服务。 相反，可以通过 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)向事件处理程序（如 [Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure 逻辑应用](https://azure.microsoft.com/services/logic-apps/)），甚至是向自己的 Webhook 推送事件，且仅需为已使用的内容付费。 有关定价的详细信息，请参阅[事件网格定价](https://azure.microsoft.com/pricing/details/event-grid/)。

媒体服务事件的可用性与事件网格[可用性](../../event-grid/overview.md)相关联，当事件网格在其他地区可用时，媒体服务事件也同样可用。  

## <a name="media-services-events-and-schemas"></a>媒体服务事件和架构

事件网格使用[事件订阅](../../event-grid/concepts.md#event-subscriptions)将事件消息路由到订阅方。 媒体服务事件包含响应数据中的更改所需的所有信息。 可以识别媒体服务事件，因为 eventType 属性以“Microsoft.Media”开头。

有关详细信息，请参阅[媒体服务事件架构](media-services-event-schemas.md)。

## <a name="practices-for-consuming-events"></a>使用事件的做法

处理媒体服务事件的应用程序应遵循以下建议的做法：

* 由于可将多个订阅配置为将事件路由至相同的事件处理程序，因此请勿假定事件来自特定的源，而是应检查消息的主题，确保它来自所期望的存储帐户。
* 同样，检查 eventType 是否为准备处理的项，并且不假定所接收的全部事件都是期望的类型。
* 忽略不了解的字段。  此做法有助于适应将来可能添加的新功能。
* 使用“subject”前缀和后缀匹配项，将事件限制为特定事件。

> [!NOTE]
> 事件取决于事件网格[服务级别协议（SLA）](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/)。 如果要使用 Api 获取事件通知，请参阅使用[.NET sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/ContentProtection/BasicAESClearKey)或[Java sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)时如何使用事件的示例。

## <a name="next-steps"></a>后续步骤

* [监视事件-门户](monitor-events-portal-how-to.md)
* [监视事件-CLI](job-state-events-cli-how-to.md)
