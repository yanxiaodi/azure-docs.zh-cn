---
title: 了解活动日志警报中使用的 Webhook 架构
description: 了解有关活动日志警报激活时发布到 webhook URL 的 JSON 架构。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: b9ba809baa8fc4adddfad1344d6f36375cb361c4
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675225"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Azure 活动日志警报的 Webhook
作为操作组定义的一部分，可以配置 webhook 终结点以接收活动日志警报通知。 通过 webhook 可以将这些通知路由到其他系统，以便进行后续处理或自定义操作。 本文介绍针对 webhook 发出的 HTTP POST 的有效负载的大致形式。

有关活动日志警报的详细信息，请参阅如何[创建 Azure 活动日志警报](activity-log-alerts.md)。

有关操作组的信息，请参阅如何[创建操作组](../../azure-monitor/platform/action-groups.md)。

> [!NOTE]
> 还可以使用[常见警报架构](https://aka.ms/commonAlertSchemaDocs)，它的优点是可以跨 Azure Monitor 中的所有警报服务提供单个可扩展且统一的警报有效负载，用于 Webhook 集成。 [了解常见的警报架构定义。](https://aka.ms/commonAlertSchemaDefinitions)


## <a name="authenticate-the-webhook"></a>对 webhook 进行身份验证
Webhook 可以选择使用基于令牌的授权进行身份验证。 保存的 webhook URI 具有令牌 ID，例如，`https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`。

## <a name="payload-schema"></a>负载架构
根据有效负载的 data.context.activityLog.eventSource 字段，POST 操作中包含的 JSON 有效负载会有所不同。

### <a name="common"></a>常见

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>管理

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>安全性

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>建议

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>ServiceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

有关服务运行状况通知活动日志警报的特定架构详细信息，请参阅[服务运行状况通知](../../azure-monitor/platform/service-notifications.md)。 此外，请了解如何[使用现有的问题管理解决方案配置服务运行状况 Webhook 通知](../../service-health/service-health-alert-webhook-guide.md)。

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| 元素名称 | 描述 |
| --- | --- |
| 状态 |用于度量值警报。 对于活动日志警报，始终设置为“已激活”。 |
| context |事件的上下文。 |
| resourceProviderName |受影响资源的资源提供程序。 |
| conditionType |始终为“事件”。 |
| name |警报规则的名称。 |
| ID |警报的资源 ID。 |
| description |创建警报时设置警报说明。 |
| subscriptionId |Azure 订阅 ID。 |
| timestamp |处理请求的 Azure 服务生成事件的时间。 |
| resourceId |受影响资源的资源 ID。 |
| resourceGroupName |受影响资源的资源组的名称。 |
| properties |一组包含事件详细信息的 `<Key, Value>` 对（即 `Dictionary<String, String>`）。 |
| 事件 |包含有关事件的元数据的元素。 |
| authorization |事件的基于角色的访问控制属性。 这些属性通常包括“action”、“role”和“scope”。 |
| category |事件的类别。 支持的值包括“Administrative”、“Alert”、“Security”、“ServiceHealth”和“Recommendation”。 |
| caller |执行操作的用户的电子邮件地址（基于可用性的 UPN 声明或 SPN 声明）。 对于某些系统调用可以为 null。 |
| correlationId |通常是字符串格式的 GUID。 具有属于同一较大操作的 correlationId 的事件，通常共享 correlationId。 |
| eventDescription |事件的静态文本说明。 |
| eventDataId |事件的唯一标识符。 |
| eventSource |生成事件的 Azure 服务或基础结构的名称。 |
| httpRequest |请求通常包括“clientRequestId”、“clientIpAddress”和“HTTP method”（例如 PUT）。 |
| 级别 |以下值之一：Critical、Error、Warning 和 Informational。 |
| operationId |通常是在与单个操作对应的事件之间共享的 GUID。 |
| operationName |操作的名称。 |
| properties |事件的属性。 |
| 状态 |字符串。 操作的状态。 常见值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| subStatus |通常包含对应 REST 调用的 HTTP 状态代码。 它还可能包含描述子状态的其他字符串。 常见子状态值包括“正常(HTTP 状态代码: 200)”、“已创建(HTTP 状态代码: 201)、已接受(HTTP 状态代码:202)、没有任何内容(HTTP 状态代码:204)、错误的请求(HTTP 状态代码:400)、找不到(HTTP 状态代码:404)、冲突(HTTP 状态代码:409)、内部服务器错误(HTTP 状态代码:500)”、“服务不可用(HTTP 状态代码: 503)”和“网关超时(HTTP 状态代码: 504)。 |

有关所有其他活动日志警报的特定架构的详细信息，请参阅 [Azure 活动日志概述](../../azure-monitor/platform/activity-logs-overview.md)。

## <a name="next-steps"></a>后续步骤
* [了解有关活动日志的更多信息](../../azure-monitor/platform/activity-logs-overview.md)。
* [对 Azure 警报执行 Azure 自动化脚本 (Runbook)](https://go.microsoft.com/fwlink/?LinkId=627081)。
* [使用逻辑应用通过 Twilio 从 Azure 警报发送短信](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)。 本示例适用于度量值警报，但经过修改后可用于活动日志警报。
* [使用逻辑应用从 Azure 警报发送 Slack 消息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)。 本示例适用于度量值警报，但经过修改后可用于活动日志警报。
* [使用逻辑应用从 Azure 警报将消息发送到 Azure 队列](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)。 本示例适用于度量值警报，但经过修改后可用于活动日志警报。

