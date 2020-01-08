---
title: Power BI 工作区集合常见问题解答
description: 与 Power BI 工作区集合相关的常见问题。
services: power-bi-workspace-collections
ms.service: power-bi-embedded
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: b737c7753ce374d0360738e37d83609d1db995b1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672424"
---
# <a name="power-bi-workspace-collections-faq"></a>Power BI 工作区集合常见问题解答

> [!IMPORTANT]
> Power BI 工作区集合已弃用，到 2018 年 6 月 或合同指示时可用。 建议你规划到 Power BI Embedded 的迁移以避免应用程序中断。 有关如何将数据迁移到 Power BI Embedded 的信息，请参阅[如何将 Power BI 工作区集合内容迁移到 Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)。

## <a name="what-is-microsoft-power-bi-workspace-collections"></a>什么是 Microsoft Power BI 工作区集合？

Power BI 工作区集合是一项 Azure 服务，应用程序开发人员可以通过该服务将令人惊叹的完整交互式报表和可视化元素嵌入到面向客户的应用中，使客户无需花费时间和费用从头开始生成其自己的控件。 现在，全球 9 个数据中心内都提供了包含 SLA 的 Power BI 工作区集合。 此服务中还提供了增强的功能，例如，支持通过 Power BI 工作区集合中的行级安全性 (RLS) 提供数据安全性以用于高级筛选。 Power BI 工作区集合定价模型也进行了简化和更新。

## <a name="who-would-want-to-use-microsoft-power-bi-workspace-collections-and-why"></a>谁想要使用 Microsoft Power BI 工作区集合，为什么？

Microsoft Power BI 工作区集合适用于想要向其用户的所有设备提供令人惊叹的交互式数据可视化元素体验的应用程序开发人员，使用户无需自行生成这些体验。 通过 Power BI 工作区集合，开发人员可以利用直接查询提供始终保持最新的视图。 开发人员还可以使用 Azure 资源管理器 API 和 Power BI API 以编程方式部署、管理和自动化 Power BI。 与所有的 Power BI 服务一样，这种嵌入式服务也可以自动缩放以满足应用程序的使用情况和需求。 Power BI 工作区集合服务采取即用即付消费的定价模式。

## <a name="how-does-power-bi-workspace-collections-relate-to-the-power-bi-service"></a>Power BI 工作区集合与 Power BI 服务有什么关系？

Power BI 工作区集合和 Power BI 服务是单独的产品/服务。 Power BI 工作区集合采取基于消费的计费模式，通过 Azure 门户部署并且旨在使 ISV 能够将数据可视化元素嵌入到应用程序中，供其客户使用。 在 Power BI 服务进行计费和通过 Microsoft 365 管理中心内部署和独立通用的 BI 产品/服务主要面向在企业内部使用。 若要了解 Power BI 服务的详细信息，请参阅 [www.powerbi.com](https://powerbi.microsoft.com)。

## <a name="how-does-power-bi-workspace-collections-improve-my-app"></a>Power BI 工作区集合如何改进应用？

当可以利用优秀的交互式数据可视化功能直接在应用程序中通知用户的决策时，应用程序的功能将更为强大。 Power BI 工作区集合可通过丰富的、始终保持最新的交互式数据可视化元素增强应用，以便提高应用的实用性、用户的满意度和忠诚度，并可在任何设备上轻松提供语境分析。

## <a name="are-there-any-rules-or-restrictions-about-how-i-can-use-power-bi-workspace-collections-in-my-app"></a>关于如何在应用中使用 Power BI 工作区集合是否存在任何规则或限制？

Power BI 工作区集合仅用于为供第三方使用而提供的应用程序。 如果想要使用 Power BI 工作区集合服务创建内部业务应用程序，则每个内部用户都需要具有一个 Power BI Pro USL，并且组织除需要支付 Power BI Pro USL 费用外，还需要支付使用 Power BI 工作区集合服务的费用。 为避免产生 Power BI Pro USL 费和 Power BI 工作区集合用于内部应用程序的消耗费，Power BI 服务在 Power BI 工作区集合外提供了其自己的内容嵌入功能，而未额外增加 Power BI USL 持有者的成本 (dev.powerbi.com)。

## <a name="can-power-bi-workspace-collections-be-used-to-create-internal-applications"></a>Power BI 工作区集合可以用于创建内部应用程序吗？

不能，Power BI 工作区集合仅供外部用户使用，不应在内部业务应用程序中使用。 为了嵌入用于内部业务应用程序的 Power BI 内容，应该使用 Power BI 服务，并且所有使用该内容的用户必须拥有一个有效的 Power BI 免费版或 Power BI Pro 的用户订阅许可证。 有关如何将内部应用程序与 Power BI 服务进行集成的详细信息，请访问 [https://dev.powerbi.com](https://dev.powerbi.com)。

## <a name="is-this-service-available-globally"></a>此服务是否在全球提供？

目前，大多数数据中心内都提供 Power BI 工作区集合服务。 始终可以[在此处](https://azure.microsoft.com/status/)检查最新可用性。

## <a name="what-is-the-available-sla-for-the-service"></a>对于该服务，可用的 SLA 是什么？

具有 Azure 标准 SLA 的 Power BI 工作区集合。 有关详细信息，请参阅[服务级别协议](https://azure.microsoft.com/support/legal/sla/)。

## <a name="what-is-a-report-session-and-how-is-it-billed"></a>什么是报表会话，它是如何计费的？

会话是指最终用户和 Power BI 工作区集合报表之间的一组交互活动。 每次向用户显示 Power BI 工作区集合报表时，就会启动一个会话，并向订阅持有者收取使用会话的费用。 会话按统一收费率计费，独立于报表中的视觉对象元素数量或报表内容的刷新频率。 当用户关闭报表时或者会话于一小时后超时时，会话结束。

## <a name="do-you-offer-any-tools-or-guidance-to-help-me-estimate-how-many-renderssession-i-should-expect-how-will-i-know-how-many-renders-have-been-completed"></a>是否提供了任何工具或指南来帮助用户估计应预估的呈现/会话数？ 如何才能知道已完成了多少呈现？

Azure 门户会提供关于针对订阅已执行的呈现/报表会话数的帐单详细信息。

## <a name="do-i-need-a-power-bi-subscription-in-order-to-develop-applications-with-power-bi-workspace-collections-how-do-i-get-started"></a>使用 Power BI 工作区集合开发应用程序是否需要 Power BI 订阅？ 如何入门？

作为应用程序开发人员，创建希望在应用程序中使用的报表和可视化元素无需具有 Power BI 订阅。 但需要 Microsoft Azure 订阅和免费的 Power BI Desktop 应用程序。

有关如何使用 Power BI 工作区集合服务的详细信息，请参阅服务文档。

## <a name="i-have-an-azure-subscription-can-i-use-power-bi-workspace-collections-using-my-existing-subscription"></a>用户有一个 Azure 订阅。 是否可以通过现有的订阅使用 Power BI 工作区集合？

是的。 可以使用现有的 Azure 订阅预配和使用 Microsoft Power BI 工作区集合服务。

## <a name="does-my-application-end-user-need-a-power-bi-license"></a>应用程序的最终用户是否需要 Power BI 许可证？

否。 应用程序的最终用户无需单独购买 Power BI 订阅便可访问应用内数据可视化元素。 在 Power BI 工作区集合模型中，会通过 Azure 消耗计量器针对服务向应用程序提供商计费。 请参阅[定价和许可页](https://go.microsoft.com/fwlink/?LinkId=760527)。

## <a name="how-does-user-authentication-work-with-power-bi-workspace-collections"></a>如何对 Power BI 工作区集合的用户进行身份验证？

Power BI 工作区集合服务使用“应用令牌”进行身份验证和授权，而不是使用显式的最终用户身份验证。 在应用令牌模型中，应用程序管理最终用户的身份验证和授权。 然后，若有必要，应用将创建

并发送应用令牌，以指示服务来呈现所请求的报表。 此设计不要求应用使用 Azure AD 进行用户身份验证和授权，但仍然可以这样做。 可以[在此处](app-token-flow.md)了解应用令牌的详细信息。 Power BI 工作区集合中还引入了行级安全性 (RLS) 功能以用于高级安全性筛选方案。

## <a name="what-data-sources-are-currently-supported-with-power-bi-workspace-collections"></a>Power BI 工作区集合当前支持哪些数据源？

将支持通过直接查询对使用基本凭据的云数据源进行访问。 这意味着目前支持的源有 Azure SQL DB 和 Azure SQL DW 等。 在未来几个月中会添加对其他数据源和访问类型的支持。 有关详细信息，请参阅[连接到数据源](connect-datasource.md)。

## <a name="how-does-the-tenancy-model-work-for-power-bi-workspace-collections"></a>Power BI 工作区集合的租户模型如何工作？

在 Power BI 工作区集合模型中，明确要求 Azure AD 租户中必须存在客户。 可以为客户选择是否需要 Azure AD。 这样，应用程序的体系结构和基础结构就可以用来确定 Power BI 工作区集合要求的租户模型。

开发人员/员工操作或创建应用程序时会需要具有 AAD 用户帐户才能通过 Azure 门户管理 Azure 订阅和工作区集合。 开发人员可以使用编程 API 导入报表、修改连接字符串、获取嵌入式 URL、改用应用令牌进行身份验证，因此无需使用 AAD。

## <a name="where-can-i-learn-more"></a>可以从何处了解详细信息？

可以访问 [Power BI 工作区集合文档页](get-started.md)。 通过访问 [Power BI 博客](https://powerbi.microsoft.com/blog/)或通过访问 dev.powerbi.com 中的 Power BI 开发人员中心，可以了解该服务的最新信息。 也可以在 [Stack Overflow](https://stackoverflow.com/questions/tagged/powerbi) 上提问。

## <a name="how-do-i-get-started"></a>如何入门？

可以立即开始体验免费版！ 如果拥有 Azure 订阅，现在就可以直接从 Azure 门户预配 Power BI 工作区集合。 也可以创建[免费 Azure 帐户](https://azure.microsoft.com/free/)。 一旦 Power BI 工作区集合服务预配完毕，就可以直接轻松使用 Power BI REST API，或使用 [GitHub](https://go.microsoft.com/fwlink/?LinkID=746472) 上提供的开发人员 SDK。 关于如何使用开发人员 SDK 提供的示例。

## <a name="see-also"></a>请参阅

[什么是 Microsoft Power BI 工作区集合](what-are-power-bi-workspace-collections.md)
[Microsoft Power BI 工作区集合入门](get-started.md)
[示例入门](get-started-sample.md)   
[JavaScript 嵌入示例](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

有更多问题？ [尝试 Power BI 社区](https://community.powerbi.com/)