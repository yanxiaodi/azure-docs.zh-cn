---
title: 从命令行创建自动部署 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure CLI 的 IoT 扩展为 IoT Edge 设备组创建自动部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 2601d05c5d2302bedb51e959747939aa3c33db44
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839630"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>使用 Azure CLI 大规模部署并监视 IoT Edge 模块

使用 Azure 命令行接口创建“IoT Edge 自动部署”，以便同时管理多个设备的正在进行的部署。 IoT Edge 的自动部署属于 IoT 中心的[自动设备管理](/azure/iot-hub/iot-hub-automatic-device-management)功能。 部署是动态的过程，允许将多个模块部署到多台设备，跟踪这些模块的状态和运行状况，以及在必要时进行更改。 

有关详细信息，请参阅[了解单个设备或大规模的 IoT Edge 自动部署](module-deployment-monitoring.md)。

在本文中，将安装 Azure CLI 和 IoT 扩展。 然后，了解如何使用可用的 CLI 命令将模块部署到一组 IoT Edge 设备并监视进度。

## <a name="cli-prerequisites"></a>CLI 先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device-cli.md)。
* 环境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 版本必须至少是 2.0.24 或更高版本。 请使用 `az --version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 有关详细信息，请参阅[了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)。

若要使用 Azure CLI 来部署模块，请将部署清单在本地另存为 .txt 文件。 在下一部分中，当运行命令以将配置应用到设备时，会用到该文件路径。 

下面是一个基本的部署清单示例，其中有一个模块：

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
              }
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "route": "FROM /* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {}
      }
    }
  }
}
```

## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建部署之前，必须能够指定想要影响的设备。 Azure IoT Edge 标识使用设备孪生中的标记标识设备。 每个设备都可以具有多个标记，你可以采用适合你的解决方案的任何方式定义这些标记。 例如，如果管理有智能楼宇的校园，可将以下标记添加到设备：

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

有关设备孪生和标记的详细信息，请参阅[了解和使用 IoT 中心的设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。

## <a name="create-a-deployment"></a>创建部署

请创建一个包含部署清单和其他参数的部署，以便将模块部署到目标设备。 

使用[az iot edge deployment create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create)命令创建部署:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

部署创建命令采用以下参数： 

* **--deployment-id** - 将在 IoT 中心创建的部署的名称。 为部署提供唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。
* **--hub-name** - 将在其中创建部署的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令更改当前订阅。
* **--content** - 部署清单 JSON 的文件路径。 
* **--labels** - 添加用于跟踪部署的标签。 标签是描述部署的“名称, 值”对。 标签对名称和值采用 JSON 格式设置。 例如： `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - 输入一个目标条件，用于确定哪些设备会成为此部署的目标。 该条件基于设备孪生标记或设备孪生报告的属性，应与表达式格式相匹配。 例如， `tags.environment='test' and properties.reported.devicemodel='4000x'` 。 
* **--priority** - 一个正整数。 如果同一设备上确定的部署目标至少有两个，则会应用优先级数值最高的部署。

## <a name="monitor-a-deployment"></a>监视部署

使用[az iot edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show)命令显示单个部署的详细信息:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

部署显示命令采用以下参数：
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

在命令窗口中检查部署。  **metrics** 属性列出由每个中心评估的每个指标的计数：

* **targetedCount** - 一个系统指标，根据目标条件指定 IoT 中心的设备孪生数。
* **appliedCount** - 一个系统指标，指定已在 IoT 中心将部署内容应用到其模块孪生的设备数。
* **reportedSuccessfulCount** - 一个设备指标，用于指定通过 IoT Edge 客户端运行时报告成功的部署中的 IoT Edge 设备数。
* **reportedFailedCount** - 一个设备指标，用于指定通过 IoT Edge 客户端运行时报告失败的部署中的 IoT Edge 设备数。

你可以使用[az iot edge deployment show-公制](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric)命令显示每个指标的设备 id 或对象的列表:

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

部署显示指标命令采用以下参数： 
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--metric-id** - 需要查看设备 ID 列表时所对应指标的名称，例如 `reportedFailedCount`
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

## <a name="modify-a-deployment"></a>修改部署

修改部署时，更改会立即复制到所有目标设备。 

如果更新目标条件，将发生以下更新：

* 如果设备虽然不满足旧的目标条件，但满足新的目标条件，且此部署是此设备的最高优先级，则此部署将应用到设备。 
* 如果当前运行此部署的设备不再满足目标条件，则它将卸载此部署并采用下一个最高优先级部署。 
* 如果当前运行此部署的设备不再满足目标条件且不满足任何其他部署的目标条件，则此设备上不会发生任何更改。 设备在当前状态下继续运行当前模块，但不再作为此部署的一部分被托管。 一旦它满足任何其他部署的目标条件，将卸载此部署并采用新的部署。 

使用[az iot edge deployment update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update)命令更新部署:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

部署更新命令采用以下参数：
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅
* **--set** - 更新部署中的属性。 可以更新以下属性：
  * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`
  * 标签 
  * 优先级


## <a name="delete-a-deployment"></a>删除部署

删除部署时，任何设备都将采用下一个最高优先级部署。 如果设备不满足任何其他部署的目标条件，则删除该部署时不会删除模块。 

使用[az iot edge deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete)命令删除部署:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

部署删除命令采用以下参数： 
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

## <a name="next-steps"></a>后续步骤

详细了解[将模块部署到 IoT Edge 设备](module-deployment-monitoring.md)。
