---
title: 关于 Azure 应用服务中的移动应用
description: 了解应用服务为企业移动应用带来的优势。
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: yochayk
editor: ''
ms.assetid: 4e96cb9d-a632-4cf6-8219-0810d8ade3f9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: a3817b3e9f62a863f3fea9305ef169db2eb84491
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637947"
---
# <a name="getting-started"> </a>关于 Azure 应用服务中的移动应用

> [!NOTE]
> Visual Studio App Center 正在为移动应用开发的新的和集成的服务进行投资。 开发人员可以使用**生成**、**测试**和**分发**服务来设置持续集成和交付管道。 部署应用后, 开发人员可以使用**分析**和**诊断**服务监视应用的状态和使用情况, 并使用**推送**服务与用户联系。 开发人员还可以利用**Auth**来验证其用户和**数据**服务, 以便在云中持久保存和同步应用程序数据。 立即查看[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-value-prop) 。
>

Azure 应用服务是完全托管的[平台即服务](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) 产品，适用于专业开发人员。 该服务为 Web、移动和集成方案提供丰富的功能集。 

Azure 应用服务中的移动应用功能为企业开发人员和系统集成商提供高度可缩放、全局可用的移动应用程序开发平台。

![移动应用功能概览](./media/app-service-mobile-value-prop/overview.png)

## <a name="why-mobile-apps"></a>为何使用移动应用？
移动应用功能用于：

* **生成本机和跨平台应用**：无论是要生成本机 iOS、Android、Windows 应用还是跨平台 Xamarin 或 Cordova (PhoneGap) 应用，都可以通过本机 SDK 利用应用服务。
* **连接到企业系统**：使用移动应用功能，可以在短时间内添加企业登录，连接到企业本地资源或云资源。
* **生成具有数据同步功能的可脱机应用**：生成可脱机运行的应用，在与任何企业数据源或软件即服务 (SaaS) API 建立连接时，使用移动应用在后台同步数据，提高移动工作者的生产力。
* **在几秒内向数百万用户推送通知**：在任何设备上使用实时推送通知与客户联系，根据客户需求个性化推送通知并适时发送。

## <a name="mobile-apps-features"></a>移动应用功能
以下功能对于支持云的移动开发十分重要：

* **身份验证和授权**：支持标识提供者，包括适用于企业身份验证的 Azure Active Directory，以及 Facebook、Google、Twitter 和 Microsoft 帐户等社交提供程序。 移动应用可为每个提供者提供 OAuth 2.0 服务。 还可以为标识提供者集成 SDK，获取特定于提供者的功能。

    深入了解[身份验证功能]。

* **数据访问**：移动应用提供已链接到 Azure SQL 数据库或本地 SQL Server 且适合移动用途的 OData v3 数据源。 该服务可基于实体框架，因此可轻松地与其他 NoSQL 和 SQL 数据提供程序集成，包括 [Azure 表存储]、MongoDB、[Azure Cosmos DB] 和 SaaS API 提供程序（如 Office 365 和 Salesforce.com）。

* **脱机同步**：可以通过客户端 SDK 轻松地生成可靠、响应能力强且可与脱机数据集一起运行的移动应用程序。 可以让此数据集自动与后端数据（包括冲突解决方案支持）同步。

  深入了解[数据功能]。

* **推送通知**：客户端 SDK 与 Azure 通知中心的注册功能无缝集成，可将推送通知同时发送给数百万用户。

  深入了解[推送通知功能]。

* **客户端 SDK**：提供整套客户端 SDK 来全面满足本机开发（[iOS]、[Android] 和 [Windows]）、跨平台开发（[Xamarin.iOS 和 Xamarin.Android]、[Xamarin.Forms]）和混合应用程序开发 ([Apache Cordova]) 要求。 每个客户端 SDK 附带 MIT 许可证，并且是开源的。

## <a name="azure-app-service-features"></a>Azure 应用服务功能
以下平台功能可用于移动生产站点：

* **自动缩放**：使用应用服务可以快速地进行纵向或横向扩展，以处理任何传入的客户负载。 手动选择 VM 的数量和大小，或者设置自动缩放，根据负载或计划来缩放移动应用后端。

  深入了解[自动缩放]。

* **过渡环境**：应用服务可以运行多个版本的站点，用于执行 A/B 测试，在较大型 DevOps 计划中进行生产测试，以及对新后端执行现场过渡。

  深入了解 [过渡环境]。

* **持续部署**：应用服务可与常用_源代码控制管理_ (SCM) 系统集成，方便你轻松部署新版后端。

  深入了解 [部署选项](../app-service/deploy-local-git.md)。

* **虚拟网络**：应用服务可以使用虚拟网络、Azure ExpressRoute 或混合连接与本地资源建立连接。

  深入了解[混合连接]、[虚拟网络]和 [ExpressRoute]。

* **隔离的专用环境**：可以在一个完全隔离的专用环境中运行应用服务，以便安全运行 Azure 应用服务应用。 此环境适用于需要的规模大且必须进行隔离或安全网络访问的应用程序工作负荷。

  深入了解[应用服务环境]。

## <a name="next-steps"></a>后续步骤

若要开始在 Azure 应用服务中使用移动应用，请完成[入门]教程。 该教程介绍生成移动后端和所选客户端的基础知识， 并介绍集成身份验证、脱机同步和推送通知。 可以多次完成该教程，每个客户端应用程序一次。

有关移动应用的详细信息，请查看[学习路线图]。
有关 Azure 应用服务平台的详细信息，请参阅 [Azure 应用服务]。

<!-- URLs. -->
[Migrate your mobile service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[入门]: app-service-mobile-ios-get-started.md
[Azure 表存储]:../cosmos-db/table-storage-how-to-use-dotnet.md
[Azure Cosmos DB]: ../cosmos-db/sql-api-get-started.md
[身份验证功能]: ./app-service-mobile-auth.md
[数据功能]: ./app-service-mobile-offline-data-sync.md
[推送通知功能]: ../notification-hubs/notification-hubs-push-notification-overview.md
[iOS]: ./app-service-mobile-ios-how-to-use-client-library.md
[Android]: ./app-service-mobile-android-how-to-use-client-library.md
[Windows]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.iOS 和 Xamarin.Android]: ./app-service-mobile-dotnet-how-to-use-client-library.md
[Xamarin.Forms]: ./app-service-mobile-xamarin-forms-get-started.md
[Apache Cordova]: ./app-service-mobile-cordova-how-to-use-client-library.md
[自动缩放]: ../app-service/manage-scale-up.md
[过渡环境]: ../app-service/deploy-staging-slots.md
[混合连接]: ../biztalk-services/integration-hybrid-connection-overview.md
[虚拟网络]: ../app-service/web-sites-integrate-with-vnet.md
[ExpressRoute]: ../app-service/environment/app-service-app-service-environment-network-configuration-expressroute.md
[应用服务环境]: ../app-service/environment/intro.md
[学习路线图]: https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/
[Azure 应用服务]: ../app-service/overview.md
