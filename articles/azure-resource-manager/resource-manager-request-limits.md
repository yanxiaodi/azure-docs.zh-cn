---
title: 请求限制 - Azure 资源管理器 | Microsoft Azure
description: 介绍在达到订阅限制时，如何对 Azure 资源管理器请求使用限制。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: f457b316d9f499f2cab02452c1b03ad07a9aef27
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302831"
---
# <a name="throttling-resource-manager-requests"></a>限制 Resource Manager 请求

对于每个 Azure 订阅和租户，资源管理器的限制为每小时最多 12,000 个读取请求和 1,200 个写入请求。 这些限制的范围限定为发出请求的安全主体 (用户或应用程序) 以及订阅 ID 或租户 ID。 如果你的请求来自多个安全主体, 则你的订阅或租户的限制将大于 12000, 1200/小时。

请求适用于订阅或租户。 订阅请求涉及到传递订阅 ID, 例如, 检索订阅中的资源组。 租户请求（例如，检索有效的 Azure 位置）不包括订阅 ID。

这些限制适用于每个 Azure 资源管理器实例。 每个 Azure 区域中有多个实例，Azure 资源管理器将部署到所有 Azure 区域。  因此，在实践中，限制实际上比上述限制要高得多，因为用户请求通常是由多个不同的实例提供服务。

如果应用程序或脚本达到这些限制，则需要限制请求。 本文说明如何在达到限制之前确定剩余的请求数，以及达到限制时如何做出响应。

达到限制时，会收到 HTTP 状态代码“429 请求过多”。 

Azure 资源图限制对其操作的请求数。 本文中的步骤确定剩余的请求以及在达到限制时如何做出响应也适用于资源图。 但是, 资源图表会设置其自己的限制和重置速率。 有关详细信息, 请参阅[Azure 资源图中的限制](../governance/resource-graph/overview.md#throttling)。

## <a name="remaining-requests"></a>剩余的请求数
可以通过检查响应标头来确定剩余的请求数。 读取请求在标头中返回一个值，表示剩余读取请求的数目。 写入请求包含的值表示剩余写入请求的数目。 下表描述了可在其中检查这些值的标头：

| 响应标头 | 描述 |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |划归到订阅的剩余读取数。 执行读取操作时返回此值。 |
| x-ms-ratelimit-remaining-subscription-writes |划归到订阅的剩余写入数。 执行写入操作时返回此值。 |
| x-ms-ratelimit-remaining-tenant-reads |划归到租户的剩余读取数 |
| x-ms-ratelimit-remaining-tenant-writes |划归到租户的剩余写入数 |
| x-ms-ratelimit-remaining-subscription-resource-requests |划归到订阅的剩余资源类型请求数。<br /><br />仅当服务重写了默认限制时，才返回此标头值。 Resource Manager 将累加此值而不是订阅读取/写入数。 |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |划归到订阅的剩余资源类型集合请求数。<br /><br />仅当服务重写了默认限制时，才返回此标头值。 此值提供剩余集合请求数（列出资源）。 |
| x-ms-ratelimit-remaining-tenant-resource-requests |划归到租户的剩余资源类型请求数。<br /><br />仅当服务重写了默认限制时，才为租户级别的请求添加此标头。 Resource Manager 将累加此值而不是租户读取/写入数。 |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |划归到租户的剩余资源类型集合请求数。<br /><br />仅当服务重写了默认限制时，才为租户级别的请求添加此标头。 |

## <a name="retrieving-the-header-values"></a>检索标头值
检索代码或脚本中的这些标头值与检索任何标头值没有什么不同。 

例如，在 **C#** 中，可使用以下代码从名为 **response** 的 **HttpWebResponse** 对象中检索标头值：

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

在 **PowerShell** 中，可以从 Invoke-WebRequest 操作检索标头值。

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

有关完整 PowerShell 示例的信息，请参阅[检查订阅的资源管理器限制](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)。

如果想要查看剩余的调试请求数，可以在“PowerShell”cmdlet 中提供“-Debug”参数   。

```powershell
Get-AzResourceGroup -Debug
```

这会返回许多值，包括以下响应值：

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

若要获取写入限制，请使用写入操作： 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

这会返回许多值，包括以下值：

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

在 **Azure CLI** 中，可以使用更详细的选项检索标头值。

```azurecli
az group list --verbose --debug
```

这会返回许多值，包括以下值：

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

若要获取写入限制，请使用写入操作： 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

这会返回许多值，包括以下值：

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>发送下一个请求之前等待一段时间
达到请求限制时，Resource Manager 会在标头中返回 **429** HTTP 状态代码和 **Retry-After** 值。 **Retry-After** 值指定在发送下一个请求之前应用程序应该等待（或休眠）秒数。 如果在尚未达到重试时间值的情况下发送请求，该请求不会得到处理，并会返回一个新的重试时间值。

## <a name="next-steps"></a>后续步骤

* 有关完整 PowerShell 示例的信息，请参阅[检查订阅的资源管理器限制](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)。
* 有关限制和配额的详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)。
* 若要了解如何处理异步 REST 请求，请参阅[跟踪异步 Azure 操作](resource-manager-async-operations.md)。
