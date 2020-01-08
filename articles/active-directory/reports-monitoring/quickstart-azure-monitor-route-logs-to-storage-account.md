---
title: 教程 - 将 Azure Active Directory 日志存档到存储帐户 | Microsoft Docs
description: 了解如何设置 Azure 诊断来将 Azure Active Directory 日志推送到存储帐户
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d98fb0677b864fccfb5abd2b08381db1bd1c9c8f
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989735"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>教程：将 Azure AD 日志存档到 Azure 存储帐户

本教程介绍如何设置 Azure Monitor 诊断设置，以便将 Azure Active Directory (Azure AD) 日志路由到 Azure 存储帐户。

## <a name="prerequisites"></a>先决条件 

若要使用此功能，需满足以下条件:

* 一个具有 Azure 存储帐户的 Azure 订阅。 如果没有 Azure 订阅，可以[注册免费试用版](https://azure.microsoft.com/free/)。
* Azure AD 租户。
* 一个是 Azure AD 租户的全局管理员或安全管理员的用户。  

## <a name="archive-logs-to-an-azure-storage-account"></a>将日志存档到 Azure 存储帐户

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 选择“Azure Active Directory” > “活动” > “审核日志”。    

3. 选择“导出设置”。  

4. 在“诊断设置”窗格中，执行下述操作之一： 
   * 若要更改现有设置，请选择“编辑设置”  。
   * 若要添加新设置，请选择“添加诊断设置”  。  
     最多可以有三个设置。 

     ![导出设置](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. 为设置输入一个可以让你记住其用途的友好名称（例如，“发送到 Azure 存储帐户”）。  

6. 选中“存档到存储帐户”  复选框，然后选择“存储帐户”。  

7. 选择要将日志路由到的 Azure 订阅和存储帐户。
 
8. 选择“确定”  以退出配置。

9. 执行下列两项操作或之一：
    * 若要将审核日志发送到存储帐户，请选中“AuditLogs”  复选框。 
    * 若要将登录日志发送到存储帐户，请选中“SignInLogs”  复选框。

10. 使用滑块来设置日志数据的保留期。 默认情况下，此值为 *0*，这意味着日志将无限期地保留在存储帐户中。 如果设置其他值，早于所选天数的事件会自动清除。

11. 选择“保存”  ，保存设置。

    ![诊断设置](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. 在大约 15 分钟后，验证日志是否已推送到存储帐户。 转到 [Azure 门户](https://portal.azure.com)，依次选择“存储帐户”、之前使用的存储帐户、“Blob”。   对于“审核日志”  ，请选择“insights-log-audit”。  对于“登录日志”  ，请选择“insights-logs-signin”。 

    ![存储帐户](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的审核日志架构](reference-azure-monitor-audit-log-schema.md)
* [解释 Azure Monitor 中的登录日志架构](reference-azure-monitor-sign-ins-log-schema.md)
* [常见问题解答和已知的问题](concept-activity-logs-azure-monitor.md#frequently-asked-questions)