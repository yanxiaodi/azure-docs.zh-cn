---
title: Azure API 管理策略示例 - 使用 OAuth2 在网关和后端之间进行授权 | Microsoft Docs
description: Azure API 管理策略示例 - 演示如何使用 OAuth2 在网关和后端之间进行授权。 该示例演示如何从 AAD 获取访问令牌并将其转发到后端。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: fac10b728e4b7f09ec1019c3257f7c9e5d6e7714
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071863"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>使用 OAuth2 在网关和后端之间进行授权

本文介绍 Azure API 管理策略示例，该示例演示如何使用 OAuth2 在网关和后端之间进行授权。 该示例演示如何从 AAD 获取访问令牌并将其转发到后端。 

若要设置或编辑策略代码，请执行[设置或编辑策略](../set-edit-policies.md)中所述的步骤。 若要查看其他示例，请参阅[策略示例](../policy-samples.md)。

以下脚本使用了 {{property}} 中出现的属性。 若要了解各个属性以及如何在 API 管理策略中使用它们，请参阅[此](../api-management-howto-properties.md)主题。
 
## <a name="policy"></a>策略

将代码粘贴到“入站”块中。

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>后续步骤

了解有关 APIM 策略的详细信息：

+ [转换策略](../api-management-transformation-policies.md)
+ [策略示例](../policy-samples.md)

