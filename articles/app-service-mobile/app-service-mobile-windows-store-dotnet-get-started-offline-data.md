---
title: 使用移动应用为通用 Windows 平台 (UWP) 应用启用脱机同步 | Microsoft Docs
description: 了解如何在通用 Windows 平台 (UWP) 应用中使用 Azure 移动应用缓存和同步脱机数据。
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 4970a80b911a1efbc308d48ac4b8a50f774b4d04
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "67551929"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>为 Windows 应用启用脱机同步
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center 正在为移动应用开发的新的和集成的服务进行投资。 开发人员可以使用**生成**、**测试**和**分发**服务来设置持续集成和交付管道。 部署应用后, 开发人员可以使用**分析**和**诊断**服务监视应用的状态和使用情况, 并使用**推送**服务与用户联系。 开发人员还可以利用**Auth**来验证其用户和**数据**服务, 以便在云中持久保存和同步应用程序数据。 立即查看[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started-offline-data) 。
>

## <a name="overview"></a>概述
本教程演示如何使用 Azure 移动应用后端为通用 Windows 平台 (UWP) 应用添加脱机支持。 脱机同步允许最终用户与移动应用交互（查看、添加或修改数据），即使在没有网络连接时也是如此。 更改存储在本地数据库中。 设备重新联机后，这些更改会与远程后端同步。

在本教程中，需更新[创建 Windows 应用]教程中的 UWP 应用项目，以支持 Azure 移动应用的脱机功能。 如果不使用下载的快速入门服务器项目，必须将数据访问扩展包添加到项目。 有关服务器扩展包的详细信息，请参阅[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

若要了解有关脱机同步功能的详细信息，请参阅主题 [Azure 移动应用中的脱机数据同步]。

## <a name="requirements"></a>要求  
本教程需要的先决条件如下：

* 在 Windows 8.1 或更高版本上运行的 Visual Studio 2013。
* 完成[创建 Windows 应用][创建 Windows 应用]。
* [Azure 移动服务 SQLite 应用商店][sqlite store nuget]
* [适用于通用 Windows 平台开发的 SQLite](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>更新客户端应用以支持脱机功能
脱机情况下，可使用 Azure 移动应用脱机功能与本地数据库交互。 若要在应用中使用这些功能, 可以初始化[SyncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable]接口。 SQLite 在设备上用作本地存储。

1. 安装[适用于通用 Windows 平台的 SQLite 运行时](https://sqlite.org/2016/sqlite-uwp-3120200.vsix)。
2. 在 Visual Studio 中，打开在[创建 Windows 应用]教程中完成的 UWP 应用项目的 NuGet 包管理器。
    搜索并安装 **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 包。
3. 在解决方案资源管理器中，右键单击“引用”>“添加引用...”>“通用 Windows”>“扩展”，并同时启用“适用于通用 Windows 平台的 SQLite”和“适用于通用 Windows 平台应用的 Visual C++ 2015 运行时”。

    ![添加 SQLite UWP 引用][1]
4. 打开 MainPage.xaml.cs 文件并取消注释 `#define OFFLINE_SYNC_ENABLED` 定义。
5. 在 Visual Studio 中，按 **F5** 键重新生成并运行客户端应用。 应用的工作方式与启用脱机同步之前一样。但是，本地数据库中现在填充了可以在脱机方案中使用的数据。

## <a name="update-sync"></a>更新应用以与后端断开连接
在本部分中，将断开与移动应用后端的连接，以模拟脱机情况。 添加数据项时，异常处理程序会指示该应用处于脱机模式。 在此状态下，新项将添加到本地存储，下次以连接状态运行推送时，这些新项将同步到移动应用后端。

1. 编辑共享项目中的 App.xaml.cs。 注释掉 **MobileServiceClient** 的初始化并添加使用无效移动应用 URL 的以下行：

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    还可以通过在设备上禁用 wifi 和手机网络或使用飞行模式来演示脱机行为。
2. 按 **F5** 生成并运行应用。 请注意，在应用启动时，同步刷新会失败。
3. 输入新项，并注意每次单击 **保存** 时，推送将失败，并显示 [CancelledByNetworkError]状态。 但是，新的待办事项在推送到移动应用后端之前，存在于本地存储中。  在生产应用中，如果取消显示这些异常，客户端应用的行为就像它仍连接到移动应用后端一样。
4. 关闭应用程序并重新启动它，以验证你创建的新项目是否已永久保存到本地存储中。
5. （可选）在 Visual Studio 中，打开“服务器资源管理器”。 导航到“Azure”->“SQL 数据库”中的数据库。 右键单击数据库并选择“在 SQL Server 对象资源管理器中打开”。 现在便可以浏览 SQL 数据库表及其内容。 验证后端数据库中的数据是否未更改。
6. （可选）通过 Fiddler 或 Postman 之类的 REST 工具使用 `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem` 格式的 GET 查询，查询移动后端。

## <a name="update-online-app"></a>更新应用以重新连接移动应用后端
在本部分中，会将应用重新连接到移动应用后端。 这些更改可模拟应用上的网络重新连接。

首次运行该应用程序时，`OnNavigatedTo` 事件处理程序将调用 `InitLocalStoreAsync`。 而此方法又将调用 `SyncAsync`，将本地存储与后端数据库同步。 应用会尝试在启动时同步。

1. 在共享项目中，打开 App.xaml.cs，并取消注释 `MobileServiceClient` 之前的初始化，以使用正确的移动应用 URL。
2. 按 **F5** 键重新生成并运行应用。 执行 `OnNavigatedTo` 事件处理程序时，该应用将立即使用推送和拉取操作将本地更改与 Azure 移动应用后端同步。
3. （可选）使用 SQL Server 对象资源管理器或 Fiddler 之类的 REST 工具查看更新后的数据。 请注意，数据已在 Azure 移动应用后端数据库和本地存储之间进行同步。
4. 在应用程序中，单击要在本地存储区中完成的几个项旁边的复选框。

   `UpdateCheckedTodoItem` 调用 `SyncAsync`，将每个已完成项与移动应用后端同步。 `SyncAsync` 同时调用推送和拉取操作。 但是，**每当对客户端已更改的表执行拉取操作时，始终会自动执行推送操作**。 此行为可确保本地存储中的所有表以及关系都保持一致。 此行为可能会导致意外的推送。  有关此行为的详细信息，请参阅 [Azure 移动应用中的脱机数据同步]。

## <a name="api-summary"></a>API 摘要
为了支持移动服务的脱机功能, 我们使用了[IMobileServiceSyncTable]接口, 并使用本地 SQLite 数据库初始化了[MobileServiceClient。][synccontext] 脱机时，移动应用的普通 CRUD 操作执行起来就像此应用仍处于连接状态一样，但操作针对本地存储进行。 以下方法用于将本地存储与服务器进行同步：

* [PushAsync] 由于此方法是 [IMobileServicesSyncContext] 的成员，因此对所有表进行的更改将推送到后端。 只有具有本地更改的记录将发送到服务器。
* **[PullAsync]** 从 [IMobileServiceSyncTable] 启动拉取操作。 当表中存在被跟踪的更改时，会执行隐式推送操作以确保本地存储中的所有表以及关系都保持一致。 *PushOtherTables* 参数控制在隐式推送操作中是否推送上下文中的其他表。 *Query*参数使用[\<IMobileServiceTableQuery T >][IMobileServiceTableQuery]或 OData 查询字符串来筛选返回的数据。 *queryId* 参数用于定义增量同步。有关详细信息，请参阅 [Azure 移动应用中的脱机数据同步](app-service-mobile-offline-data-sync.md#how-sync-works)。
* **[PurgeAsync]** 应用应定期调用此方法，从本地存储中清除过时数据。 需要清除尚未同步的任何更改时，请使用 *force* 参数。

有关这些概念的详细信息，请参阅 [Azure 移动应用中的脱机数据同步](app-service-mobile-offline-data-sync.md#how-sync-works)。

## <a name="more-info"></a>更多信息
以下主题提供有关移动应用的脱机同步功能的更多背景信息：

* [Azure 移动应用中的脱机数据同步]
* [Azure 移动应用 .NET SDK 如何][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Azure 移动应用中的脱机数据同步]: app-service-mobile-offline-data-sync.md
[创建 windows 应用]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
