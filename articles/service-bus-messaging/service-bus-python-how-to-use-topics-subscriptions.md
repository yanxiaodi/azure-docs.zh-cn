---
title: 如何通过 Python 使用 Azure 服务总线主题 | Microsoft Docs
description: 了解如何通过 Python 使用 Azure 服务总线主题和订阅。
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: ef0237b38c8f640c0fc4b1b1788215c8804a5cd4
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141901"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>如何通过 Python 使用服务总线主题和订阅

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文介绍了如何使用服务总线主题和订阅。 示例是用 Python 编写的, 并使用了[Azure PYTHON SDK 包][Azure Python package]。 涉及的方案包括：

- 创建主题和订阅 
- 创建订阅筛选器 
- 将消息发送到主题 
- 从订阅接收消息
- 删除主题和订阅

## <a name="prerequisites"></a>先决条件
1. Azure 订阅。 要完成本教程，需要一个 Azure 帐户。 你可以[激活 Visual Studio 或 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或者注册[免费试用帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
2. 按照[快速入门：使用 Azure 门户创建一个服务总线主题和对此主题的订阅](service-bus-quickstart-topics-subscriptions-portal.md)中的步骤来创建服务总线**命名空间**并获取**连接字符串**。

    > [!NOTE]
    > 在本快速入门中，你将使用 **Python** 创建一个**主题**和对此主题的**订阅**。 
3. 安装[Azure Python 包][Azure Python package]。 请参阅 [Python 安装指南](/azure/python/python-sdk-azure-install)。

## <a name="create-a-topic"></a>创建主题

可以通过 **ServiceBusService** 对象处理主题。 将以下代码添加到任何 Python 文件的顶部附近，你希望在其中以编程方式访问服务总线：

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

以下代码创建 **ServiceBusService** 对象。 将 `mynamespace`、`sharedaccesskeyname` 和 `sharedaccesskey` 替换为实际的命名空间、共享访问签名 (SAS) 密钥名称和密钥值。

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

可从 [Azure 门户][Azure portal]获取 SAS 密钥名称值和其值。

```python
bus_service.create_topic('mytopic')
```

`create_topic` 方法还支持其他选项，通过这些选项可以替代默认主题设置，例如消息生存时间或最大主题大小。 以下示例将最大主题大小设置为 5 GB，将生存时间 (TTL) 值设置为 1 分钟：

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>创建订阅

主题订阅也是使用 **ServiceBusService** 对象创建的。 为订阅命名，并且订阅可以具有可选筛选器，以限制传送到订阅的虚拟队列的消息集。

> [!NOTE]
> 默认情况下, 订阅是永久性的, 并且将继续存在, 直到删除这些订阅或它们所订阅的主题。
> 
> 可以通过设置[auto_delete_on_idle 属性](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python)来自动删除订阅。

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>创建具有默认 (MatchAll) 筛选器的订阅

如果在创建新订阅时未指定任何筛选器，则将使用默认的 MatchAll 筛选器。 使用 **MatchAll** 筛选器时，发布到主题的所有消息都将置于订阅的虚拟队列中。 以下示例创建名为 `AllMessages` 的订阅，并使用默认的 **MatchAll** 筛选器。

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>创建具有筛选器的订阅

还可以定义筛选器，以指定发送到主题的哪些消息应该在特定主题订阅中显示。

订阅支持的最灵活的一种筛选器类型是 **SqlFilter**，它实现了部分 SQL92 功能。 SQL 筛选器将对发布到主题的消息的属性进行操作。 有关可用于 SQL 筛选器的表达式的详细信息，请参阅 [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 语法。

可使用 **ServiceBusService** 对象的 **create\_rule** 方法向订阅添加筛选器。 此方法允许向现有订阅中添加新筛选器。

> [!NOTE]
> 由于默认筛选器会自动应用到所有新订阅，因此，必须首先删除默认筛选器，否则 MatchAll 会替代可能指定的任何其他筛选器。 可以使用 ServiceBusService 对象的 `delete_rule` 方法删除默认规则。
> 
> 

以下示例创建一个名为 `HighMessages` 的订阅，该订阅包含一个 SqlFilter，它仅选择自定义 `messagenumber` 属性大于 3 的消息：

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

同样，以下示例创建一个名为 `LowMessages` 的订阅，该订阅包含一个 SqlFilter，它仅选择 `messagenumber` 属性小于或等于 3 的消息：

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

现在，消息发送到 `mytopic` 时，会始终传送到订阅了 **AllMessages** 主题订阅的接收方，并选择性地传送到订阅了 **HighMessages** 和 **LowMessages** 主题订阅的接收方（具体取决于消息内容）。

## <a name="send-messages-to-a-topic"></a>将消息发送到主题

要将消息发送到服务总线主题，应用程序必须使用 ServiceBusService 对象的 `send_topic_message` 方法。

以下示例演示如何向 `mytopic` 发送五条测试消息。 每条消息的 `messagenumber` 属性值因循环迭代而异（这会确定哪些订阅接收它）：

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messagenumber': i})
    bus_service.send_topic_message('mytopic', msg)
```

服务总线主题在[标准层](service-bus-premium-messaging.md)中支持的最大消息容量为 256 KB，在[高级层](service-bus-premium-messaging.md)中则为 1 MB。 标头最大大小为 64 KB，其中包括标准和自定义应用程序属性。 一个主题中包含的消息数量不受限制，但消息的总大小受限制。 此主题大小是在创建时定义的，上限为 5 GB。 有关配额的详细信息，请参阅[服务总线配额][Service Bus quotas]。

## <a name="receive-messages-from-a-subscription"></a>从订阅接收消息

对 ServiceBusService 对象使用 `receive_subscription_message` 方法可从订阅接收消息：

```python
msg = bus_service.receive_subscription_message(
    'mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

参数 `peek_lock` 设置为“False”时，会在读取消息后将其从订阅删除。 通过将参数 `peek_lock` 设置为“True”，可读取（速览）并锁定消息而不会将其从队列中删除。

在接收过程中读取并删除消息的行为是最简单的模式，并且最适合在发生故障时应用程序可以容忍不处理消息的情况。 为了理解此行为，可以考虑这样一种情形：使用方发出接收请求，但在处理该请求前发生了崩溃。 由于服务总线已将消息标记为“已使用”，因此当应用程序重启并重新开始使用消息时，它就漏掉了在发生故障前使用的消息。

如果将 `peek_lock` 参数设置为“True”，则接收会变成一个两阶段操作，从而可支持无法容忍遗漏消息的应用程序。 当 Service Bus 收到请求时，它会查找下一条要使用的消息，锁定该消息以防其他使用者接收，然后将该消息返回到应用程序。 应用程序处理完消息（或安全存储该消息以供将来处理）后，会通过对 Message 对象调用 `delete` 方法完成接收过程的第二个阶段。 `delete` 方法会将消息标记为已使用，并将其从订阅中删除。

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>如何处理应用程序崩溃和不可读消息

服务总线提供了相关功能，帮助你轻松地从应用程序错误或消息处理问题中恢复。 如果接收方应用程序因某种原因无法处理消息，则可对 Message 对象调用 `unlock` 方法。 此方法会导致服务总线解锁订阅中的消息并使其能够重新被同一个正在使用的应用程序或其他正在使用的应用程序接收。

另外，还存在与订阅中已锁定消息关联的超时，并且如果应用程序无法在锁定超时到期之前处理消息（例如，如果应用程序崩溃），则服务总线会自动解锁该消息并使其可再次被接收。

如果应用程序在处理消息之后，但在调用 `delete` 方法之前崩溃，则在应用程序重启时会将该消息重新传送给它。 此行为通常称为 “至少处理一次”\*，即每条消息将至少被处理一次，但在某些情况下，同一消息可能会被重新传送。 如果方案无法容忍重复处理，则应用程序开发人员应向其应用程序添加更多逻辑以处理重复消息传送。 为此，可以使用消息的 **MessageId** 属性，该属性在各次传送尝试中保持不变。

## <a name="delete-topics-and-subscriptions"></a>删除主题和订阅

除非设置[auto_delete_on_idle 属性](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python), 否则主题和订阅是永久性的。 可以通过[Azure 门户][Azure portal]或以编程方式删除这些方法。 以下示例说明如何删除名为 `mytopic` 的主题：

```python
bus_service.delete_topic('mytopic')
```

删除某个主题也会删除向该主题注册的所有订阅。 也可以单独删除订阅。 以下代码说明如何从 `mytopic` 主题删除名为 `HighMessages` 的订阅：

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

> [!NOTE]
> 可以使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer/)管理服务总线资源。 服务总线资源管理器允许用户连接到服务总线命名空间并以一种简单的方式管理消息传送实体。 该工具提供高级功能，如导入/导出功能或用于对主题、队列、订阅、中继服务、通知中心和事件中心进行测试的功能。 

## <a name="next-steps"></a>后续步骤

现在，已了解有关 Service Bus 主题的基础知识，单击下面的链接可了解更多信息。

* 请参阅[队列、主题和订阅][Queues, topics, and subscriptions]。
* [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 参考。

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
