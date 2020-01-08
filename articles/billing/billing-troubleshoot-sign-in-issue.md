---
title: 排查 Azure 订阅登录问题
description: 帮助解决无法登录 Azure 门户或 Azure 帐户中心的问题。
author: v-miegge
manager: na
editor: na
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: v-miegge
ms.openlocfilehash: ca641813e8b01a39d31a56e3730424b0fa1d6436
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "69657042"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>排查 Azure 订阅登录问题

本指南可帮助解决无法登录 Azure 门户或 Azure 帐户中心的问题。 

## <a name="issues"></a>问题

### <a name="page-hangs-in-the-loading-status"></a>页面在加载状态下挂起

如果 Internet 浏览器页面挂起，请尝试执行以下每个步骤，直到能够访问 Azure 门户为止。

- 刷新页面。
- 使用另一个 Internet 浏览器。
- 使用浏览器的专用浏览模式。 对于 Internet Explorer：单击“工具”   > “安全”   > “InPrivate 浏览”  ，然后浏览并登录到 [Azure 门户](https://portal.azure.com/)或 [Azure 帐户中心](https://account.azure.com/Subscriptions)。

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>用户以其他用户身份自动登录

如果在 Internet 浏览器中使用多个用户帐户，则可能出现此问题。

若要解决此问题，请尝试下列方法：

- 清除缓存并删除 Internet Cookie。 在 Internet Explorer 中，单击“工具”   >   “Internet 选项” >   “删除”。 确保选中临时文件、cookie、密码和浏览历史记录的复选框，并单击“删除”。
- 重置 Internet Explorer 设置，还原所做的任何个人设置。 单击“工具”   >   “Internet 选项” >   “高级”> 选中“删除个人设置”  框 >“重置”  。
- 使用浏览器的专用浏览模式。 对于 Internet Explorer：单击“工具”   > “安全”   > “InPrivate 浏览”  ，然后浏览并登录到 [Azure 门户](https://portal.azure.com/)或 [Azure 帐户中心](https://account.azure.com/Subscriptions)。

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>可以登录，但看到“找不到任何订阅” 

如果选择了错误的目录，或者帐户没有足够的权限，会出现此问题。

**场景 1：** [Azure 门户](https://portal.azure.com/)收到错误消息

解决此问题：

- 通过单击右上角的帐户确保已选择正确的 Azure 目录。
- 如果已选择正确的 Azure 目录，但仍收到错误消息，请将帐户[添加为所有者](billing-add-change-azure-subscription-administrator.md)。

**场景 2：** [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)收到错误消息

请检查使用的帐户是否是帐户管理员。 要验证谁是帐户管理员，请执行下列步骤：

1. 登录到 [Azure 门户中的订阅视图](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。

2. 选择要检查的订阅，并关注“设置”  下的信息。

3. 选择“属性”。  订阅的帐户管理员会显示在“帐户管理员”框中。 

## <a name="additional-help-resources"></a>其他帮助资源

Azure 计费和订阅的其他疑难解答文章

- [银行卡被拒绝](billing-troubleshoot-declined-card.md)
- [订阅注册问题](billing-troubleshoot-azure-sign-up.md)
- [找不到任何订阅](billing-no-subscriptions-found.md)
- [企业成本视图已禁用](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>联系我们以获取帮助

如有任何疑问或需要帮助，请[创建支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- [Azure 计费文档](index.md)
