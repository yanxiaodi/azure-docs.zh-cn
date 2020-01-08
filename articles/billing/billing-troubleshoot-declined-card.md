---
title: 对 Azure 注册时卡被拒进行故障排除
description: 解决在 Azure 门户或帐户中心注册 Azure 时信用卡被拒的问题。
author: v-miegge
manager: adpick
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: banders
ms.openlocfilehash: 730238d62e4ee4aad1807a4461c9b26ee1c8485d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "69657055"
---
# <a name="troubleshoot-a-declined-card-at-azure-sign-up"></a>对 Azure 注册时卡被拒进行故障排除

本文可帮助解决在 Azure 门户或 Azure 帐户中心注册 Azure 时信用卡被拒的问题。 在开始排查问题之前，请检查以下几点：

- 确保为 Azure 帐户个人资料提供的信息（例如联系电子邮件、地址和电话号码）正确无误。
- 确保信用卡信息正确无误。
- 确保尚未拥有包含相同信息的 Microsoft 帐户。
- 不接受借记卡

## <a name="issues"></a>问题

下面列出了可能会导致信用卡在 Azure 注册时被拒的常见问题。

### <a name="the-credit-card-provider-is-not-accepted-for-your-country"></a>你所在的国家/地区不接受此信用卡提供商

你选择卡时，Azure 会显示在所选国家/地区有效的卡选项。 请联系银行或卡颁发机构，确认你的信用卡能够进行国际交易。 有关支持的国家/地区和货币的详细信息，请参阅 [Azure 购买常见问题解答](https://azure.microsoft.com/pricing/faq/)。

>[!Note]
>目前，印度不支持美国运通信用卡作为付款方式。 我们不知道什么时候可以接受这种付款方式。

### <a name="youre-using-a-virtual-or-prepaid-card"></a>你使用的是虚拟卡或预付卡 

虚拟或预付信用卡或借记卡不能作为 Azure 订阅有效的付款选项。

### <a name="your-credit-information-is-inaccurate-or-incomplete"></a>信用信息不准确或不完整 

输入的姓名、地址和 CVV 代码必须与卡上打印的内容完全匹配。

### <a name="the-card-is-inactive-or-blocked"></a>卡处于非活动状态或被阻止 

请联系银行，确保卡有效。

你可能会遇到其他注册问题 

有关如何排查 Azure 注册问题的详细信息，请参阅以下知识库文章： 

[无法在 Azure 门户或 Azure 帐户中心注册 Azure](billing-troubleshoot-azure-sign-up.md)

### <a name="you-represent-a-business-that-doesnt-want-to-pay-by-card"></a>你所代表的企业不想用卡支付 

如果你代表企业，可以使用发票付款方式（如支票、隔夜支票或电汇）来支付 Azure 订阅。 将帐户设置为按发票付款后，除非你签订了 Microsoft 客户协议并通过 Azure 网站注册 Azure，否则无法更改为其他付款选项。

有关如何按发票付款的详细信息，请参阅[提交按发票支付 Azure 订阅的请求](billing-how-to-pay-by-invoice.md)。

### <a name="your-credit-card-information-is-outdated"></a>信用卡信息已过时 

有关如何管理卡信息（包括更改或删除卡）的信息，请参阅[添加、更新或删除 Azure 额度](billing-how-to-change-credit-card.md)。

## <a name="additional-help-resources"></a>其他帮助资源

Azure 计费和订阅的其他疑难解答文章

- [注册问题](billing-troubleshoot-azure-sign-up.md)
- [订阅登录问题](billing-troubleshoot-sign-in-issue.md)
- [找不到任何订阅](billing-no-subscriptions-found.md)
- [企业成本视图已禁用](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>联系我们以获取帮助

如有任何疑问或需要帮助，请[创建支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="next-steps"></a>后续步骤

- [Azure 计费文档](index.md)
