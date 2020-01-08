---
title: 处理具有 UNH 2.5 段的 EDIFACT 消息 - Azure 逻辑应用 | Microsoft Docs
description: 在带有 Enterprise Integration Pack 的 Azure 逻辑应用中解析具有 UNH2.5 段的 EDIFACT 文档
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 926c9ebe8675d8b50d4544be813ae0b15492ae35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60681642"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理具有 UNH2.5 段的 EDIFACT 文档

EDIFACT 文档中存在 UNH2.5 时，它用于架构查找。 

示例：UNH 字段是**EAN008** EDIFACT 消息中  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

处理消息要遵循的步骤 
1. 更新架构
2. 检查协议设置  

## <a name="update-the-schema"></a>更新架构
若要处理消息，需要部署具有 UNH2.5 根节点名称的架构。  对于给定的示例，架构根名称将为 **EFACT_D03B_ORDERS_EAN008**  

对于具有不同 UNH2.5 段的每个 D03B_ORDERS，将需要部署单个架构。  

## <a name="add-schema-to-the-edifact-agreement"></a>将架构添加到 EDIFACT 协议
### <a name="edifact-decode"></a>EDIFACT 解码
若要解码传入消息，请在 EDIFACT 协议接收设置中配置架构
1. 将架构添加到集成帐户    
2. 在 EDIFACT 协议接收设置中配置架构。 
3. 选择 EDIFACT 协议，并单击“编辑为 JSON”  。  在接收协议 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png) 中添加 UNH2.5 值

### <a name="edifact-encode"></a>EDIFACT 编码
若要编码传入消息，请在 EDIFACT 协议发送设置中配置架构
1. 将架构添加到集成帐户    
2. 在 EDIFACT 协议发送设置中配置架构。 
3. 选择 EDIFACT 协议，并单击“编辑为 JSON”  。  在发送协议 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png) 中添加 UNH2.5 值

## <a name="next-steps"></a>后续步骤
* [详细了解集成帐户协议](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企业集成协议")  