---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173456"
---
1. 在“解决方案”视图（或 Visual Studio 的“解决方案资源管理器”）中，右键单击“组件”文件夹，单击“获取更多组件...”，搜索“Google Cloud Messaging 客户端”组件，并将其添加到项目中     。
2. 打开 ToDoActivity.cs 项目文件，将以下 using 语句添加到类：

    ```csharp
    using Gcm.Client;
    ```

3. 在 **ToDoActivity** 类中，添加以下新代码： 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    这样便可以通过推送处理程序服务流程访问移动客户端实例。
4. 创建 **MobileServiceClient** 后，将以下代码添加到 **OnCreate** 方法：

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

**ToDoActivity** 现已准备就绪，可以添加推送通知了。
