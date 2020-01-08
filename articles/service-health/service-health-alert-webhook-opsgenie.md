---
title: 使用 OpsGenie 使用 webhook 将发送 Azure 服务运行状况警报
description: 获取有关发送到 OpsGenie 实例的服务运行状况事件的个性化通知。
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 06/10/2019
ms.openlocfilehash: fab99b7093ac3f18f6313273d21905e0a3ed7e5b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067165"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>使用 OpsGenie 使用 webhook 将发送 Azure 服务运行状况警报

本文介绍如何使用 Webhook，通过 OpsGenie 设置 Azure 服务运行状况警报。 可以通过 [OpsGenie](https://www.opsgenie.com/) 的 Azure 服务运行状况集成，将 Azure 服务运行状况警报转发到 OpsGenie。 OpsGenie 根据值勤计划来确定要通知的适当人员，使用电子邮件、短信 (SMS)、电话、iOS 和 Android 推送通知以及升级警报向他们发出通知，直到这些人员确认收到警报或将其关闭。

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>在 OpsGenie 中创建服务运行状况集成 URL
1.  请确保已注册并登录到 [OpsGenie](https://www.opsgenie.com/) 帐户。

1.  在 OpsGenie 中导航到“集成”部分。 

    ![OpsGenie 中的“集成”部分](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  选择“Azure 服务运行状况”  集成按钮。

    ![OpsGenie 中的“Azure 服务运行状况按钮”](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  为警报**命名**，并指定“分配到团队”字段。 

1.  填写其他字段，例如“收件人”、“启用”、“禁止显示通知”。   

1.  复制并保存“集成 URL”，其中应该已经包含追加到末尾的 `apiKey`。 

    ![OpsGenie 中的“集成 URL”](./media/webhook-alerts/opsgenie-integration-url.png)

1.  选择“保存集成” 

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>在 Azure 门户中使用 OpsGenie 创建警报
### <a name="for-a-new-action-group"></a>对于新操作组：
1. 执行[使用 Azure 门户为新操作组创建有关服务运行状况通知的警报](../azure-monitor/platform/alerts-activity-log-service-notifications.md)中的步骤 1 到步骤 8。

1. 在“操作”  列表中定义：

    a. **操作类型：** *Webhook*

    b. **详细信息：** 前面保存的 OpsGenie 集成 URL  。

    c. **名称：** Webhook 的名称、别名或标识符。

1. 警报创建完成后，选择“保存”  。

### <a name="for-an-existing-action-group"></a>对于现有操作组：
1. 在 [Azure 门户](https://portal.azure.com/)中，选择“监视”  。

1. 在“设置”  部分中，选择“操作组”  。

1. 找到要编辑的操作组并选择它。

1. 添加到“操作”  列表：

    a. **操作类型：** *Webhook*

    b. **详细信息：** 前面保存的 OpsGenie 集成 URL  。

    c. **名称：** Webhook 的名称、别名或标识符。

1. 操作组更新完成后，选择“保存”  。

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>通过 HTTP POST 请求测试 Webhook 集成
1. 创建要发送的服务运行状况有效负载。 可以在 [Azure 活动日志警报的 Webhook](../azure-monitor/platform/activity-log-alerts-webhook.md) 中找到示例服务运行状况 Webhook 有效负载。

1. 按如下所示创建 HTTP POST 请求：

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. 此时会收到 `200 OK` 响应，其中包含状态消息“成功”。

1. 转到 [OpsGenie](https://www.opsgenie.com/)，确认集成已设置成功。

## <a name="next-steps"></a>后续步骤
- 了解如何[为现有问题管理系统配置 Webhook 通知](service-health-alert-webhook-guide.md)。
- 查看[活动日志警报 webhook 架构](../azure-monitor/platform/activity-log-alerts-webhook.md)。 
- 了解[服务运行状况通知](../azure-monitor/platform/service-notifications.md)。
- 详细了解[操作组](../azure-monitor/platform/action-groups.md)。