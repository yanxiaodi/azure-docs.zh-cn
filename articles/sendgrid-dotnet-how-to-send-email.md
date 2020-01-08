---
title: 如何使用 SendGrid 电子邮件服务 (.NET) | Microsoft Docs
description: 了解如何在 Azure 上使用 SendGrid 电子邮件服务发送电子邮件。 代码示例用 C# 编写且使用 .NET API。
services: ''
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: erikre
ms.reviewer: dx@sendgrid.com
ms.openlocfilehash: c3211ba9f8a8b16ad4372c82d8e50c46f3ad6897
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876377"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>如何在 Azure 中使用 SendGrid 发送电子邮件
## <a name="overview"></a>概述
本指南演示了如何在 Azure 上使用 SendGrid 电子邮件服务执行常见编程任务。 示例采用 C\# 编写并支持 .NET Standard 1.3。 涉及的方案包括构建电子邮件、发送电子邮件、添加附件，以及启用各种邮件和跟踪设置。 有关 SendGrid 和发送电子邮件的详细信息，请参阅[后续步骤][Next steps]部分。

## <a name="what-is-the-sendgrid-email-service"></a>什么是 SendGrid 电子邮件服务？
SendGrid 是一项[基于云的电子邮件服务]，该服务提供了可靠的[事务电子邮件传递]、伸缩性、实时分析以及可用于简化自定义集成的灵活的 API。 常见的 SendGrid 用例包括：

* 自动向客户发送收据或购买确认。
* 管理用于每月向客户发送传单和促销资料的通讯组列表。
* 收集诸如已阻止的电子邮件和客户参与度等项目的实时指标。
* 转发客户查询。
* 处理传入的电子邮件。

有关详细信息, 请[https://sendgrid.com](https://sendgrid.com)访问或 SendGrid 的[ C#库 GitHub 存储库][sendgrid-csharp]。

## <a name="create-a-sendgrid-account"></a>创建 SendGrid 帐户
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>引用 SendGrid.NET 类库
[SendGrid NuGet 包](https://www.nuget.org/packages/Sendgrid)是获取 SendGrid API 和使用所有依赖项配置应用程序的最简单方法。 NuGet 是 Microsoft Visual Studio 2015 及更高版本随附的一个 Visual Studio 扩展，可让用户轻松安装和更新库与工具。

> [!NOTE]
> 如果运行的 Visual Studio 版本低于 Visual Studio 2015，并且想要安装 NuGet，那么，请访问 [https://www.nuget.org](https://www.nuget.org)，并单击“安装 NuGet”按钮  。
>
>

若要在应用程序中安装 SendGrid NuGet 包，请执行以下操作：

1. 单击“新建项目”  ，并选择“模板”。 

   ![创建新项目][create-new-project]
2. 在“解决方案资源管理器”中，右键单击“引用”，并单击“管理 NuGet 包”。   

   ![SendGrid NuGet 包][SendGrid-NuGet-package]
3. 搜索 **SendGrid**，并在结果列表中选择 **SendGrid** 项。
4. 从版本下拉列表中选择 Nuget 包的最新稳定版本，以便使用本文中演示的对象模型和 API。

   ![SendGrid 包][sendgrid-package]
5. 单击“安装”以完成安装，并关闭此对话框。 

SendGrid 的 .NET 类库名为 **SendGrid**。 其中包含以下命名空间：

* **SendGrid**，与 SendGrid 的 API 通信。
* **SendGrid.Helpers.Mail**，适用于帮助器方法，可以轻松地创建 SendGridMessage 对象，以便指定如何发送电子邮件。

在希望以编程方式访问 SendGrid 电子邮件服务的任何 C# 文件中，将以下代码命名空间声明添加到文件的顶部。

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>如何：创建电子邮件
使用 **SendGridMessage** 对象创建电子邮件。 创建邮件对象后，可以设置属性和方法，包括电子邮件发件人、电子邮件收件人以及电子邮件的主题和正文。

以下示例演示了如何创建完全填充的电子邮件对象：

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

有关 **SendGrid** 类型支持的所有属性和方法的详细信息，请参阅 GitHub 上的 [sendgrid-csharp][sendgrid-csharp]。

## <a name="how-to-send-an-email"></a>如何：发送电子邮件
创建电子邮件后，可以使用 SendGrid 的 API 发送该邮件。 或者, 你可以使用[。NET 的内置库][NET-library]。

发送电子邮件需要用户提供 SendGrid API 密钥。 如果需要有关如何配置 API 密钥的详细信息, 请访问 SendGrid 的 API 密钥[文档][documentation]。

可通过 Azure 门户存储这些凭据，方法是单击“应用程序设置”，再在“应用设置”下添加键/值对。

 ![Azure 应用设置][azure_app_settings]

 然后，可按如下所示访问这些凭据：

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

以下示例演示如何通过控制台应用程序使用 SendGrid Web API 发送电子邮件。

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>如何：使用 MailHelper 类从 ASP .NET Core API 发送电子邮件

下面的示例可用于使用 `SendGrid.Helpers.Mail` 命名空间的 `MailHelper` 类将单个电子邮件从 ASP .NET Core API 发送给多个人。 在此示例中，我们使用的是 ASP .NET Core 1.0。 

此示例中，API 密钥已存储在 `appsettings.json` 文件中，可以从 Azure 门户中重写该文件，如以上示例所示。

`appsettings.json` 文件的内容类似于：

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

首先，我们需将以下代码添加到 .NET Core API 项目的 `Startup.cs` 文件中。 这是必需的，因此我们可使用 API 控制器中的依赖项注入，从 `appsettings.json` 文件访问 `SENDGRID_API_KEY`。 将 `IConfiguration` 接口添加到下列 `ConfigureServices` 方法中后，可将其注入控制器的构造函数中。 添加了所需代码之后，`Startup.cs` 文件的内容类似于：

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

在控制器上，插入 `IConfiguration` 接口后，可以使用 `MailHelper` 类的 `CreateSingleEmailToMultipleRecipients` 方法将单个电子邮件发送到多个收件人。 该方法接受一个名为 `showAllRecipients` 的附加布尔参数。 此参数可用于控制电子邮件收件人是否能够在电子邮件标题的“收件人”部分查看其他每个电子邮件地址。 控制器的示例代码应如下所示 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>如何：添加附件
可以通过调用 **AddAttachment** 方法并概要指定要附加的文件名和 Base64 编码内容，将附件添加到邮件。 若要包括多个附件，可对想要附加的每个文件调用此方法一次，或者使用 **AddAttachments** 方法。 以下示例演示了如何将附件添加到邮件：

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>如何：使用邮件设置启用页脚、跟踪和分析
SendGrid 通过使用邮件设置和跟踪设置提供其他电子邮件功能。 可将这些设置添加到电子邮件以启用特定功能（例如点击跟踪、Google 分析、订阅跟踪等）。 有关应用的完整列表, 请参阅[设置文档][settings-documentation]。

可以使用作为 **SendGridMessage** 类的一部分实现的方法将应用应用到 **SendGrid** 电子邮件。 以下示例演示了脚注和单击跟踪筛选器：

以下示例演示了脚注和单击跟踪筛选器：

### <a name="footer-settings"></a>页脚设置
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>单击跟踪
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>如何：使用其他 SendGrid 服务
SendGrid 提供了多个 API 和 Webhook，方便用户在 Azure 应用程序中使用其他功能。 有关更多详细信息, 请参阅[SENDGRID API 参考][SendGrid API documentation]。

## <a name="next-steps"></a>后续步骤
此时，已了解 SendGrid 电子邮件服务的基础知识，请访问以下链接以了解更多信息。

* SendGrid C\# 库存储库：[sendgrid-csharp][sendgrid-csharp]
* SendGrid API 文档：<https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[基于云的电子邮件服务]: https://sendgrid.com/solutions
[事务电子邮件传递]: https://sendgrid.com/use-cases/transactional-email

