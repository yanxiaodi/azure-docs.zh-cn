---
title: 解释 Azure Monitor 中的 Azure Active Directory 审核日志架构 | Microsoft Docs
description: 介绍了在 Azure Monitor 中使用的 Azure AD 审核日志架构
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f75af14e388626a9ebbb54d43079f30dcfdd98a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987945"
---
# <a name="interpret-the-azure-ad-audit-logs-schema-in-azure-monitor-preview"></a>解释 Azure Monitor 中的 Azure AD 审核日志架构（预览版）

本文介绍 Azure Monitor 中的 Azure Active Directory (Azure AD) 审核日志架构。 每个单独的日志项目都存储为文本，格式为 JSON blob，如以下两个示例所示： 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

```json
{
    "records": [
    {
        "time": "2018-12-10T00:03:46.6161822Z",
        "resourceId": "/tenants/7918d4b5-0442-4a97-be2d-36f9f9962ece/providers/Microsoft.aadiam",
        "operationName": "Update policy",
        "operationVersion": "1.0",
        "category": "AuditLogs",
        "tenantId": "7918d4b5-0442-4a97-be2d-36f9f9962ece",
        "resultSignature": "None",
        "durationMs": 0,
        "callerIpAddress": "<null>",
        "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
        "identity": "MS-PIM",
        "level": "Informational",
        "properties": {
            "id": "Directory_VNXV4_28148892",
            "category": "Policy",
            "correlationId": "192298c1-0994-4dd6-b05a-a6c5984c31cb",
            "result": 0,
            "resultReason": "",
            "activityDisplayName": "Update policy",
            "activityDateTime": "2018-12-10T00:03:46.6161822+00:00",
            "loggedByService": "Core Directory",
            "operationType": "Update",
            "initiatedBy": {},
            "targetResources": [
            {
                "id": "5e7a8ae7-165d-44a4-a4f4-6141f8c8ef40",
                "displayName": "Default Policy",
                "type": "Policy",
                "modifiedProperties": []
            }
            ],
            "additionalDetails": []
        }
    }
    ]
}

```

## <a name="field-and-property-descriptions"></a>字段和属性说明

| 字段名称 | 描述 |
|------------|-------------|
| 时间       | 日期和时间 (UTC)。 |
| operationName | 操作的名称。 |
| operationVersion | 客户端请求的 REST API 版本。 |
| category | 目前，“审核”是唯一支持的值。 |
| tenantId | 与日志关联的租户 GUID。 |
| resultType | 操作结果。 结果可以是“成功”或“失败”。 |
| resultSignature |  此字段未映射，可以放心地忽略它。 | 
| resultDescription | 结果的附加说明（如果有）。 | 
| durationMs |  此字段未映射，可以放心地忽略它。 |
| callerIpAddress | 发出请求的客户端的 IP 地址。 | 
| correlationId | 客户端所传递的可选 GUID。 它可以帮助将客户端操作与服务器端操作相关联，并且在跟踪跨服务的日志时非常有用。 |
| identity | 发出请求时提供的令牌中的标识。 标识可以是用户帐户、系统帐户或服务主体。 |
| level | 消息类型。 对于审核日志，此级别始终为“信息”。 |
| location | 数据中心的位置。 |
| properties | 列出与审核日志相关的受支持属性。 有关详细信息，请参阅下一个表格。 | 

<br>

| 属性名 | 描述 |
|---------------|-------------|
| AuditEventCategory | 审核事件的类型。 它可以是“用户管理”、“应用程序管理”或其他类型。|
| 标识类型 | 类型可以是“应用程序”或“用户”。 |
| 操作类型 | 类型可以是“添加”、“更新”、“删除”。 或“其他”。 |
| 目标资源类型 | 指定已对其执行操作的目标资源类型。 类型可以是“应用程序”、“用户”、“角色”、“策略” | 
| 目标资源名称 | 目标资源的名称。 它可以是应用程序名称、角色名称、用户主体名称或服务主体名称。 |
| additionalTargets | 列出特定操作的任何其他属性。 例如，对于更新操作，旧值和新值在 targetUpdatedProperties 下列出。 | 

## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的登录日志架构](reference-azure-monitor-sign-ins-log-schema.md)
* [Azure 诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [常见问题解答和已知的问题](concept-activity-logs-azure-monitor.md#frequently-asked-questions)