---
title: 获取 Azure 订阅的计费所有权
description: 了解如何从其他用户请求 Azure 订阅的计费所有权。
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "69019558"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>从其他帐户获取 Azure 订阅的计费所有权

如果现有的计费所有者即将离职，或者你希望通过计费帐户为订阅付费，你可以接管 Azure 订阅的所有权。 接管所有权会将订阅的计费责任转移到你的帐户。

本文适用于 Microsoft 客户协议的计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-for-access)。

只有**发票科目所有者**或**发票科目参与者**才能请求计费所有权。 有关详细信息，请参阅[发票科目角色任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。

## <a name="request-billing-ownership"></a>请求计费所有权

1. 以 Microsoft 客户协议计费帐户的发票科目所有者或参与者身份登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. 在左侧选择“发票科目”。  根据你的访问权限，可能需要选择一个计费帐户或计费配置文件。 在计费帐户或配置文件中，选择“发票科目”。 
   
   ![显示选择发票科目的屏幕截图](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 从列表中选择一个发票科目。 接管订阅所有权后，将从此发票科目中计收订阅费用。

5. 选择左下角的“转移请求”，然后选择“添加”。  
 
   ![显示选择转移请求的屏幕截图](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. 输入要从其请求计费所有权的用户的电子邮件地址。 该用户必须是 Microsoft 在线服务计划计费帐户的帐户管理员，或企业协议中的帐户所有者。 有关详细信息，请参阅[在 Azure 门户中查看计费帐户](billing-view-all-accounts.md)。 选择“发送转移请求”。 

   ![显示发送转移请求的屏幕截图](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. 用户将收到一封电子邮件，其中包含查看转让请求的说明。

   ![显示查看转移请求电子邮件的屏幕截图](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. 若要批准转移请求，用户需选择电子邮件中的链接，并按说明操作。

    ![显示查看转移请求电子邮件的屏幕截图](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>检查转移请求状态

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

   ![显示如何在 Azure 门户中搜索“成本管理 + 计费”的屏幕截图](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. 在左侧选择“发票科目”。  根据你的访问权限，可能需要选择一个计费帐户或计费配置文件。 在计费帐户或配置文件中，选择“发票科目”。 
   
   ![显示选择发票科目的屏幕截图](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. 从列表中选择已对其发送转移请求的发票科目。

5. 选择左下角的“转移请求”。  “转移请求”页将显示以下信息：

    ![显示转移请求列表的屏幕截图](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |列|定义|
   |---------|---------|
   |请求日期|发送转移请求的日期|
   |Recipient|已向其发送转移计费所有权请求的用户的电子邮件地址|
   |到期日期|请求过期的日期|
   |状态|转移请求的状态|

    转移请求可能处于以下状态之一：

   |状态|定义|
   |---------|---------|
   |正在进行|用户未接受转移请求|
   |Processing|用户已批准转移请求。 用户选择的订阅的计费正在转移到你的发票科目|
   |已完成| 用户选择的订阅的计费已转移到你的发票科目|
   |已完成但出错|请求已完成，但用户选择的某些订阅的计费无法转移|
   |已过期|用户未及时接受请求，请求现已过期|
   |已取消|有权访问转移请求的某人取消了该请求|
   |已拒绝|用户拒绝了转移请求|

7. 选择一个转移请求以查看详细信息。 转移详细信息页将显示以下信息：
   
   ![显示已转移订阅列表的屏幕截图](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |列  |定义|
   |---------|---------|
   |转移请求 ID|转移请求的唯一 ID。 提交支持请求时，请与 Azure 支持人员共享该 ID，以加速支持请求的处理|
   |转移请求日期|发送转移请求的日期|
   |转移请求者|发送转移请求的用户的电子邮件地址|
   |转移请求过期日期| 转移请求的过期日期|
   |收件人的电子邮件地址|已向其发送转移计费所有权请求的用户的电子邮件地址|
   |发送给收件人的转移链接|发送给用户的 URL，用于查看转移请求|

## <a name="supported-subscription-types"></a>支持的订阅类型

可以请求下面列出的订阅类型的计费所有权。

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure 开放许可](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Azure Pass 赞助](https://azure.microsoft.com/offers/azure-pass/)\*
- [Enterprise 开发/测试](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Microsoft Azure 赞助套餐](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Microsoft 企业协议](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Visual Studio Enterprise (BizSpark) 订户](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Visual Studio Enterprise (MPN) 订户](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Visual Studio Enterprise 订户](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Visual Studio Test Professional 订户](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* 转移后，订阅中可用的任何额度在新帐户中将不可用。

\*\* 仅支持在 Azure 网站上注册期间创建的帐户中的订阅。


## <a name="additional-information"></a>其他信息

以下部分提供有关转移订阅的其他信息。

### <a name="no-service-downtime"></a>服务不会中断

订阅中的 Azure 服务将保持正常运行，而不会出现任何中断。 我们只会转换用户选择转移的 Azure 订阅的计费关系。

### <a name="disabled-subscriptions"></a>已禁用的订阅

无法转移已禁用的订阅。 订阅必须处于活动状态才能转移其计费所有权。

### <a name="azure-resources-transfer"></a>Azure 资源转移

订阅中的所有资源（例如 VM、磁盘和网站）都会转移。

### <a name="azure-marketplace-products-transfer"></a>Azure 市场产品转移

Azure 市场产品将连同各自的订阅一起转移。

### <a name="azure-reservations-transfer"></a>Azure 预留项转移

Azure 预留项不会随订阅一起自动移动。 请[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)移动预留项。

### <a name="access-to-azure-services"></a>对 Azure 服务的访问权限

在转换期间，使用 Azure RBAC（基于角色的访问控制）[../role-based-access-control/overview.md] 为现有用户、组或服务主体分配的访问权限不受影响。

### <a name="azure-support-plan"></a>Azure 支持计划

Azure 支持不会随订阅一起转移。 如果用户要转移所有 Azure 订阅，请让他们取消其支持计划。

### <a name="charges-for-transferred-subscription"></a>转移的订阅费用

订阅的原始计费所有者负责支付转移完成之前所报告的所有费用。 你的发票科目负责支付从转移开始时报告的费用。 可能有些费用是在转移之前发生的，但在转移之后才报告。 这些费用将显示在你的发票科目中。

### <a name="cancel-a-transfer-request"></a>取消转移请求

在批准或拒绝转移请求之前，你可以取消该请求。 若要取消转移请求，请转到[转移详细信息页](#check-the-transfer-request-status)，并在页面底部选择“取消”。

### <a name="software-as-a-service-saas-transfer"></a>软件即服务 (SaaS) 转移

SaaS 产品不会随订阅一起转移。 请让用户[联系 Azure 支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)转移 SaaS 产品的计费所有权。 除了计费所有权以外，用户还可以转移资源所有权。 使用资源所有权可以执行管理操作，例如删除产品，以及查看产品详细信息。 用户必须是 SaaS 产品中的资源所有者才能转移资源所有权。

## <a name="check-for-access"></a>检查访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- Azure 订阅的计费所有权现已转移到你的发票科目。 可在 [Azure 门户](https://portal.azure.com)中跟踪这些订阅的费用。
- 为其他人授予查看和管理这些订阅的计费的权限。 有关详细信息，请参阅[发票科目角色和任务](billing-understand-mca-roles.md#invoice-section-roles-and-tasks)。
