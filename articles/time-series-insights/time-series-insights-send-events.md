---
title: 向 Azure 时序见解环境发送事件 | Microsoft Docs
description: 了解如何配置事件中心，并运行示例应用程序来推送可以在 Azure 时序见解中查看的事件。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 84eb0e230875b999218b67d47a66a3c92b494253
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072842"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>通过使用事件中心向时序见解环境发送事件

本文介绍如何在 Azure 事件中心中创建和配置事件中心。 其中还介绍了如何运行示例应用程序将事件从事件中心推送到 Azure 时序见解。 如果你已经有了一个事件中心，其中的事件采用 JSON 格式，则可跳过本教程，在 [Azure 时序见解](./time-series-insights-update-create-environment.md)中查看你的环境。

## <a name="configure-an-event-hub"></a>配置事件中心

1. 若要了解如何创建事件中心，请参阅[事件中心文档](https://docs.microsoft.com/azure/event-hubs/)。
1. 在搜索框中，搜索“事件中心”。 在返回的列表中选择“事件中心”。
1. 选择事件中心。
1. 在创建事件中心时，实际上要创建事件中心命名空间。 如果尚未在命名空间中创建事件中心，请在菜单中的“实体”下创建事件中心。  

    [![事件中心列表](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. 创建事件中心后，请在事件中心列表中选择它。
1. 在菜单中的“实体”下，选择“事件中心”。
1. 选择事件中心的名称对其进行配置。
1. 在 "**概述**" 下, 选择 "**使用者组**", 然后选择 "**使用者组**"。

    [![创建使用者组](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. 请确保创建一个使用者组，由时序见解事件源独占使用。

    > [!IMPORTANT]
    > 请确保该使用者组没有被任何其他服务（例如 Azure 流分析作业或其他时序见解环境）使用。 如果使用者组由其他服务使用，则此环境和其他服务的读取操作会受到负面影响。 如果使用 $Default 作为使用者组，则其他读者可能会重复使用使用者组。

1. 在菜单中的“设置”下，选择“共享访问策略”，然后选择“添加”。

    [![选择“共享访问策略”，然后选择“添加”按钮](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. 在“添加新的共享访问策略”窗格中，创建名为“MySendPolicy”的共享访问。 将使用此共享访问策略在本文后面的 C# 示例中发送事件。

    [![在“策略名称”框中输入 MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. 在“声明”下选择“发送”复选框。

## <a name="add-a-time-series-insights-instance"></a>添加时序见解实例

时序见解更新使用实例将上下文数据添加到传入的遥测数据中。 使用时间序列 ID 在查询时加入数据。 在本文后面使用的示例 windmills 项目的“时间序列 ID”是 `id`。 若要详细了解时序见解实例和时间序列 ID，请参阅[时序模型](./time-series-insights-update-tsm.md)。

### <a name="create-a-time-series-insights-event-source"></a>创建时序见解事件源

1. 如果尚未创建事件源，请完成步骤以[创建事件源](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)。

1. 为 `timeSeriesId` 设置一个值。 若要详细了解时间序列 ID，请参阅[时序模型](./time-series-insights-update-tsm.md)。

### <a name="push-events"></a>推送事件（windmills 示例）

1. 在搜索栏中搜索“事件中心”。 在返回的列表中选择“事件中心”。

1. 选择事件中心实例。

1. 请访问**共享访问策略** > **MySendPolicy**。 复制“连接字符串 - 主密钥”的值。

    [![复制主密钥连接字符串的值](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. 转到  https://tsiclientsample.azurewebsites.net/windFarmGen.html 。 URL 运行模拟 windmill 设备。
1. 在网页上的“事件中心连接字符串”框中，粘贴在[推送事件](#push-events)中复制的连接字符串。
  
    [![将主密钥连接字符串粘贴到“事件中心连接字符串”框中](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. 选择“单击可启动”。 模拟器生成可以直接使用的实例 JSON。

1. 返回到 Azure 门户中的事件中心。 在“概述”页面上，可以看到事件中心收到的新事件：

    [![显示事件中心指标的事件中心“概述”页](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>支持的 JSON 形状

### <a name="example-one"></a>示例一

* **输入**：一个简单的 JSON 对象。

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **输出**：一个事件。

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>示例二

* **输入**：包含两个 JSON 对象的 JSON 数组。 每个 JSON 对象都转换为事件。

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **输出**：两个事件。

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>示例三

* **输入**：具有嵌套 JSON 数组（其中包含两个 JSON 对象）的 JSON 对象。

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **输出**：两个事件。 “location”属性复制到每个事件。

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>示例四

* **输入**：具有嵌套 JSON 数组（其中包含两个 JSON 对象）的 JSON 对象。 此输入表明复杂 JSON 对象可以表示全局属性。

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **输出**：两个事件。

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|压强|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|振动|abs G|217.09|

## <a name="next-steps"></a>后续步骤

- 在时序见解资源管理器中[查看环境](https://insights.timeseries.azure.com)。
