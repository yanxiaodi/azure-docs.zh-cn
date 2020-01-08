---
title: Azure Service Fabric CLI - sfctl rpm | Microsoft Docs
description: 介绍 Service Fabric CLI sfctl rpm 命令。
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
ms.openlocfilehash: 3f40451087aba5af5b02625ad3ac1ca6231d976c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035872"
---
# <a name="sfctl-rpm"></a>sfctl rpm
查询并向修复管理器服务发送命令。

## <a name="commands"></a>命令

|Command|描述|
| --- | --- |
| approve-force | 强制批准给定的修复任务。 |
| 删除 | 删除已完成的修复任务。 |
| 列表 | 获取与给定筛选器匹配的修复任务列表。 |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force
强制批准给定的修复任务。

此 API 支持 Service Fabric 平台；不应从代码直接使用它。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --task-id [必需] | 修复任务的 ID。 |
| --version | 修复任务的当前版本号。 如果值为非零，则仅当此值与修复任务的实际当前版本匹配时，请求才会成功。 如果值为零，则不执行版本检查。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。 |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
删除已完成的修复任务。

此 API 支持 Service Fabric 平台；不应从代码直接使用它。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --task-id [必需] | 要删除的已完成修复任务的 ID。 |
| --version | 修复任务的当前版本号。 如果值为非零，则仅当此值与修复任务的实际当前版本匹配时，请求才会成功。 如果值为零，则不执行版本检查。 |

### <a name="global-arguments"></a>全局参数

|参数|描述|
| --- | --- |
| --debug | 提高日志记录详细程度，以显示所有调试日志。 |
| --help -h | 显示此帮助消息并退出。 |
| --output -o | 输出格式。  允许的值\: json、jsonc、table、tsv。  默认值\: json。 |
| --query | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http\://jmespath.org/。 |
| --verbose | 提高日志记录详细程度。 使用 --debug 可获取完整调试日志。 |

## <a name="sfctl-rpm-list"></a>sfctl rpm list
获取与给定筛选器匹配的修复任务列表。

此 API 支持 Service Fabric 平台；不应从代码直接使用它。

### <a name="arguments"></a>参数

|参数|描述|
| --- | --- |
| --executor-filter | 应在列表中包含其声明任务的修复执行程序的名称。 |
| --state-filter | 以下值的按位 OR 运算，指定应在结果列表中包含哪些任务状态。 <br> 1 - 已创建 <br>2 - 已声明  <br>4 - 正在准备  <br>8 - 已批准  <br>16 - 正在执行  <br>32 - 正在还原  <br>64 - 已完成 |
| --task-id-filter | 要匹配的修复任务 ID 前缀。 |

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