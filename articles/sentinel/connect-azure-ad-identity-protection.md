---
title: 将 Azure AD Identity Protection 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Azure AD Identity Protection 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: de8f31a02b62164a8a6b099a90297a207aefae80
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240800"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>连接 Azure AD Identity Protection 的数据



可以将日志从[Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip)流式传输到 azure sentinel，以将警报流式传输到 azure sentinel，以查看仪表板、创建自定义警报和改进调查。 Azure Active Directory Identity Protection 提供了风险用户、风险检测和漏洞的综合视图，能够立即补救风险，并设置策略以自动修正将来的事件。 此服务构建在 Microsoft 的体验上，可保护使用者标识，并从超过13000000000的一天的信号中获得极大的准确性。 


## <a name="prerequisites"></a>先决条件

- 你必须具有[Azure Active Directory Premium P1 或 P2 许可证](https://azure.microsoft.com/pricing/details/active-directory/)
- 具有全局管理员或安全管理员权限的用户


## <a name="connect-to-azure-ad-identity-protection"></a>连接到 Azure AD Identity Protection

如果已有 Azure AD Identity Protection，请确保已[在网络上启用](../active-directory/identity-protection/enable.md)它。
如果 Azure AD Identity Protection 已部署并获取数据，则可以轻松地将警报数据流式传输到 Azure Sentinel。


1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后单击 " **Azure AD Identity Protection** " 磁贴。

2. 单击 "**连接**" 开始将 Azure AD Identity Protection 事件流式传输到 Azure Sentinel。


6. 若要在 Azure AD Identity Protection 警报 Log Analytics 中使用相关架构，请搜索**IdentityProtectionLogs_CL**。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure AD Identity Protection 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
