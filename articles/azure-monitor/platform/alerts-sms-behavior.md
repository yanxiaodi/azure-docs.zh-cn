---
title: 操作组中的短信警报行为
description: 短信格式，以及回复短信以取消订阅、重新订阅或请求帮助。
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 74666149824627308b6c5b026e0c9ba7a7750ada
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60346291"
---
# <a name="sms-alert-behavior-in-action-groups"></a>操作组中的短信通知行为
## <a name="overview"></a>概述 ##
通过操作组可以配置操作列表。 在定义警报时将使用这些组；确保警报被触发时向特定操作组发送通知。 支持的操作之一是短信；短信通知支持双向通信。 用户可以对短信做出响应来执行以下操作：

- **取消订阅警报：** 用户可以为所有操作组或单个操作组取消订阅所有短信警报。
- **重新订阅警报：** 用户可以为所有操作组或单个操作组重新订阅所有短信警报。  
- **请求帮助：** 用户可以询问有关短信的详细信息。 用户会被重定向到本文。

本文介绍短信警报的行为，以及用户根据其区域设置可采取的响应操作：

## <a name="receiving-an-sms-alert"></a>接收短信警报
警报被触发时，配置为操作组的一部分的短信接收方将收到短信。 短信包含以下信息：
* 此警报发送到的操作组的短名称
* 警报的标题

| 回复 | 描述 |
| ----- | ----------- |
| DISABLE `<Action Group Short name>` | 禁用来自操作组的进一步短信 |
| ENABLE `<Action Group Short name>` | 重新启用来自操作组的短信 |
| STOP | 禁用来自所有操作组的进一步短信 |
| START | 重新启用来自所有操作组的短信 |
| HELP | 将向用户发送带本文链接的回复信息。 |

>[!NOTE]
>如果用户已取消订阅短信警报，但随后被添加到新的操作组，将接收新操作组的短信警报，但对于所有以前的操作组均保持取消订阅状态。

## <a name="next-steps"></a>后续步骤
获取[活动日志警报概述](alerts-overview.md)，了解如何接收警报  
了解有关[短信速率限制](alerts-rate-limiting.md)的详细信息  
详细了解[操作组](../../azure-monitor/platform/action-groups.md)

