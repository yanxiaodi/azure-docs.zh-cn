---
title: 跨租户进行身份验证 - Azure 资源管理器
description: 介绍了 Azure 资源管理器如何跨租户处理身份验证请求。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 625a17156eaf199af0d51151c6fd37769b8f7b4a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848756"
---
# <a name="authenticate-requests-across-tenants"></a>跨租户对请求进行身份验证

创建多租户应用程序时，可能需要处理针对不同租户中的资源的身份验证请求。 一个租户中的虚拟机必须加入另一个租户中的虚拟网络是一种常见情况。 Azure 资源管理器提供了一个标头值来存储用于对针对不同租户的请求进行身份验证的辅助令牌。

## <a name="header-values-for-authentication"></a>用于身份验证的标头值

请求具有以下身份验证标头值：

| 标头名称 | 描述 | 示例值 |
| ----------- | ----------- | ------------ |
| Authorization | 主令牌 | Bearer &lt;primary-token&gt; |
| x-ms-authorization-auxiliary | 辅助令牌 | 持有&lt;者 token1&gt;, EncryptedBearer &lt;, token2&gt;, 持有&lt;者辅助-token3&gt; |

辅助标头最多可以容纳三个辅助令牌。 

在多租户应用的代码中，获取其他租户的身份验证令牌并将其存储在辅助标头中。 所有令牌都必须来自同一用户或应用程序。 该用户或应用程序必须已被作为来宾邀请到其他租户。

## <a name="processing-the-request"></a>处理请求

当应用向资源管理器发送请求时，该请求将以主令牌中的身份运行。 主令牌必须有效且未过期。 此令牌必须来自可以管理订阅的租户。

当请求引用了其他租户的资源时，资源管理器会检查辅助令牌来确定是否可以处理该请求。 标头中的所有辅助标记必须有效且未过期。 如果有任何令牌已过期，则资源管理器将返回 401 响应代码。 响应包括无效令牌中的客户端 ID 和租户 ID。 如果辅助标头包含租户的有效令牌，则会处理跨租户请求。

## <a name="next-steps"></a>后续步骤
* 若要了解如何通过 Azure 资源管理器 API 发送身份验证请求，请参阅[使用资源管理器身份验证 API 访问订阅](resource-manager-api-authentication.md)。
* 有关令牌的详细信息，请参阅 [Azure Active Directory 访问令牌](/azure/active-directory/develop/access-tokens)。
