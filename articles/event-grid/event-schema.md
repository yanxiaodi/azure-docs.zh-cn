---
title: Azure 事件网格事件架构
description: 介绍为 Azure 事件网格中事件所提供的属性
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/20/2019
ms.author: babanisa
ms.openlocfilehash: 8a8193d21bbc1d0af933657705e605ce31589cbf
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785854"
---
# <a name="azure-event-grid-event-schema"></a>Azure 事件网格事件架构

本文介绍为所有事件提供的属性和架构。 事件由 5 个所需的字符串属性和 1 个 所需的数据对象构成。 这些属性在任何发布服务器的所有事件中通用。 数据对象具有特定于每个发布者的属性。 对于系统主题，这些属性特定于资源提供程序，例如 Azure 存储或 Azure 事件中心。

事件源会将事件发送到数组中的 Azure 事件网格（其中可包含多个事件对象）。 将事件发布到事件网格主题时，数组的总大小最大可为 1 MB。 数组中的每个事件被限制为 64 KB （正式版） 或 1 MB （预览版）。 事件或数组超出大小限制时会收到响应“413 有效负载太大”  。

> [!NOTE]
> 事件的大小高达 64 KB 介绍了通过正式版 (GA) 服务级别协议 (SLA)。 大小最多的事件的支持 1 MB 目前处于预览状态。 超过 64 KB 的事件是按 64 KB 的增量计费。 

事件网格会将事件发送给具有单个事件的数组中的订阅者。 此行为在将来可能会更改。

可以在[事件架构存储](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane)中找到事件网格事件的 JSON 架构和每个 Azure 发布服务器的数据负载。

## <a name="event-schema"></a>事件架构

以下示例显示所有事件发布者使用的属性：

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

例如，为 Azure Blob 存储事件发布的架构是：

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>事件属性

所有事件均具有以下相同的顶级数据：

| 属性 | 类型 | 描述 |
| -------- | ---- | ----------- |
| topic | string | 事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。 |
| subject | string | 事件主题的发布者定义路径。 |
| eventType | string | 此事件源的一个注册事件类型。 |
| eventTime | string | 基于提供程序 UTC 时间的事件生成时间。 |
| id | string | 事件的唯一标识符。 |
| data | object | 特定于资源提供程序的事件数据。 |
| dataVersion | string | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | string | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

若要了解数据对象中的属性，请参阅事件源：

* [Azure 订阅（管理操作）](event-schema-subscriptions.md)
* [容器注册表](event-schema-container-registry.md)
* [Blob 存储](event-schema-blob-storage.md)
* [事件中心](event-schema-event-hubs.md)
* [IoT 中心](event-schema-iot-hub.md)
* [媒体服务](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [资源组（管理操作）](event-schema-resource-groups.md)
* [服务总线](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)

对于自定义主题，事件发布者确定数据对象。 顶级数据应具有与标准资源所定义事件相同的字段。

将事件发布到自定义主题时，可为事件创建主题，便于订阅者们了解他们是否对该事件感兴趣。 订阅者使用主题来筛选和路由事件。 请考虑为事件发生的位置提供路径，以便订阅者可根据该路径的片段进行筛选。 通过路径，订阅者可精确或宽泛地筛选事件。 例如，如果在主题中提供一个由三个片段构成的路径（如 `/A/B/C`），订阅者可根据第一个片段 `/A` 进行筛选，获取范围较宽泛的一组事件。 这些订阅者会获取主题为 `/A/B/C` 或 `/A/D/E` 这样的事件。 其他订阅者可通过 `/A/B` 进行筛选，这样可以获取范围更精确的一组事件。

有时，需要提供有关发生事件更详细的信息才能查找到所需主题。 例如，将文件添加到容器时，“存储帐户”发布服务器提供主题 `/blobServices/default/containers/<container-name>/blobs/<file>`  。 订阅者可以按路径 `/blobServices/default/containers/testcontainer` 进行筛选，获取有关该容器而非存储帐户中其他容器的所有事件。 订阅者还可通过使用后缀 `.txt` 进行筛选或路由，来达到仅处理文本文件的目的。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](overview.md)
* 有关创建 Azure 事件网格订阅的详细信息，请参阅[事件网格订阅架构](subscription-creation-schema.md)。
