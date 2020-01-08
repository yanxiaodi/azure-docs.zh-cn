---
title: 了解 Azure IoT 中心设备孪生 | Microsoft Docs
description: 开发人员指南 - 使用设备孪生在 IoT 中心与设备之间同步状态和配置数据
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: f4db353e3c2f625478df6a547d1b67c5d074d18a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640615"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>了解并在 IoT 中心内使用设备孪生

设备孪生是存储设备状态信息（例如元数据、配置和条件）的 JSON 文档。 Azure IoT 中心为连接到 IoT 中心的每台设备保留一个设备孪生。 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

本文介绍：

* 设备孪生的结构：标记、所需的属性和报告的属性。
* 设备应用和后端可在设备孪生上执行的操作。

使用设备克隆可以：

* 将设备特定的元数据存储在云中。 例如，存储在自动售货机的部署位置。

* 通过设备应用报告当前状态信息，例如可用功能和条件。 例如，通过移动电话网络或 WiFi 连接到 IoT 中心的设备。

* 同步设备应用与后端应用之间的长时间运行工作流的状态。 例如，当解决方案后端指定要安装的新固件版本以及设备应用报告更新过程的各个阶段时。

* 查询设备的元数据、配置或状态。

有关使用报告的属性、设备到云的消息或文件上传的指导，请参阅[设备到云的通信指南](iot-hub-devguide-d2c-guidance.md)。

有关使用所需的属性、直接方法或云到设备的消息的指导，请参阅[云到设备的通信指南](iot-hub-devguide-c2d-guidance.md)。

## <a name="device-twins"></a>设备克隆

设备克隆存储具有以下用途的设备相关信息：

* 设备和后端可以使用这些信息来同步设备状态和配置。

* 解决方案后端可以使用这些信息来查询和定位长时间运行的操作。

设备孪生的生命周期链接到相应的[设备标识](iot-hub-devguide-identity-registry.md)。 在 IoT 中心创建或删除设备标识时，将隐式创建和删除设备孪生。

设备克隆是一个 JSON 文档，其中包含：

* **标记**。 解决方案后端可从中读取和写入数据的 JSON 文档的某个部分。 标记对设备应用不可见。

* **所需的属性**。 与报告的属性结合使用，同步设备配置或状态。 解决方案后端可设置所需的属性，并且设备应用可进行读取。 此外，当所需的属性发生更改时，设备应用可收到通知。

* **报告属性**。 与所需的属性结合使用，同步设备配置或状态。 设备应用可设置报告的属性，并且解决方案后端可进行读取和查询。

* **设备标识属性**。 设备孪生 JSON 文档的根包含[标识注册表](iot-hub-devguide-identity-registry.md)中存储的相应设备标识的只读属性。

![设备孪生属性的屏幕截图](./media/iot-hub-devguide-device-twins/twin.png)

以下示例显示了一个设备孪生 JSON 文档：

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

根对象中包含设备标识属性，以及 `tags`、`reported` 和 `desired` 属性的容器对象。 `properties` 容器包含一些只读元素（`$metadata`、`$etag` 和 `$version`），[设备孪生元数据](iot-hub-devguide-device-twins.md#device-twin-metadata)和[乐观并发](iot-hub-devguide-device-twins.md#optimistic-concurrency)部分描述了这些元素。

### <a name="reported-property-example"></a>报告属性示例

在上面的示例中，设备孪生包含设备应用报告的 `batteryLevel` 属性。 使用此属性可以根据上次报告的电池电量水平查询和操作设备。 其他示例包括让设备应用报告设备功能或连接选项。

> [!NOTE]
> 报告的属性如何简化解决方案后端获取属性最后一个已知值的方案。 如果解决方案后端需要以带时间戳的事件序列（例如时间序列）的形式处理设备遥测数据，可以使用[设备到云的消息](iot-hub-devguide-messages-d2c.md)。

### <a name="desired-property-example"></a>所需属性示例

在上面的示例中，解决方案后端和设备应用使用 `telemetryConfig` 设备孪生的所需和报告属性来同步此设备的遥测配置。 例如：

1. 解决方案后端使用所需配置值设置所需属性。 下面是包含所需属性集的文档的一部分：

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. 连接后或者首次重新连接时，设备应用会立即收到更改通知。 然后，设备应用报告更新的配置（或使用 `status` 属性报告错误状态）。 下面是报告属性的一部分：

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. 解决方案后端可以通过[查询](iot-hub-devguide-query-language.md)设备孪生，跟踪多个设备上的配置操作结果。

> [!NOTE]
> 为便于阅读，上述代码片段示例经过优化，演示了为设备配置及其状态进行编码的一种方式。 IoT 中心不会对设备孪生中的所需属性和报告属性施加特定的架构。
> 

可以使用孪生来同步长时间运行的操作，例如固件更新。 有关如何使用属性来同步和跟踪各设备中长时间运行的操作的详细信息，请参阅[使用所需的属性来配置设备](tutorial-device-twins.md)。

## <a name="back-end-operations"></a>后端操作

解决方案后端使用以下通过 HTTPS 公开的原子操作对设备孪生执行操作：

* **按 ID 检索设备孪生**。 此操作返回设备孪生文档，包括标记、所需的系统属性和报告的系统属性。

* **部分更新设备克隆**。 解决方案后端可以使用此操作部分更新设备孪生中的标记或所需属性。 部分更新以 JSON 文档的形式表示，可添加或更新任何属性。 将删除设置为 `null` 的属性。 以下示例将创建值为 `{"newProperty": "newValue"}` 的新所需属性，将现有值 `existingProperty` 覆盖为 `"otherNewValue"`，并删除 `otherOldProperty`。 不会对现有的所需属性或标记进行其他任何更改：

   ```json
   {
       "properties": {
           "desired": {
               "newProperty": {
                   "nestedProperty": "newValue"
               },
               "existingProperty": "otherNewValue",
               "otherOldProperty": null
           }
       }
   }
   ```

* **替换所需属性**。 解决方案后端可以使用此操作完全覆盖所有现有的所需属性，并使用新 JSON 文档替代 `properties/desired`。

* **替换标记**。 解决方案后端可以使用此操作完全覆盖所有现有标记，并使用新 JSON 文档替代 `tags`。

* **接收孪生通知**。 此操作允许解决方案后端在修改孪生时收到通知。 为此，IoT 解决方案需要创建一个路由，并且将“数据源”设置为等于 *twinChangeEvents*。 默认情况下没有此类路由预先存在，因此不会发送孪生通知。 如果更改速率太高，或由于其他原因（例如内部故障），IoT 中心可能会只发送一个包含所有更改的通知。 因此，如果应用程序需要可靠地审核和记录所有中间状态，则应使用设备到云消息。 孪生通知消息包括属性和正文。

  - 属性

    | 名称 | ReplTest1 |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  发送通知的时间 |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | 设备 ID |
    hubName | IoT 中心的名称 |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) 操作时间戳 |
    iothub-message-schema | deviceLifecycleNotification |
    opType | “replaceTwin”或“updateTwin” |

    消息系统属性以 `$` 符号为前缀。

  - Body
        
    本部分包括 JSON 格式的所有孪生更改。 它使用与修补程序相同的格式，不同的是它包含所有孪生节：标记、properties.reported、properties.desired，并且它包含“$metadata”元素。 例如，

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

上述所有操作均支持[乐观并发](iot-hub-devguide-device-twins.md#optimistic-concurrency)，并且需要[控制对 IoT 中心的访问](iot-hub-devguide-security.md)中定义的 **ServiceConnect** 权限。

除了上述操作以外，解决方案后端还可以：

* 使用类似于 SQL 的 [IoT 中心查询语言](iot-hub-devguide-query-language.md)查询设备孪生。

* 使用[作业](iot-hub-devguide-jobs.md)针对大型设备孪生集执行操作。

## <a name="device-operations"></a>设备操作

设备应用使用以下原子操作对设备克隆执行操作：

* **检索设备克隆**。 此操作返回当前连接的设备的设备克隆文档 (包括所需的系统属性和报告的系统属性)。 (标记对设备应用不可见。)

* **部分更新报告属性**。 使用此操作可以部分更新当前连接的设备的报告属性。 此操作使用的 JSON 更新格式与解决方案后端用于部分更新所需属性的格式相同。

* **观察所需属性**。 当前连接的设备可以选择在所需属性发生更新时接收通知。 设备收到的更新格式与解决方案后端执行的更新格式相同（部分或完全替换）。

上述所有操作都需要[控制对 IoT 中心的访问](iot-hub-devguide-security.md)中定义的 **DeviceConnect** 权限。

借助 [Azure IoT 设备 SDK](iot-hub-devguide-sdks.md)，可通过多种语言和平台轻松使用上述操作。 有关用于同步所需属性的 IoT 中心基元的详细信息，请参阅[设备重新连接流](iot-hub-devguide-device-twins.md#device-reconnection-flow)。

## <a name="tags-and-properties-format"></a>标记和属性格式

标记、所需的属性和报告的属性是具有以下限制的 JSON 对象：

* JSON 对象中的所有键是区分大小写的 64 字节 UTF-8 UNICODE 字符串。 允许的字符不包括 UNICODE 控制字符（段 C0 和 C1）以及 `.`、`$` 和 SP。

* JSON 对象中的所有值可采用以下 JSON 类型：布尔值、数字、字符串、对象。 不允许数组。 最大整数值为 4503599627370495，而最小整数值为 -4503599627370496。

* 标记、所需属性和报告属性中的所有 JSON 对象的最大嵌套深度为 5 层。 例如，以下对象是有效的：

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "property": "value"
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

* 所有字符串的值的长度最多为 512 个字节。

## <a name="device-twin-size"></a>设备克隆的大小

IoT 中心对 `tags`、`properties/desired` 和 `properties/reported`（不包括只读元素）的各个总值强制实施 8KB 大小限制。

该大小的计算考虑到了所有字符，但不包括 UNICODE 控制字符（段 C0 和 C1），以及出现在字符串常量外部的空格。

IoT 中心拒绝将这些文档的大小增加到超出限制的所有操作，在这种情况下还会返回错误。

## <a name="device-twin-metadata"></a>设备孪生的元数据

IoT 中心保留设备孪生所需属性和报告属性中每个 JSON 对象的上次更新时间戳。 时间戳采用 UTC，以 [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) 格式编码`YYYY-MM-DDTHH:MM:SS.mmmZ`。

例如：

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

将在每个级别（而不仅仅是 JSON 结构的叶级别）保留此信息，以便保留删除了对象键的更新。

## <a name="optimistic-concurrency"></a>乐观并发

标记、所需的属性和报告的属性都支持乐观并发。
标记包含一个符合 [RFC7232](https://tools.ietf.org/html/rfc7232) 规范的 ETag，它是标记的 JSON 表示形式。 可在解决方案后端上的条件更新操作中使用 ETag 来确保一致性。

设备孪生所需的属性和报告的属性不包含 ETag，但包含一个保证可递增的 `$version` 值。 更新方可以使用类似于 ETag 的版本来强制实施更新一致性。 例如，报告的属性的设备应用，或者所需的属性的解决方案后端。

当监视代理（例如，监视所需属性的设备应用）必须协调检索操作结果与更新通知之间的资源争用时，版本也很有用。 [设备重新连接流](iot-hub-devguide-device-twins.md#device-reconnection-flow)部分提供了详细信息。

## <a name="device-reconnection-flow"></a>设备重新连接流

IoT 中心不会保留已断开连接设备的所需属性更新通知。 它遵循的原则是：连接的设备必须检索整个所需属性文档，此外还要订阅更新通知。 如果更新通知与完全检索之间存在资源争用的可能性，则必须确保遵循以下流：

1. 设备应用连接到 IoT 中心。
2. 设备应用订阅所需属性更新通知。
3. 设备应用检索所需属性的完整文档。

设备应用可以忽略 `$version` 小于或等于完全检索文档的版本的所有通知。 之所以能够使用此方法，是因为 IoT 中心保证版本始终是递增的。

> [!NOTE]
> 此逻辑已在 [Azure IoT 设备 SDK](iot-hub-devguide-sdks.md) 中实现。 仅当设备应用无法使用任何 Azure IoT 设备 SDK，必须直接为 MQTT 接口编程时，这段说明才有作用。
> 

## <a name="additional-reference-material"></a>其他参考资料

IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点](iot-hub-devguide-endpoints.md)一文介绍了每个 IoT 中心针对运行时和管理操作公开的各种终结点。

* [限制和配额](iot-hub-devguide-quotas-throttling.md)一文介绍了适用于 IoT 中心服务的配额，以及使用服务时应会碰到的限制行为。

* [Azure IoT 设备和服务 SDK](iot-hub-devguide-sdks.md) 一文列出了开发与 IoT 中心交互的设备和服务应用时可使用的各种语言 SDK。

* [设备孪生、作业和消息路由的 IoT 中心查询语言](iot-hub-devguide-query-language.md)一文介绍了可用于从 IoT 中心检索设备孪生和作业相关信息的 IoT 中心查询语言。

* [IoT 中心 MQTT 支持](iot-hub-mqtt-support.md)一文提供有关 IoT 中心对 MQTT 协议的支持的详细信息。

## <a name="next-steps"></a>后续步骤

了解设备孪生后，可以根据兴趣参阅以下 IoT 中心开发人员指南主题：

* [在 IoT 中心内了解并使用模块孪生](iot-hub-devguide-module-twins.md)
* [在设备上调用直接方法](iot-hub-devguide-direct-methods.md)
* [在多个设备上计划作业](iot-hub-devguide-jobs.md)

要尝试本文中介绍的一些概念，请参阅以下 IoT 中心教程：

* [如何使用设备孪生](iot-hub-node-node-twin-getstarted.md)
* [如何使用设备孪生属性](tutorial-device-twins.md)
* [使用适用于 VS Code 的 Azure IoT 工具进行设备管理](iot-hub-device-management-iot-toolkit.md)
