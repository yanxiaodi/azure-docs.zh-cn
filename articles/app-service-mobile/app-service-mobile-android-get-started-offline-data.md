---
title: 为 Azure 移动应用启用脱机同步 (Android)
description: 了解如何在 Android 应用程序中使用应用服务移动应用来缓存和同步脱机数据
documentationcenter: android
author: elamalani
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 3fe5b176d864fd4cdd1ff49d8c064495663aa3b0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443582"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>为 Android 移动应用启用脱机同步
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center 投入新和集成服务移动应用开发的核心。 开发人员可以使用**构建**，**测试**并**分发**服务来设置持续集成和交付管道。 应用程序部署后，开发人员可以监视状态和其应用程序使用的使用情况**Analytics**并**诊断**服务，并与用户使用**推送**服务。 开发人员还可以利用**身份验证**其用户进行身份验证并**数据**服务以持久保存并在云中的应用程序数据同步。 请查看[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started-offline-data)今天。
>

## <a name="overview"></a>概述
本教程介绍适用于 Android 的 Azure 移动应用的脱机同步功能。 脱机同步允许最终用户与移动应用进行交互&mdash;查看、添加或修改数据&mdash;，即使在没有网络连接时也是如此。 更改存储在本地数据库中。 设备重新联机后，这些更改会与远程后端同步。

对于首次体验 Azure 移动应用的读者，请先完成[创建 Android 应用]教程。 如果不使用下载的快速入门服务器项目，必须将数据访问扩展包添加到项目。 有关服务器扩展包的详细信息，请参阅[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

若要了解有关脱机同步功能的详细信息，请参阅主题 [Azure 移动应用中的脱机数据同步]。

## <a name="update-the-app-to-support-offline-sync"></a>更新应用以支持脱机同步
借助脱机同步，可从*同步表*读取和写入（使用 *IMobileServiceSyncTable* 接口），该表是设备上 **SQLite** 数据库的一部分。

若要在设备与 Azure 移动服务之间推送和拉取更改，可以使用*同步上下文* (*MobileServiceClient.SyncContext*)，该上下文借助本地存储数据时所用的本地数据库进行初始化。

1. 在 `TodoActivity.java` 中，注释掉 `mToDoTable` 的现有定义，取消注释同步表版本：
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. 在 `onCreate` 方法中，注释掉 `mToDoTable` 的现有初始化，取消注释以下定义：
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. 在 `refreshItemsFromTable` 中，注释掉 `results` 的定义，取消注释以下定义：
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. 注释掉 `refreshItemsFromMobileServiceTable` 的定义。
5. 取消注释 `refreshItemsFromMobileServiceTableSyncTable` 的定义：
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. 取消注释 `sync` 的定义：
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>测试应用程序
在此部分中，会在启用 WiFi 的情况下测试行为，然后关闭 WiFi 以创建脱机方案。

添加数据项时，它们保存在本地 SQLite 存储中，但直到按下“刷新”  按钮才同步到移动服务。 根据数据需要同步的时间，其他应用可能具有不同的要求，但出于演示目的，本教程让用户显式请求它。

按下该按钮时，将启动新的后台任务。 该任务先使用同步上下文推送对本地存储所做的所有更改，并将所有更改的数据从 Azure 拉取到本地表。

### <a name="offline-testing"></a>脱机测试
1. 将设备或模拟器置于*飞行模式*中。 这会创建脱机方案。
2. 添加一些 *ToDo* 项或将一些项标记为“完成”。 退出设备或模拟器（或强制关闭应用），然后重新启动。 验证所做更改是否保存在设备上，因为本地 SQLite 存储已保存这些更改。
3. 使用 SQL 工具（如 *SQL Server Management Studio*）或 REST 客户端（如 *Fiddler* 或 *Postman*）查看 Azure *TodoItem* 表的内容。 验证新项是否*未*同步到服务器
   
       + 对于 Node.js 后端，请转到 [Azure 门户](https://portal.azure.com/)，在移动应用后端中单击“简易表”   > “TodoItem”  ，查看 `TodoItem` 表的内容。
       + 对于 .NET 后端，请使用 SQL 工具（如 *SQL Server Management Studio*）或 REST 客户端（如 *Fiddler* 或 *Postman*）查看表内容。
4. 在设备或模拟器中打开 WiFi。 接下来，按“刷新”  按钮。
5. 在 Azure 门户中再次查看 TodoItem 数据。 新的和更改的 TodoItem 现在应会出现。

## <a name="additional-resources"></a>其他资源
* [Azure 移动应用中的脱机数据同步]
* [云覆盖：Azure 移动服务中的脱机同步] \(注意：此视频中是有关移动服务的内容，但是脱机同步在 Azure 移动应用中的工作原理与其类似\)

<!-- URLs. -->

[Azure 移动应用中的脱机数据同步]: app-service-mobile-offline-data-sync.md

[创建 Android 应用]: app-service-mobile-android-get-started.md

[云覆盖：Azure 移动服务中的脱机同步]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

