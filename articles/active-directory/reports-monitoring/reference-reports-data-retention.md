---
title: Azure AD 存储报告数据有多长时间？ | Microsoft Docs
description: 了解 Azure 存储各种类型的报表数据的时间长度。
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: c52f8873527d92e621ef032f5bc3e82d3364a691
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989585"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Azure AD 存储报告数据有多长时间？

本文介绍 Azure Active Directory 中不同活动报告的数据保留策略。 

### <a name="when-does-azure-ad-start-collecting-data"></a>Azure AD 何时开始收集数据？

| Azure AD 版本 | 开始收集 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | 注册订阅时 |
| Azure AD 免费版 <br /> Azure AD 基本版 | 首次打开 [Azure Active Directory 边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)或使用[报告 API](https://aka.ms/aadreports) 时  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>活动数据何时出现在 Azure 门户中？

- **立即** - 如果已在 Azure 门户中使用报告。
- **2 小时内** - 如果尚未在 Azure 门户中启用报告。

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>获得高级许可证后多久可看见活动数据？

如果已经拥有免费许可证的活动数据，则可在升级时立即看到这些数据。 升级到高级许可证后，如果没有任何数据，则需要在一到两天后，数据才会显示在报告中。

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>获得 Azure AD Premium 许可证后是否能查看上个月的数据？

如果最近刚切换到高级版本（包括试用版），则最初最多能看到 7 天的数据。 随着数据累积，可以看到过去 30 天的数据。

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Azure AD 何时开始收集安全信号数据？  

当用户选择使用**标识防护中心**时，收集安全信号的过程会开始。 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Azure AD 存储数据多长时间？

**活动报告**    

| 报告                 | Azure AD 免费版 | Azure AD 基本版 | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| 审核日志             | 7 天        |  7 天        | 30 天             | 30 天             |
| 登录               | 不可用           |  不可用           | 30 天             | 30 天             |
| Azure MFA 使用情况        | 30 天       |  30 天       | 30 天             | 30 天             |

通过使用 Azure Monitor 将审核和登录活动数据路由到 Azure 存储帐户，可以使这些活动数据的保留时间长于上述默认保留期限。 有关详细信息，请参阅[将 Azure AD 日志存档到 Azure 存储帐户](quickstart-azure-monitor-route-logs-to-storage-account.md)。

**安全信号**

| 报告         | Azure AD 免费版 | Azure AD 基本版 | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| 有风险的用户  | 7 天        | 7 天         | 30 天             | 90 天             |
| 有风险的登录 | 7 天        | 7 天         |  30 天            | 90 天             |

---
