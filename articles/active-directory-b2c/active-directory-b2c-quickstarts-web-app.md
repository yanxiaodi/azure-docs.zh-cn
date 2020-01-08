---
title: 快速入门 - 使用 Azure Active Directory B2C 为 ASP.NET 应用程序设置登录
description: 运行一个使用 Azure Active Directory B2C 提供帐户登录的示例 ASP.NET Web 应用。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 446f891538f926342e1a4e3e5f5bca0cc791b27e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065808"
---
# <a name="quickstart-set-up-sign-in-for-an-aspnet-application-using-azure-active-directory-b2c"></a>快速入门：使用 Azure Active Directory B2C 为 ASP.NET 应用程序设置登录

Azure Active Directory B2C (Azure AD B2C) 提供云标识管理来保护应用程序、业务和客户。 应用程序可以使用 Azure AD B2C 通过开放式标准协议对社交帐户和企业帐户进行身份验证。 在本教程中，请使用 ASP.NET 应用程序通过社交标识提供者来登录，并调用受 Azure AD B2C 保护的 Web API。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 带有 ASP.NET 和 Web 开发  工作负荷的 [Visual Studio 2019](https://www.visualstudio.com/downloads/)。
- Facebook、Google 或 Microsoft 中的社交帐户。
- 从 GitHub [下载 zip 文件](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)或克隆示例 Web 应用程序。

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
    ```

    示例解决方案中有两个项目：

    - **TaskWebApp** - 一个用于创建和编辑任务列表的 Web 应用程序。 此 Web 应用程序使用**注册或登录**用户流来注册或登录用户。
    - **TaskService** - 一个 Web API，支持创建、读取、更新和删除任务列表的功能。 此 Web API 受 Azure AD B2C 保护，可以通过 Web 应用程序进行调用。

## <a name="run-the-application-in-visual-studio"></a>在 Visual Studio 中运行应用程序

1. 在示例应用程序项目文件夹中，打开 Visual Studio 中的 **B2C-WebAPI-DotNet.sln** 解决方案。
2. 在此快速入门中，请同时运行 **TaskWebApp** 和 **TaskService** 项目。 在解决方案资源管理器中右键单击 **B2C-WebAPI-DotNet** 解决方案，然后选择“设置启动项目”。 
3. 选择“多个启动项目”，将两个项目的“操作”更改为“启动”。   
4. 单击“确定”。 
5. 按 **F5** 调试这两个应用程序。 各应用程序分别在自己的浏览器选项卡中打开：

    - `https://localhost:44316/` - ASP.NET Web 应用程序。 在快速入门中，直接与此应用程序交互。
    - `https://localhost:44332/` - 由 ASP.NET Web 应用程序调用的 Web API。

## <a name="sign-in-using-your-account"></a>使用帐户登录

1. 单击 ASP.NET Web 应用程序中的“注册/登录”，启动工作流。 

    ![浏览器中的示例 ASP.NET Web 应用，突出显示了注册/签名链接](media/active-directory-b2c-quickstarts-web-app/web-app-sign-in.png)

    此示例支持多个注册选项，包括使用社交标识提供者，或者使用电子邮件地址来创建本地帐户。 对于本快速入门，将使用 Facebook、Google 或 Microsoft 社交标识提供者帐户。

2. Azure AD B2C 为示例 Web 应用程序的虚构公司“Fabrikam”提供了一个登录页。 要使用社交标识提供者注册，请单击要使用的标识提供者按钮。

    ![显示标识提供者的“登录”或“注册”页面按钮](media/active-directory-b2c-quickstarts-web-app/sign-in-or-sign-up-web.png)

    请使用社交帐户凭据进行身份验证（登录）并授权应用程序读取社交帐户的信息。 通过授予访问权限，应用程序可以从社交帐户检索个人资料信息，如姓名和城市。

3. 完成标识提供者的登录进程。

## <a name="edit-your-profile"></a>编辑个人资料

Azure Active Directory B2C 提供允许用户更新个人资料的功能。 示例 Web 应用对工作流使用 Azure AD B2C 编辑配置文件用户流。

1. 在应用程序菜单栏中，单击个人资料名称，并选择“编辑个人资料”以编辑创建的个人资料。 

    ![浏览器中的示例 Web 应用，突出显示了编辑配置文件链接](media/active-directory-b2c-quickstarts-web-app/edit-profile-web.png)

2. 更改“显示名称”或“城市”，然后单击“继续”以更新个人资料。   

    更改的内容显示在 Web 应用程序主页的右上部分。

## <a name="access-a-protected-api-resource"></a>访问受保护的 API 资源

1. 单击“待办事项列表”输入并修改待办事项列表项。 

2. 在“新建项”文本框中输入文本。  单击“添加”调用受 Azure AD B2C 保护的 Web API，该 API 可添加待办事项列表项。 

    ![浏览器中的示例 Web 应用，其中包含添加待办事项列表项](media/active-directory-b2c-quickstarts-web-app/add-todo-item-web.png)

    ASP.NET Web 应用程序在对受保护的 Web API 资源的请求中包括 Azure AD 访问令牌，该请求的目的是针对用户的待办事项列表项执行操作。

你已成功使用 Azure AD B2C 用户帐户对受 Azure AD B2C 保护的 Web API 进行授权调用。

## <a name="clean-up-resources"></a>清理资源

如果打算尝试其他 Azure AD B2C 快速入门或教程，可以使用 Azure AD B2C 租户。 可以在不再需要时[删除 Azure AD B2C 租户](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)。

## <a name="next-steps"></a>后续步骤

本快速入门使用示例 ASP.NET 应用程序执行了以下操作：

* 使用自定义登录页登录
* 使用社交标识提供者登录
* 创建 Azure AD B2C 租户
* 调用受 Azure AD B2C 保护的 Web API

通过创建自己的 Azure AD B2C 租户来完成入门。

> [!div class="nextstepaction"]
> [在 Azure 门户中创建 Azure Active Directory B2C 租户](tutorial-create-tenant.md)
