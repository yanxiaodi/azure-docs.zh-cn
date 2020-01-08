---
title: 为 Azure 移动应用启用脱机同步 (Xamarin.Forms) | Microsoft Docs
description: 了解如何在 Xamarin.Forms 应用程序中使用应用服务移动应用缓存和同步脱机数据
documentationcenter: xamarin
author: elamalani
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 53f339d5450965c992f6528ff294e0d37ec2f7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446282"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>为 Xamarin.Forms 移动应用启用脱机同步
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center 投入新和集成服务移动应用开发的核心。 开发人员可以使用**构建**，**测试**并**分发**服务来设置持续集成和交付管道。 应用程序部署后，开发人员可以监视状态和其应用程序使用的使用情况**Analytics**并**诊断**服务，并与用户使用**推送**服务。 开发人员还可以利用**身份验证**其用户进行身份验证并**数据**服务以持久保存并在云中的应用程序数据同步。 请查看[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started-offline-data)今天。
>

## <a name="overview"></a>概述
本教程介绍适用于 Xamarin.Forms 的 Azure 移动应用的脱机同步功能。 脱机同步允许最终用户与移动应用交互（查看、添加或修改数据），即使在没有网络连接时也是如此。 更改存储在本地数据库中。 设备重新联机后，这些更改会与远程服务同步。

本教程基于在完成 [创建 Xamarin iOS 应用] 教程时创建的移动应用的 Xamarin.Forms 快速入门解决方案。 Xamarin.Forms 的快速入门解决方案包含用于支持脱机同步的代码，只需启用即可使用。 在本教程中，需更新快速入门解决方案，以打开 Azure 移动应用的脱机功能。 还将重点介绍该应用中的脱机特定代码。 如果不使用下载的快速入门解决方案，必须将数据访问扩展包添加到项目。 有关服务器扩展包的详细信息，请参阅[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK][1]。

若要了解有关脱机同步功能的详细信息，请参阅主题 [Azure 移动应用中的脱机数据同步][2]。

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>启用快速入门解决方案中的脱机同步功能
脱机同步代码使用 C# 预处理器指令，包含在项目中。 定义 **OFFLINE\_SYNC\_ENABLED** 符号时，这些代码路径包含在生成中。 对于 Windows 应用，还必须安装 SQLite 平台。

1. 在 Visual Studio 中，右键单击解决方案 >“管理解决方案的 NuGet 包…”  ，并在解决方案的所有项目中搜索并安装 **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 包。
2. 在解决方案资源管理器中，从名称中包含 **Portable** 的项目（该项目是可移植类库项目）中打开 TodoItemManager.cs 文件，并取消注释以下预处理器指令：

        #define OFFLINE_SYNC_ENABLED
3. （可选）若要支持 Windows 设备，请安装以下 SQLite 运行时包之一：

   * **Windows 8.1 运行时：** 安装[适用于 Windows 8.1 的 SQLite][3]。
   * **Windows Phone 8.1：** 安装[适用于 Windows Phone 8.1 的 SQLite][4]。
   * **通用 Windows 平台**安装[适用于通用 Windows 世界的 SQLite][5]。

     尽管快速入门中不包含通用 Windows 项目，但通用 Windows 平台支持 Xamarin Forms。
4. （可选）在每个 Windows 应用项目中，右键单击“引用”   > “添加引用...”  ，展开 **Windows** 文件夹 >“扩展”  。
    启用合适的 **SQLite for Windows** SDK 以及 **Visual C++ 2013 Runtime for Windows** SDK。
    每个 Windows 平台的 SQLite SDK 名称略有不同。

## <a name="review-the-client-sync-code"></a>查看客户端同步代码
下面简要概述了在教程代码的 `#if OFFLINE_SYNC_ENABLED` 指令中已包含的内容。 脱机同步功能在可移植类库项目的 TodoItemManager.cs 项目文件中。 有关功能的概念性概述，请参阅 [Azure 移动应用中的脱机数据同步][2]。

* 表操作之前，必须初始化本地存储区。 在 **TodoItemManager** 类构造函数中使用以下代码初始化本地存储数据库：

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    此代码使用 **MobileServiceSQLiteStore** 类创建一个新的本地 SQLite 数据库。

    **DefineTable** 方法在本地存储中创建一个与所提供类型的字段匹配的表。  该类型无需包括远程数据库中的所有列。 可以存储列的子集。
* **TodoItemManager** 中的 **todoTable** 字段是 **IMobileServiceSyncTable** 类型，而不是 **IMobileServiceTable** 类型。 此类使用本地数据库进行所有创建、读取、更新和删除 (CRUD) 表操作。 决定何时通过对 **IMobileServiceSyncContext** 调用 **PushAsync** 将这些更改推送到移动应用后端。 对于调用 **PushAsync** 时客户端应用修改的所有表，该同步上下文通过跟踪和推送这些表中的更改来帮助保持表关系。

    将调用以下 **SyncAsync** 方法来与移动应用后端进行同步：

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    此示例使用默认同步处理程序的简单错误处理。 实际的应用程序使用自定义的 **IMobileServiceSyncHandler** 实现处理各种错误，如网络状况和服务器冲突。

## <a name="offline-sync-considerations"></a>脱机同步注意事项
在此示例中，仅在启动时和请求同步时才调用 **SyncAsync** 方法。  若要在 Android 或 iOS 应用中启动同步，请下拉项目列表；对于 Windows，请使用“同步”  按钮。 在实际应用程序中，还可以在网络状态发生更改时触发同步。

对具有由上下文跟踪的未完成本地更新的表执行拉取操作时，该拉取操作会自动触发在前面执行的上下文推送操作。 在此示例中刷新、添加和完成项目时，可省略显式 **PushAsync** 调用。

在所提供的代码中，查询远程 TodoItem 表中的所有记录，但它还可以筛选记录，只需将查询 ID 和查询传递给 **PushAsync** 即可。 有关详细信息，请参阅 [Azure 移动应用中的脱机数据同步][2]中的增量同步  部分。

## <a name="run-the-client-app"></a>运行客户端应用
现已启用脱机同步，可在每个平台上至少运行一次客户端应用程序，以填充本地存储数据库。 然后模拟脱机情况，并在应用处于脱机状态时修改本地存储中的数据。

## <a name="update-the-sync-behavior-of-the-client-app"></a>更新客户端应用的同步行为
在本部分中，修改客户端项目，通过对后端使用无效的应用程序 URL 来模拟脱机情况。 或者可以通过将设备切换到“飞行模式”来关闭网络连接。  添加或更改数据项时，这些更改将保存在本地存储中，但在重新建立连接之前，不会同步到后端数据存储中。

1. 在解决方案资源管理器中，从 **Portable** 项目打开 Constants.cs 项目文件，更改 `ApplicationURL` 的值使其指向无效的 URL：

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. 从 **Portable** 项目打开 TodoItemManager.cs 文件，并在 **SyncAsync** 的 **try...catch** 块中为 **Exception** 基类添加一个 **catch**。 此 **catch** 块会将异常消息写入控制台，如下所示：

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. 生成并运行客户端应用。  添加一些新项。 请注意每次尝试与后端同步时，都会在控制台中记录异常。 这些新项目在推送到移动后端之前，只存在于本地存储中。 客户端应用的行为就像它已连接到支持所有创建、读取、更新、删除 (CRUD) 操作的后端一样。
4. 关闭应用程序并重新启动它，以验证你创建的新项目是否已永久保存到本地存储中。
5. （可选）使用 Visual Studio 查看 Azure SQL 数据库表，以了解后端数据库中的数据是否未更改。

    在 Visual Studio 中，打开“服务器资源管理器”  。 导航到“Azure”  ->“SQL 数据库”  中的数据库。 右键单击数据库并选择“在 SQL Server 对象资源管理器中打开”  。 现在便可以浏览 SQL 数据库表及其内容。

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>更新客户端应用以重新连接移动后端
在本部分中，将应用重新连接到移动后端，以模拟重新回到联机状态的应用。 执行刷新手势时，数据同步到移动后端。

1. 重新打开 Constants.cs。 更正 `applicationURL`，使其指向正确的 URL。
2. 重新生成并运行客户端应用。 该应用在启动后将尝试与移动应用后端进行同步。 验证调试控制台中是否未记录任何异常。
3. （可选）查看更新后的数据使用 SQL Server 对象资源管理器或 Fiddler 之类的 REST 工具或[Postman][6]。 请注意，数据已在后端数据库和本地存储之间进行同步。

    请注意，数据已在数据库和本地存储之间进行同步，并包含在应用断开连接时添加的项目。

## <a name="additional-resources"></a>其他资源
* [Azure 移动应用中的脱机数据同步][2]
* [Azure 移动应用.NET SDK 操作方法][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
