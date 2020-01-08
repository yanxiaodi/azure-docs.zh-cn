---
title: 适用于 Xamarin iOS 应用的 Azure 应用服务移动应用入门 | Microsoft Docs
description: 按照本教程进行操作，开始使用移动应用进行 Xamarin.iOS 开发。
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 57867eeca9f29cfc3983cbdca94c830aa7a20500
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446246"
---
# <a name="create-a-xamarinios-app"></a>创建 Xamarin iOS 应用
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center 投入新和集成服务移动应用开发的核心。 开发人员可以使用**构建**，**测试**并**分发**服务来设置持续集成和交付管道。 应用程序部署后，开发人员可以监视状态和其应用程序使用的使用情况**Analytics**并**诊断**服务，并与用户使用**推送**服务。 开发人员还可以利用**身份验证**其用户进行身份验证并**数据**服务以持久保存并在云中的应用程序数据同步。 请查看[App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started)今天。
>

## <a name="overview"></a>概述
本教程说明如何使用 Azure 移动应用后端向 Xamarin.iOS 移动应用添加基于云的后端服务。  将创建一个新的移动应用后端以及一个简单的 *待办事项列表* Xamarin.iOS 应用，此应用将应用数据存储在 Azure 中。

只有在完成本教程后，才可以学习有关使用 Azure 应用服务中的移动应用功能的所有其他 Xamarin.iOS 教程。

## <a name="prerequisites"></a>必备组件
若要完成本教程，需要具备以下先决条件：

* 有效的 Azure 帐户。 如果没有帐户，可以注册 Azure 试用版并获取多达 10 个免费的移动应用，即使在试用期结束之后仍可继续使用这些应用。 有关详细信息，请参阅[Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* Visual Studio for Mac。 请参阅[设置和安装 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* 安装了 Xcode 9.0 或更高版本的 Mac。
  
## <a name="create-an-azure-mobile-app-backend"></a>创建 Azure 移动应用后端
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>创建数据库连接并配置客户端和服务器项目
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>运行 Xamarin.iOS 应用
1. 打开 Xamarin.iOS 项目。

2. 转到 [Azure 门户](https://portal.azure.com/)，并导航到已创建的移动应用。 在 `Overview` 边栏选项卡上，查找作为移动应用公共终结点的 URL。 示例 - 我的应用名称“test123”的站点名将为 https://test123.azurewebsites.net 。

3. 打开此文件夹（xamarin.iOS/ZUMOAPPNAME）中的文件 `QSTodoService.cs`。 应用程序名称为 `ZUMOAPPNAME`。

4. 在 `QSTodoService` 类中，将 `ZUMOAPPURL` 变量替换为上面的公共终结点。

    `const string applicationURL = @"ZUMOAPPURL";`

    变为
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. 按 F5 键在 iPhone 模拟器中部署并运行应用。

6. 在应用中键入有意义的文本（例如“完成教程”  ），然后单击 + 按钮。

    ![][10]

    来自请求的数据插入到 TodoItem 表。 移动应用后端返回存储在表中的项，数据显示在列表中。

   > [!NOTE]
   > 可以查看访问移动应用后端以查询和插入数据的代码，这些代码在 ToDoActivity.cs C# 文件中。
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png