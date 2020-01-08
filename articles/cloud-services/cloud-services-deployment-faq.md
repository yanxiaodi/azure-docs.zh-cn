---
title: Microsoft Azure 云服务部署常见问题解答 | Microsoft 文档
description: 本文将介绍一些关于 Microsoft Azure 云服务部署的常见问题解答。
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 2ffa6d7b1cf0550c97a60614f3f00ddc4b955218
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154804"
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure 云服务的部署问题：常见问题 (FAQ)

本文包括一些关于 [Microsoft Azure 云服务](https://azure.microsoft.com/services/cloud-services)部署问题的常见问题解答。 还可以参阅[云服务 VM 大小页面](cloud-services-sizes-specs.md)，了解大小信息。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>生产槽中已存在现有部署的情况下，为什么有时会在将云服务部署到过渡槽时出现资源分配错误？
如果某个云服务在任一槽中存在部署，则会将整个云服务固定到特定的群集。 这意味着，如果生产槽中已存在部署，则只能将新的过渡部署分配到生产槽所在的同一群集中。

当云服务所在的群集没有可满足部署请求的足够物理计算资源时，就会出现分配失败的情况。

有关减轻此类分配失败的帮助， [请参阅云服务分配失败：解决方法](cloud-services-allocation-failures.md#solutions)。

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>为什么缩放或扩展云服务部署有时会导致分配失败？
当部署云服务时，通常会将其固定到特定集群。 这表示缩放/扩展现有云服务必须在同一群集中分配新实例。 如果群集容量趋于饱和或所需的虚拟机大小/类型不可用，则请求可能会失败。

有关减轻此类分配失败的帮助， [请参阅云服务分配失败：解决方法](cloud-services-allocation-failures.md#solutions)。

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>为什么将云服务部署到地缘组有时会导致分配失败？
进行新的目标为空云服务的部署时，可以通过该区域任何群集中的结构对部署进行分配，除非已将云服务固定到地缘组。 会在相同的群集中尝试部署到相同的地缘组。 如果群集已接近容量，则请求可能失败。

有关减轻此类分配失败的帮助， [请参阅云服务分配失败：解决方法](cloud-services-allocation-failures.md#solutions)。

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>为什么更改虚拟机大小或将新的虚拟机添加到现有云服务有时会导致分配失败？
数据中心内的群集可能具有不同的计算机类型配置（例如，系列、Av2 系列、D 系列、Dv2 系列、G 系列、H 系列，等等）。 但并不是所有的群集都必须拥有所有类型的虚拟机。 例如，如果尝试将 D 系列虚拟机添加到仅部署在 A 系列群集中的云服务，则会出现分配失败。 尝试更改 VM SKU 大小（例如，从 A 系列切换到 D 系列）也会导致这种情况的发生。

有关减轻此类分配失败的帮助， [请参阅云服务分配失败：解决方法](cloud-services-allocation-failures.md#solutions)。

若要检查您的区域中可用的大小[，请参阅 Microsoft Azure：可用产品（按](https://azure.microsoft.com/regions/services)区域）。

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>为什么我的订阅或服务的限制/配额/约束有时会导致部署云服务失败？
如果需要分配的资源超过服务所在区域/数据中心级别允许的默认或最大配额，则云服务部署可能会失败。 有关详细信息，请参阅[云服务限制](../azure-subscription-service-limits.md#azure-cloud-services-limits)。

还可以在门户上跟踪订阅的当前使用情况/配额：Azure 门户 = > 订阅 = > \<相应的订阅 > = > "使用情况 + 配额"。

资源使用情况/相关消耗信息也可以通过 Azure 计费 API 检索。 请参阅 [Azure 资源使用状况 API（预览）](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview)。

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>如何在不重新部署已部署云服务虚拟机的情况下更改其大小？
你无法在不重新部署已部署云服务虚拟机的情况下更改其大小。 虚拟机大小内置在 CSDEF 中，只能通过重新部署进行更新。

有关详细信息，请参阅[如何更新云服务](cloud-services-update-azure-service.md)。

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>使用 Azure 资源管理器存储帐户时，为什么不能够通过服务管理 API 或 PowerShell 部署云服务？ 

由于云服务是与 Azure 资源管理器模型不直接兼容的经典资源，因此不能将其与 Azure 资源管理器存储帐户相关联。 下面是几个选项： 
 
- 通过 REST API 部署。

    通过服务管理 REST API 部署时，可以通过指定指向 blob 存储（同时使用经典和 Azure 资源管理器存储帐户）的 SAS URL 绕过限制。 在[此处](/previous-versions/azure/reference/ee460813(v=azure.100))阅读有关 PackageUrl 属性的详细信息。
  
- 通过 [Azure 门户](https://portal.azure.com)部署。

    这将从[Azure 门户](https://portal.azure.com)处理，因为调用通过允许 Azure 资源管理器和经典资源之间通信的代理/填充程序来完成。 
 
## <a name="why-does-azure-portal-require-me-to-provide-a-storage-account-for-deployment"></a>为什么 Azure 门户要求我提供部署所需的存储帐户？ 

在经典门户中，包直接上传到管理 API 层，然后 API 层暂时将其放入内部存储帐户。  API 层并不是文件上传服务，因此这个过程会导致性能和可伸缩性问题。  在 Azure 门户中（资源管理器部署模型），我们越过了先上传到 API 层这一临时步骤，因此实现了更快、更可靠的部署。 

所需成本很少，并且可以在所有部署中重复使用同一存储帐户。 可以使用[存储成本计算器](https://azure.microsoft.com/pricing/calculator/#storage1)确定上传服务包 (CSPKG)、下载 CSPKG 以及之后删除 CSPKG 的成本。 
