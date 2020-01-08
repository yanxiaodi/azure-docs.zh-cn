---
title: Azure IoT 中心缩放 | Microsoft Docs
description: 如何缩放 IoT 中心来支持预期的消息吞吐量和所需的功能。 概括介绍了每层支持的吞吐量和分片选项。
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: 8d7bb201a9d01725f933105a4a0beb85c82ca105
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203710"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>选择适用于解决方案的 IoT 中心层

每个 IoT 解决方案都是不同的，因此 Azure IoT 中心会根据定价和缩放提供多个选项。 本文旨在介绍如何评估 IoT 中心需求。 有关 IoT 中心层的定价信息，请参阅 [IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub)。

若要确定哪个 IoT 中心层适用于解决方案，请向自己提问两个问题：

**我计划使用哪些功能？**

Azure IoT 中心提供两个层，即基本层和标准层，这两个层在所支持的功能数目上有所不同。 如果 IoT 解决方案需要先从设备收集数据，然后再集中进行分析，则可能适合使用基本层。 如果需要使用更高级的配置来远程控制 IoT 设备，或者需要将某些工作负荷分发到设备本身，则应考虑标准层。 若要详细了解每一层中包括哪些功能，请转到[基本层和标准层](#basic-and-standard-tiers)。

**我计划每天移动多少数据？**

每个 IoT 中心层提供三种大小，具体取决于每天能够处理的数据吞吐量。 这些大小以数字的形式标为 1、2、3。 例如，1 级 IoT 中心的每个单元每天可以处理 40 万条消息，而 3 级单元则可以处理 3 亿条消息。 有关数据准则的更多详细信息，请转到[消息吞吐量](#message-throughput)。

## <a name="basic-and-standard-tiers"></a>基本层和标准层

IoT 中心的标准层启用了所有功能，是任何需要使用双向通信功能的 IoT 解决方案所必需的。 基本层启用了部分功能，适用于只需单向通信（从设备到云）的 IoT 解决方案。 这两个层提供相同的安全性和身份验证功能。

每个 IoT 中心在每个层内只能选择一种类型的[版本](https://azure.microsoft.com/pricing/details/iot-hub/)。 例如，可以创建具有多个 S1 单位的 IoT 中心，而不是使用不同版本的单元（例如 S1 和 S2）。

| 功能 | 基本层 | 免费/标准层 |
| ---------- | ---------- | ------------- |
| [设备到云的遥测](iot-hub-devguide-messaging.md) | 是 | 是 |
| [每设备标识](iot-hub-devguide-identity-registry.md) | 是 | 是 |
| [消息路由](iot-hub-devguide-messages-read-custom.md)和[事件网格集成](iot-hub-event-grid.md) | 是 | 是 |
| [HTTP、AMQP 和 MQTT 协议](iot-hub-devguide-protocols.md) | 是 | 是 |
| [设备预配服务](../iot-dps/about-iot-dps.md) | 是 | 是 |
| [监视和诊断](iot-hub-monitor-resource-health.md) | 是 | 是 |
| [云到设备的消息传递](iot-hub-devguide-c2d-guidance.md) |   | 是 |
| [设备孪生](iot-hub-devguide-device-twins.md)、[模块孪生](iot-hub-devguide-module-twins.md)和[设备管理](iot-hub-device-management-overview.md) |   | 是 |
| [设备流（预览版）](iot-hub-device-streams-overview.md) |   | 是 |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | 是 |
| [IoT 即插即用预览](../iot-pnp/overview-iot-plug-and-play.md) |   | 是 |

IoT 中心还提供一个免费层，用于测试和评估。 它具有标准层的所有功能，但消息传递有限额。 不能从免费层升级到基本层或标准层。

## <a name="partitions"></a>分区

Azure IoT 中心包含 [Azure 事件中心](../event-hubs/event-hubs-features.md)的许多核心组件，包括[分区](../event-hubs/event-hubs-features.md#partitions)。 IoT 中心的事件流通常由各种 IoT 设备报告的传入遥测数据进行填充。 事件流的分区功能用来减少当事件流有并发的读取和写入时发生的连接。

分区限制是在创建 IoT 中心时选择的，并且无法更改。 基本层 IoT 中心和标准层 IoT 中心的最大分区限制为 32。 大多数 IoT 中心只需要 4 个分区。 有关确定分区的详细信息，请参阅事件中心常见问题解答[我需要多少分区？](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>层升级

创建 IoT 中心以后，即可从基本层升级到标准层，不需中断现有的操作。 有关详细信息，请参阅[如何升级 IoT 中心](iot-hub-upgrade.md)。

从基本层迁移到标准层时，分配配置保持不变。

> [!NOTE]
> 免费层不支持升级到基本层或标准层。

## <a name="iot-hub-rest-apis"></a>IoT 中心 REST API

IoT 中心基本层和标准层所支持的功能存在差异，也就是说，某些 API 调用在基本层中心不适用。 下表显示了哪些 API 可用：

| API | 基本层 | 免费/标准层 |
| --- | ---------- | ------------- |
| [删除设备](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | 是 | 是 |
| [获取设备](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | 是 | 是 |
| [删除模块](https://docs.microsoft.com/rest/api/iothub/service/deletemodule) | 是 | 是 |
| [获取模块](https://docs.microsoft.com/rest/api/iothub/service/getmodule) | 是 | 是 |
| [获取注册表统计信息](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | 是 | 是 |
| [获取服务统计信息](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | 是 | 是 |
| [创建或更新设备](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | 是 | 是 |
| [创建或更新模块](https://docs.microsoft.com/rest/api/iothub/service/createorupdatemodule) | 是 | 是 |
| [查询 IoT 中心](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | 是 | 是 |
| [创建文件上传 SAS URI](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | 是 | 是 |
| [接收发往设备的通知](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | 是 | 是 |
| [发送设备事件](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | 是 | 是 |
| 发送模块事件 | 仅限 AMQP 和 MQTT | 仅限 AMQP 和 MQTT |
| [更新文件上传状态](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | 是 | 是 |
| [批量设备操作](https://docs.microsoft.com/rest/api/iothub/service/bulkcreateorupdatedevices) | 是的，IoT Edge 功能除外 | 是 |
| [取消导入导出作业](https://docs.microsoft.com/rest/api/iothub/service/cancelimportexportjob) | 是 | 是 |
| [创建导入导出作业](https://docs.microsoft.com/rest/api/iothub/service/createimportexportjob) | 是 | 是 |
| [获取导入导出作业](https://docs.microsoft.com/rest/api/iothub/service/getimportexportjob) | 是 | 是 |
| [获取导入导出作业](https://docs.microsoft.com/rest/api/iothub/service/getimportexportjobs) | 是 | 是 |
| [清除命令队列](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | 是 |
| [获取设备孪生](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | 是 |
| [获取模块孪生](https://docs.microsoft.com/rest/api/iothub/service/getmoduletwin) |   | 是 |
| [调用设备方法](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | 是 |
| [更新设备孪生](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | 是 |
| [更新模块孪生](https://docs.microsoft.com/rest/api/iothub/service/updatemoduletwin) |   | 是 |
| [放弃发往设备的通知](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | 是 |
| [完成发往设备的通知](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | 是 |
| [取消作业](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | 是 |
| [创建作业](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | 是 |
| [获取作业](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | 是 |
| [查询作业](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | 是 |

## <a name="message-throughput"></a>消息吞吐量

若要基于每个单位评估流量，最佳方式是调整 IoT 中心解决方案的大小。 尤其要考虑以下类别的操作所需的高峰吞吐量：

* 设备到云的消息
* 云到设备的消息
* 标识注册表操作

每个单位为 IoT 中心衡量流量。 创建 IoT 中心时，选择其层和版本，并设置可用的单位数。 对于 B1、B2、S1 或 S2 版，最多可购买200单位，对于 B3 或 S3 edition，最多可购买10个单位。 创建 IoT 中心后，可以更改其版本中可用的单位数，在其层中的各版本之间进行升级或降级（B1 到 B2），或从基本层升级到标准层（B1 到 S1），而不会中断现有的操作。 有关详细信息，请参阅[如何升级 IoT 中心](iot-hub-upgrade.md)。  

例如，就每个层的流量功能来说，设备到云的消息遵循以下持续吞吐量指导原则：

| 层版本 | 持续吞吐量 | 持续发送速率 |
| --- | --- | --- |
| B1、S1 |每个单元最多 1111 KB/分钟<br/>（1.5 GB/天/单元） |每个单元平均 278 条消息/分钟<br/>（400000 条消息/天/单元） |
| B2、S2 |每个单元最多 16 MB/分钟<br/>（22.8 GB/天/单元） |每个单元平均 4,167 条消息/分钟<br/>（600 万条消息/天/单元） |
| B3、S3 |每个单元最多 814 MB/分钟<br/>（1144.4 GB/天/单元） |每个单元平均 208,333 条消息/分钟<br/>（3 亿条消息/天/单元） |

从设备到云的吞吐量只是设计 IoT 解决方案时需要考虑的一个指标。 有关更全面的信息，请参阅[IoT 中心配额和限制](iot-hub-devguide-quotas-throttling.md)。

### <a name="identity-registry-operation-throughput"></a>标识注册表操作吞吐量

由于大多数 IoT 中心标识注册表操作都与设备预配相关，因此不认为这些操作是运行时操作。

有关特定脉冲性能数字，请参阅 [IoT 中心配额和限制](iot-hub-devguide-quotas-throttling.md)。

## <a name="auto-scale"></a>自动缩放

如果你接近 IoT 中心允许的消息限制，则可以使用这些[步骤自动缩放](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/)，以便在同一 iot 中心层中递增 IoT 中心单元。

## <a name="next-steps"></a>后续步骤

* 有关 IoT 中心功能和性能详细信息的详细信息，请参阅[Iot 中心定价](https://azure.microsoft.com/pricing/details/iot-hub)或[iot 中心配额和限制](iot-hub-devguide-quotas-throttling.md)。

* 若要更改 IoT 中心层，请执行[升级 IoT 中心](iot-hub-upgrade.md)中的步骤。
