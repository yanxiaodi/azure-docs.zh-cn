---
title: Marketplace 计量服务 Api-常见问题 |Azure Marketplace
description: 在 Azure Marketplace 中发出 SaaS 产品/服务的使用情况。
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: ccab7e59eaa925df4ba46447cef458111dc7e60a
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869568"
---
# <a name="marketplace-metering-service-apis---faq"></a>Azure 市场计量服务 API - 常见问题解答

一旦 Azure 用户订阅包含按流量计费的 SaaS 服务, 就会跟踪该客户所使用的每个计费维度的消耗情况。 如果消耗超出了为客户选择的期限设置的所含数量, 你的服务会向 Microsoft 发出使用情况事件。

## <a name="emit-usage-events"></a>发出使用情况事件

>[!Note]
>本部分仅适用于 SaaS 产品/服务, 其中至少有一个计划在发布产品/服务时定义了计数服务维度。

![发出使用情况事件](media/isv-emits-usage-event.png)

有关用于发出使用事件的 API 约定的信息, 请参阅[SaaS batch 使用情况事件 api](./marketplace-metering-service-apis.md#batch-usage-event) 。

### <a name="how-often-is-it-expected-to-emit-usage"></a>预期出现使用量的频率如何？

理想情况下, 仅当前一小时内使用时, 才应在过去一小时内每小时发出使用情况。

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>事件发生的时间与向 Microsoft 发送使用事件的时间之间的最大延迟是多少？

理想情况下, 在过去一小时内发生的事件每小时发出一次使用事件。 但会出现延迟。 允许的最大延迟为24小时, 超过此时间后将不接受使用事件。

例如, 如果在一天的下午1日发生使用情况事件, 则在下一天, 你将有一个到下一天下午 1: 这意味着, 如果系统发出的使用时间较低, 则它可以恢复, 然后发送使用情况事件发生的小时间隔, 而不会造成保真度损失。

### <a name="what-happens-when-you-send-more-than-one-usage-event-on-the-same-hour"></a>在同一小时发送多个使用事件时会发生什么情况？

每小时间隔只接受一个使用事件。 小时间隔从第0分钟开始, 到第59分钟结束。  如果在同一小时间隔内发出多个使用事件, 则会删除任何后续的使用事件作为重复项。

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>当你为已取消订阅的 SaaS 订阅发出使用情况时会发生什么情况？

删除 SaaS 订阅后, 将不接受任何发送到 marketplace 平台的使用事件。

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>是否可以获取所有 SaaS 订阅的列表, 包括活动订阅和取消订阅的订阅？

是的, 在调用`GET /saas/subscriptions` API 时, 它将包含所有 SaaS 订阅的列表。 每个 SaaS 订阅的响应中的 "状态" 字段将捕获订阅是处于活动状态还是已取消订阅。 调用列表订阅时, 最多返回100个订阅。

## <a name="next-steps"></a>后续步骤

- 有关详细信息, 请参阅[Marketplace 计量服务 api](./marketplace-metering-service-apis.md) 。
