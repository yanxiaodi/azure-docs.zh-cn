---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/09/2018
ms.author: estfan
ms.openlocfilehash: 3fa71085d649ace95aa24ac87c8714a7268f5386
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173543"
---
若要估计更准确的使用成本，请考虑任何给定天可能到达的消息或事件数，而不是仅基于轮询间隔进行计算。 当某个事件或消息满足触发器条件时，许多触发器将立即尝试读取满足条件的任何和所有其他等待事件或消息。 此行为意味着，即使你选择较长的轮询间隔，触发器也基于符合启动工作流条件的等待事件或消息的数量进行触发。 遵循此行为的触发器包括 Azure 服务总线和 Azure 事件中心。

因此，例如，假设你设置了一个每天检查终结点的触发器。 当触发器检查终结点并找到 15 个满足条件的事件时，触发器触发并运行相应工作流 15 次。 逻辑应用会计量这 15 个工作流执行的所有操作，包括触发器请求。 若要计算潜在成本，请尝试使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。