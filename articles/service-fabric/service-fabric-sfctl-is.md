---
title: Azure Service Fabric CLI- sfctl is | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl is 命令。
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 998a94350250402d4face64f64e12f32cf5b2a36
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036485"
---
# <a name="sfctl-is"></a>sfctl is
查询并向基础结构服务发送命令。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| command | 针对给定基础结构服务实例调用管理命令。 |
| query | 针对给定基础结构服务实例调用只读查询。 |

## <a name="sfctl-is-command"></a>sfctl is command
针对给定基础结构服务实例调用管理命令。

对于配置了一个或多个基础结构服务实例的群集，使用此 API 可向特定基础结构服务实例发送特定于基础结构的命令。 可用命令及其相应的响应格式因运行群集的基础结构而异。 此 API 支持 Service Fabric 平台；不应从代码直接使用它。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --command [必需] | 将调用的命令文本。 命令内容特定于基础结构。 |
| --service-id | 基础结构服务标识。 <br><br> 这是不包含“fabric\:”URI 方案的基础结构服务全名。 只有运行多个基础结构服务实例的群集才需要此参数。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。 |

## <a name="sfctl-is-query"></a>sfctl is query
针对给定基础结构服务实例调用只读查询。

对于配置了一个或多个基础结构服务实例的群集，使用此 API 可向特定基础结构服务实例发送特定于基础结构的查询。 可用命令及其相应的响应格式因运行群集的基础结构而异。 此 API 支持 Service Fabric 平台；不应从代码直接使用它。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --command [必需] | 将调用的命令文本。 命令内容特定于基础结构。 |
| --service-id | 基础结构服务标识。 <br><br> 这是不包含“fabric\:”URI 方案的基础结构服务全名。 只有运行多个基础结构服务实例的群集才需要此参数。 |
| --timeout -t | 服务器超时，以秒为单位。  默认值\: 60。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。 |


## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。