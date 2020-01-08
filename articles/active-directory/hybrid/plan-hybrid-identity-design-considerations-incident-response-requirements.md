---
title: 混合标识设计 - 事件响应要求 Azure | Microsoft Docs
description: 确定混合标识解决方案的监视和报告功能，IT 部门可以利用这些功能采取措施来识别和缓解潜在威胁
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52b5e37c29e4b3df3f171f683266b5d0a3e0c95d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109272"
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>确定混合标识解决方案的事件响应要求
大型或中型组织很可能会部署[安全事件响应](https://technet.microsoft.com/library/cc700825.aspx)，帮助 IT 部门根据事件级别采取相应措施。 标识管理系统是事件响应过程的重要组成部分，因为可以使用它来帮助识别针对目标执行具体操作的用户。 混合标识解决方案必须能够提供监视和报告功能，IT 部门可以利用这些功能采取措施来识别和缓解潜在威胁。 在典型的事件响应计划中将包含以下几个阶段：

1. 初始评估。
2. 事件通信。
3. 损害控制和风险降低。
4. 查明危害级别和严重级别。
5. 证据保留。
6. 通知相应部门。
7. 系统恢复。
8. 文档。
9. 损失和成本评估。
10. 过程和计划修订。

在查明危害级别和严重级别阶段，有必要标识已受到危害的系统、已遭访问的文件并确定这些文件的敏感性。 混合标识系统应能够满足这些要求，以帮助你确定进行这些更改的用户。 

## <a name="monitoring-and-reporting"></a>监视和报告
大体上，如果系统内置了审核和报告功能，标识系统还可以多次在初始评估阶段提供帮助。 在初始评估期间，IT 管理员必须能够确定可疑活动，或者该系统应能够基于预配置的任务自动触发该活动。 许多活动可能表明存在潜在攻击，但在其他情况下，配置不当的系统可能会导致入侵检测系统中出现大量误报。 

标识管理系统应帮助 IT 管理员识别并报告这些可疑活动。 通常可以通过监视所有系统并提供可以突出显示潜在威胁的报告功能，来满足这些技术要求。 以下问题用于帮助你设计混合标识解决方案，同时考虑事件响应要求：

* 贵公司是否部署了安全事件响应？
  * 如果是，则当前的标识管理系统是否作为该过程的一部分进行使用？
* 贵公司是否需要识别来自不同设备的用户的可疑登录尝试？
* 贵公司是否需要检测可能受威胁的用户的凭据？
* 贵公司是否需要审核用户的访问和操作？
* 你的公司是否需要了解当用户重置其密码？

## <a name="policy-enforcement"></a>策略强制执行
在损害控制和风险降低阶段，务必快速消减攻击的实际和潜在影响。 此时会采取的措施可以决定是受轻微影响，还是受重大影响。 具体响应将取决于组织和你面对的攻击的性质。 如果初始评估得出的结论是某个帐户已受到威胁，将需要强制执行阻止该帐户的策略。 这只是一个示例，其中将用到标识管理系统。 以下问题用于帮助你设计混合标识解决方案，同时考虑将如何强制执行策略来对正在进行的事件作出反应：

* 贵公司是否部署了策略来阻止用户访问网络（如有必要）？
  * 如果是，则当前解决方案是否将与要采用的混合标识管理系统集成？
* 贵公司是否需要以强制实施隔离区中的用户的条件性访问？ 

> [!NOTE]
> 务必记下每个答案并了解答案背后的依据。 [定义数据保护策略](plan-hybrid-identity-design-considerations-data-protection-strategy.md)会检查可用选项以及每个选项的优点/缺点。  回答了这些问题之后，就会挑选出最适合业务需求的选项。
> 
> 

## <a name="next-steps"></a>后续步骤
[定义数据保护策略](plan-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>另请参阅
[设计注意事项概述](plan-hybrid-identity-design-considerations-overview.md)

